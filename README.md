# Manji Standard Paper Writing (MSPW)
Full Paper執筆を支援する Claude Code 環境。
役割分離されたサブエージェントチーム(章を書く・調べる・レビューする)と、それを**オーケストレートする 2 つの skill**、章ごとの執筆ナレッジ skill で構成されます。

## 全体像

```
ユーザー
  │  /paper-orchestrator       … 執筆・修正・レビュー反映のパイプライン(論文全体)
  │  /paper-spec-orchestrator  … 変更の設計書(spec)づくり → docs/spec/
  │  <章名>-orchestrator        … 単章のミニパイプライン(「Abstractを直して」等で自動トリガー)
  ▼
オーケストレーター(メイン会話で動く skill)= subagent をオーケストレートする
  ── 確認が必要な点だけをまとめてユーザーに質問(AskUserQuestion)
  ── すべてのエージェント間通信を中継(サブエージェント同士は直接会話できない)
  │
  ├─ abstract 〜 conclusion   : 章専属ライター subagent 10 種(章ナレッジ持ち。レビューしない)
  ├─ paper-researcher         : 調べる人(書かない・レビューしない・記憶で断言しない)
  ├─ paper-reviewer1          : 1 次レビュー(書かない)
  ├─ paper-reviewer2          : 独立 2 次レビュー + 1 次レビューの妥当性判定(書かない)
  ├─ writing-principles       : 横断原則チェッカー・mechanical pass(書かない)
  └─ paper-spec-writer        : docs/spec/ の設計書だけを書く
```

### 役割分離の原則

- **書く人はレビューしない。レビューする人は書かない。調べる人はどちらもしない**(各 agent の `tools:` でも編集ツールを制限してツールレベルで担保)
- わからないこと・断定できないことは推測で埋めず、原稿に `[OQ-n: 質問]` マーカーを残して open question としてオーケストレーターに返す
- 外部知識(引用文献の実際の主張など)の裏取りは paper-researcher だけが行う。レビュアーもライターも記憶で断言しない
- 研究上の判断(主張の強さ、contribution の取捨、構成変更)はユーザーに返す

## ディレクトリ構造

```
.claude/
├── skills/
│   ├── paper-orchestrator/SKILL.md       # 執筆パイプライン管理(メイン入口)
│   ├── paper-spec-orchestrator/          # 変更設計書(spec)管理
│   │   ├── SKILL.md
│   │   └── spec-template.md
│   ├── abstract-orchestrator/SKILL.md    # 章オーケストレーター skill(11 種)
│   ├── introduction-orchestrator/        #   = 章ナレッジ + 対応 subagent への振り分け
│   ├── related-work-orchestrator/
│   ├── method-orchestrator/
│   ├── user-study-orchestrator/
│   ├── results-orchestrator/
│   ├── discussion-orchestrator/
│   ├── design-implications-orchestrator/
│   ├── limitations-orchestrator/
│   ├── conclusion-orchestrator/
│   └── writing-principles-orchestrator/  # 横断原則(全章共通)
└── agents/
    ├── abstract.md                       # 章専属ライター subagent(10 種)
    ├── introduction.md
    ├── related-work.md
    ├── method.md
    ├── user-study.md
    ├── results.md
    ├── discussion.md
    ├── design-implications.md
    ├── limitations.md
    ├── conclusion.md
    ├── paper-researcher.md               # 役割 subagent(4 種)
    ├── paper-reviewer1.md
    ├── paper-reviewer2.md
    ├── paper-spec-writer.md
    └── writing-principles.md             # 横断原則チェッカー(レビュー専用)
```

**ナレッジは skill に一本化**: 章専属 subagent は起動時に対応する
`.claude/skills/<章名>-orchestrator/SKILL.md` と `writing-principles-orchestrator/SKILL.md` を読み込んでから書きます。
ナレッジを更新するときは SKILL.md だけ直せばよく、agent 側の修正は不要です。

---

## Skills

### `/paper-orchestrator` — 執筆パイプライン管理

論文執筆のメイン入口。**自分では書かない・レビューしない・調べない**管理者で、下の subagent チームをオーケストレートします。

- **やること**: 計画 → 執筆(独立した章は並列 dispatch) → open question の調査 routing → 2 段レビュー → 修正反映 → mechanical pass → 完了報告
- **振り分けルール**: 執筆はすべて章専属ライター subagent へ。章横断の修正は章ごとに分割して並列 dispatch。専属エージェントのない箇所(Acknowledgments 等)は最も関連の深い章のライターに「触ってよい範囲」を明示して任せる(汎用ライターは置かない)
- **ユーザーとの関わり**: 研究上の判断(主張の強さ、contribution の取捨、構成変更、レビュー所見の不一致)だけを、フェーズ境界でまとめて質問してくる。文献で解決できる事実は paper-researcher に回すので聞いてこない
- **品質管理**: subagent の返答後に `git diff --stat` でスコープ逸脱を検査。コンパイル確認も自分で実行。open question は台帳(`[OQ-n]` マーカー)で追跡し、grep で 0 件になるまで畳む
- **使い方**:
  ```
  > /paper-orchestrator Introduction と Related Work を書いて
  > /paper-orchestrator 査読コメント(添付)を反映して
  > /paper-orchestrator docs/spec/0002 を実装して
  ```

### `/paper-spec-orchestrator` — 変更設計書(spec)管理

大きな変更を原稿に入れる**前に**、変更内容を `docs/spec/NNNN-<slug>.md` の設計書として確定させる管理者。原稿にも spec にも自分では書きません(spec 本文は `paper-spec-writer`、裏取りは `paper-researcher` に委譲)。

- **対象**: 章構成の変更、主張・contribution の変更、実験/分析の追加記述、メジャーリバイズ、査読対応方針。誤字修正レベルには使わない
- **spec の中身**: 現状(file:line 付き) / 章・段落単位の Before→After 差分 / 他章への波及(Abstract↔Intro 整合・用語・相互参照) / リサーチ結果(出典付き) / open questions(解決経路付き) / 受け入れチェックリスト
- **ライフサイクル**: `draft` →(ユーザー承認)→ `approved` →(/paper-orchestrator が実装)→ `implemented`
- **使い方**:
  ```
  > /paper-spec-orchestrator 実験 2 を追加する構成変更の spec を作って
  > /paper-spec-orchestrator リバイズ方針をまとめて
  ```

### 章オーケストレーター skill(11 種): `<章名>-orchestrator`

各章の「必須構造・アンチパターン・Before/After 例・チェックリスト」を持つ知識ベースであり、単体の章オーケストレーターでもあります。
「Abstract をレビューして」のような依頼で自動トリガーされると、メイン会話では書かずに対応する章専属 subagent(+ paper-researcher / paper-reviewer1/2)を dispatch します。ナレッジ本体は subagent が起動時に読み込む規範です。

| Skill | 対応章 | ナレッジの要点 |
|-------|--------|---------------|
| `abstract-orchestrator` | Abstract | 5 部構成(問題提起→技術的アプローチ→評価概要→Contributions→意義)。User study の詳細(N・条件数)を冒頭に置かない。Contributions は境界が読める形で「結果/発見」として書く |
| `introduction-orchestrator` | Introduction | Heilmeier の 4 つの問いに対応する 4 段落+末尾は番号付き Contributions 箇条書き(RQ で終わらせない)。Abstract と数・順序を一致させる |
| `related-work-orchestrator` | Related Work | 冒頭 headline 段落で「何の観点でまとめるか」を宣言。subsection タイトルと中身を一致させる。citation dump 禁止。節末でギャップを明確化し本研究へつなぐ |
| `method-orchestrator` | Method / System | 設計選択を Requirements→Candidates→Decision で正当化。パラメータ値に pilot/先行研究の根拠。「novel な手法」でなく「established components の実験プラットフォーム」と位置づける。ハード型番は User Study 章へ |
| `user-study-orchestrator` | User Study / Evaluation | Participants / Design+Hypotheses / Measures / Procedure / Analysis の 5 構成。仮説は引用付き根拠+方向明示の比較形。条件名は省略形で統一。時系列 Procedure+倫理審査 |
| `results-orchestrator` | Results | 事実のみ(解釈は Discussion へ)。従属変数ごとに段落、図表参照→主効果→交互作用→post-hoc の順。全仮説に supported/partially/not supported を明示。効果量併記、比較級は "than X" 必須 |
| `discussion-orchestrator` | Discussion | Results の再掲でなくメカニズム説明。引用動詞(consistent with / contrasts with / extends)は文献の実際の主張を確認して選ぶ。交互作用なしも独立した発見として議論 |
| `design-implications-orchestrator` | Design Implications | 事実→推奨→トレードオフ→緩和策(引用付き)のセット。曖昧な最上級禁止。専門用語は初出で機能説明。応用文脈別(教育/エンタメ/訓練/長時間)の推奨 |
| `limitations-orchestrator` | Limitations / Future Directions | 太字ラベル付き 3-5 個。事実→許容理由→未解決の問い→field への意義の構造。実験結果から浮上した limitation を最低 1 つ。隠蔽も自虐もしない |
| `conclusion-orchestrator` | Conclusion | 2-4 段落(問題と手法→仮説対応の発見→新規知見→意義)。新情報・future work・内部ラベル(C1/H1)・未出の概念語は禁止。Abstract/Intro と整合 |
| `writing-principles-orchestrator` | **全章共通** | 16 の横断原則: 曖昧な最上級、引用の正確な解釈、専門用語の説明、主張の保持、冗長性のバランス、比較対象、attribution、時制、能動態、投稿前 mechanical pass 等 |

> **`writing-principles-orchestrator` は常に併用する**: 章別 skill は「その章の構造」を、`writing-principles-orchestrator` は「全章に効く英文原則」を扱います。subagent 経由なら起動手順に併読が組み込み済み。skill 単体で使う場合は「`abstract-orchestrator` と `writing-principles-orchestrator` を適用して」のように明示してください。

---

## Subagents

すべてのサブエージェントは独立コンテキストで動き、この会話を見られません。
オーケストレーターが毎回の dispatch に完全なコンテキスト(対象・確定事項・経緯)を含め、
各エージェントは定義済みの **Report format** で結果を返します。

### 章専属ライター(10 種): `abstract` `introduction` `related-work` `method` `user-study` `results` `discussion` `design-implications` `limitations` `conclusion` — 書く。レビューしない。創作しない

- **担当章だけ**を書く・直す。起動時に自章の SKILL.md(`<章名>-orchestrator`)+ `writing-principles-orchestrator` + `CLAUDE.md` を読み込む
- 担当章以外は原則編集禁止(例外: 作業指示書が「触ってよい範囲」として明示した付随箇所のみ)。他章との不整合に気づいたら報告の「申し送り」へ(自分で直さない)
- 事実・数値・引用文献の主張を創作しない。不明点は原稿に `[OQ-n: 質問]` を埋めて報告に列挙(オーケストレーターが paper-researcher / ユーザーへ routing)
- 各章固有の整合先を知っている(例: `abstract`↔Intro の Contributions、`method`↔User Study の独立変数、`conclusion`↔Abstract/Intro)
- 特に `user-study` と `results` は **実験事実・統計値のでっち上げを最厳禁** と定義
- 単体で呼ぶ場合: `> use the results agent to draft the Results section`

汎用ライターは置きません。章横断の修正は章ごとに分割して各専属ライターへ並列 dispatch、
専属エージェントのない箇所はオーケストレーターが最も関連の深い章のライターに範囲明示で任せます。

### `paper-researcher` — 調べる。書かない・レビューしない

- 引用文献が**実際に**何を主張しているか(効果の方向・条件・N)、主張の断定可否の裏取り、適切な文献探し、用語・尺度・統計手法の慣例確認、BibTeX 作成
- **記憶で断言しない**: すべての回答に実際に開いた出典(URL / DOI / repo 内ファイル)を付け、確度を verified / likely / unverified の 3 段階で明示。「確認できなかった」も正規の回答
- 質問者の期待に合わせない(期待と逆の事実もそのまま報告)
- ツールは読み取り+ Web のみ(Edit / Write を持たない)

### `paper-reviewer1` / `paper-reviewer2` — 見る。原稿は 1 文字も編集しない

修正文案は報告内の advisory 提案としてのみ書け、採否はオーケストレーターとライターが決めます。
外部知識(文献の中身・分野の事実)は記憶で断言せず、research request としてオーケストレーター経由で paper-researcher に回します。

- **`paper-reviewer1`(ファーストレビュー)**: ① 章構造(章 SKILL.md のチェックリスト) → ② 横断原則(grep できる項目は実際に grep) → ③ 論理(主張と根拠、Results↔仮説、Abstract↔Intro↔Conclusion 整合) → ④ 原稿内整合 の順。所見は `R1-n [must-fix / should-fix / nit / question] @ file:line + 根拠 + 提案` の形式。「ユーザー決定済み」事項は蒸し返さない
- **`paper-reviewer2`(独立セカンドレビュー + メタレビュー)**: Phase A で R1 レポートを読む**前に**「意地悪な学会査読者」として独立レビューを確定 → Phase B で R1 の各所見に verdict(agree / agree-with-changes / disagree / needs-research、忖度禁止) → Phase C で両者一致・R1 の見逃し・判断保留を統合。高重要度時はオーケストレーターが Phase A を R1 と並列の独立 dispatch に分割できる

### `writing-principles`(チェッカー) — mechanical pass

- `writing-principles-orchestrator` skill の 16 原則+チェックリストで検査する最終関門。grep できる項目(曖昧な最上級、浮いた比較級、`??`、未定義 \ref/\cite、`[OQ` 残り、anonymization)は実際に grep してヒット数を報告
- 内容(研究の主張の良し悪し)には踏み込まない — それはレビュアーの仕事

### `paper-spec-writer` — docs/spec/ だけを書く

- `docs/spec/NNNN-<slug>.md` の作成・更新のみ。原稿(.tex/.bib)・skills・agents・CLAUDE.md は読み取り専用
- 「現状」(Before)は必ず実際に読んだ原稿の file:line に基づく(記憶で書かない)。不明点は open questions 表に解決経路(researcher / user)付きで記載
- status 遷移(draft → approved → implemented)はオーケストレーターの指示時のみ

### 権限一覧

| Agent | 書く | レビュー | 調査 | tools |
|-------|:---:|:---:|:---:|-------|
| 章専属ライター(10 種) | ✅ 担当章+指示書で明示された範囲のみ | ❌ | ❌ | Read, Grep, Glob, Edit, Write |
| `paper-researcher` | ❌ | ❌ | ✅ Web + repo | Read, Grep, Glob, WebSearch, WebFetch |
| `paper-reviewer1` | ❌ | ✅ | ✅ repo 内 | Read, Grep, Glob |
| `paper-reviewer2` | ❌ | ✅ + R1 判定 | ✅ repo 内 | Read, Grep, Glob |
| `writing-principles` | ❌ | ✅ 原則適合のみ | ✅ repo 内 | Read, Grep, Glob |
| `paper-spec-writer` | ✅ docs/spec/ のみ | ❌ | ❌ | Read, Grep, Glob, Write, Edit |

---

## セットアップ(Overleaf から clone した論文で使う場合)

### 想定ワークフロー

1. Overleaf の Git integration で論文プロジェクトをローカルに `git clone`
2. そのプロジェクトに本 toolkit の `.claude/` を配置
3. 論文プロジェクトのルートで Claude Code を起動すると skill / subagent が自動的に有効になる

### Option A: `git clone` して symlink(推奨)

```sh
cd my-paper                                # Overleaf clone 後の論文 dir
mkdir -p .claude/skills .claude/agents
git clone <this-repo-url> .claude/mspw     # toolkit を sub-dir に配置
for d in .claude/mspw/.claude/skills/*/; do
  ln -s "../mspw/.claude/skills/$(basename "$d")" ".claude/skills/$(basename "$d")"
done
for f in .claude/mspw/.claude/agents/*.md; do
  ln -s "../mspw/.claude/agents/$(basename "$f")" ".claude/agents/$(basename "$f")"
done
```

将来 MSPW 側が更新されたら `git -C .claude/mspw pull` で反映できます。
論文プロジェクト固有の skill / agent は symlink と並べてそのまま置けます。

### Option B: Git submodule として追加

```sh
cd my-paper
git submodule add <this-repo-url> .claude/mspw
# Option A と同じ symlink ループを実行
```

論文リポジトリに MSPW のバージョンを固定できます。共同執筆で全員が同じルールで動く保証が欲しい場合はこちら。

### Option C: 直接コピー

MSPW の更新を追わないなら単純にコピーでも可:

```sh
cp -r manji-standard-paper-writing/.claude my-paper/
```

### Overleaf 側への影響

- `.claude/` は Overleaf には push しない。`.gitignore` に追加しておく
- spec(`docs/spec/`)を Overleaf に出したくない場合はそれも追加

```sh
# my-paper/.gitignore に追加
.claude/
docs/spec/   # 任意
```

### 動作確認

Claude Code を `my-paper/` で起動し、`/paper-orchestrator Abstract をレビューして直して` と伝えると、
オーケストレーターが reviewer → ライターの順で subagent を起動し、判断が要る点を質問してきます。
従来どおり「Abstract をレビューして」だけでも `abstract-orchestrator` skill が自動起動し、`abstract` subagent に作業を振ります。

## CLAUDE.md の書き方(推奨)

Skill / Subagent は「どう書くか(How)」を知っていますが、「**何の研究で、何を主張したいか(What)**」は
各プロジェクト固有の情報です。プロジェクトルートの `CLAUDE.md` に書いておくと、
オーケストレーターと全エージェントが毎回これを参照します(各エージェントの起動手順に組み込み済み)。

### 最低限書いてほしい項目

1. **研究の一言要約**: 何の領域で、何を明らかにしようとしているか
2. **投稿先**: 学会名(CHI, UIST, IEEE VR など)と締切
3. **Research Gap**: 先行研究と本研究の位置関係
4. **独立変数 × 従属変数**: ユーザー実験の設計
5. **現時点の Contribution 候補**: Abstract/Intro の [4] に書く予定のもの
6. **用語統一表**: 論文内で迷いやすい用語を先に固定しておく

### 例: `CLAUDE.md` テンプレート

```markdown
# <プロジェクト名> — 論文執筆コンテキスト

## 研究の一言要約
VR環境で <独立変数A> と <独立変数B> が <従属変数> に与える影響を調べる研究。
<A と B の組み合わせ / 境界条件> はこれまで検証されていない。

## 投稿先
- 学会: IEEE VR 2026 (Full Paper)
- 投稿締切: 2025-09-XX
- 文字数制限: 8 pages (references除く) / Abstract 250 words

## Research Gap
- 先行研究 [著者, 年] は <A 単独の効果> を示した
- 先行研究 [著者, 年] は <B 単独の効果> を示した
- しかし、<AとBの交互作用 / 境界条件> は未検証

## 実験設計
- 実験デザイン: within-subjects, 2×2 (A: 水準1/水準2) × (B: 水準1/水準2)
- 参加者: N=24 (予定), 年齢20代, VR経験者
- 独立変数: A (<A の定義>), B (<B の定義>)
- 従属変数: <DV1>, <DV2>, <DV3> (それぞれ尺度・引用)
- カウンターバランス: Latin Square

## 仮説 (現時点)
- H1: <A水準1> は <A水準2> より <DV1> が高い
- H2: <B水準1> は <B水準2> より <DV1> が高い
- H3: A と B の間に交互作用がある

## Contribution 候補
1. <A と B を統合した実験プラットフォームを構築>
2. <A と B の交互作用が <発見> であることを示した>
3. <応用領域X> への設計指針を提示

## 用語統一
- <現象> は「XXX」と呼ぶ(「YYY」とは書かない)
- 客観測定値は "performance"、主観評価は "perceived performance" と区別
- 条件は C1 (A1-B1), C2 (A1-B2), C3 (A2-B1), C4 (A2-B2) で統一

## 執筆ステータス
- [x] Abstract: v2 完成
- [ ] Introduction: 段落1〜3 まで, 段落4 (Contributions) 未着手
- [ ] Related Work: headline 段落 未着手
- [ ] Method: 実装詳細執筆中
...
```

### なぜこれが有効か

- **Skill/Subagent は How を知っている**が、**What は知らない**。`CLAUDE.md` で What を渡すと、
  例えば `abstract` agent が Contribution 候補を知った上で 5 部構成に整形できる
- **整合性チェックが自動化される**: Abstract と Intro の contribution の一致を reviewer が
  `CLAUDE.md` の Contribution 候補と照合できる
- **エージェントは会話を見られない**: 独立コンテキストで動く subagent にとって、
  `CLAUDE.md` が唯一の安定した研究コンテキスト源になる

### 運用のコツ

- 研究が進んで仮説や contribution が変わったら `CLAUDE.md` を先に更新する(論文本文より先に)
- 大きい変更は `/paper-spec-orchestrator` で spec 化してから `CLAUDE.md` → 本文の順で反映する
- 執筆ステータスを書いておくと、オーケストレーターが「次にどこを進めるか」を提案しやすい

## 設計思想

- **skill がオーケストレート、subagent が実行**: 仕事の振り分け(どの章を・誰に・どの順で)は
  オーケストレーター skill が決め、執筆・調査・レビューは独立コンテキストの subagent が担う
- **役割分離**: ライターは書く、レビュアーは見る、リサーチャーは調べる。1 エージェント 1 責務。
  原稿を書いた本人がレビューすることはパイプライン上あり得ない構造にする。
  読み取り専用の役は agent の `tools:` から編集ツールを外してツールレベルでも担保する
- **推測の禁止**: 不明点は `[OQ-n]` マーカー + open question として明示的に返す。
  「もっともらしく埋める」ことを全エージェントの最悪の失敗と定義している
- **通信の一元化**: subagent 同士は会話できない(Claude Code の仕様)。これを逆手に取り、
  すべての情報の流れをオーケストレーター経由に強制して追跡可能にする
- **ナレッジの単一ソース**: 章ナレッジは `.claude/skills/<章名>-orchestrator/SKILL.md` のみに置き、
  agent は起動時にそれを読む。skill 単体利用と agent 利用で同じルールが適用される
- **横断原則の分離**: 英文スタイル・引用解釈など章をまたぐルールは `writing-principles-orchestrator` に集約。
  どの章を書く/レビューするときも必ず併読される
- **変更は spec で先に固める**: 大きな変更は docs/spec/ に Before/After 差分として残し、
  実装・レビュー・受け入れ確認の基準にする
