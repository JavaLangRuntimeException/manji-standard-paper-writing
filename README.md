# Manji Standard Paper Writing (MSPW)

国際学会のFull Paper執筆を支援するClaude Code Skill群。
章ごとに分かれており、それぞれ独立に、または `mspw-writing-principles` と組み合わせて使えます。

## セットアップ（Overleafから clone した論文で使う場合）

### 想定ワークフロー

1. Overleaf の Git integration で論文プロジェクトをローカルに `git clone`
2. そのプロジェクトの `.claude/` 配下にこの MSPW toolkit を配置
3. 論文プロジェクトのルートで Claude Code を起動すると skill / subagent が自動的に有効になる

### 配置後のディレクトリ構造

```
my-paper/                          # Overleaf から clone した論文プロジェクト
├── paper.tex                      # 論文本体
├── figures/                       # 図ファイル
├── references.bib
├── CLAUDE.md                      # 本研究固有のコンテキスト（下部のテンプレ参照）
└── .claude/
    ├── skills/                    # ← MSPW の skill をここに配置
    │   ├── mspw-abstract/SKILL.md
    │   ├── mspw-introduction/SKILL.md
    │   ├── mspw-related-work/SKILL.md
    │   ├── mspw-method/SKILL.md
    │   ├── mspw-user-study/SKILL.md
    │   ├── mspw-results/SKILL.md
    │   ├── mspw-discussion/SKILL.md
    │   ├── mspw-design-implications/SKILL.md
    │   ├── mspw-limitations/SKILL.md
    │   ├── mspw-conclusion/SKILL.md
    │   └── mspw-writing-principles/SKILL.md
    └── agents/                    # ← MSPW の subagent をここに配置
        ├── mspw-abstract.md
        ├── mspw-introduction.md
        └── ... (全11個)
```

### 配置方法

#### Option A: `git clone` して symlink（推奨）

```sh
cd my-paper                                                   # Overleaf clone 後の論文dir
mkdir -p .claude
git clone <this-repo-url> .claude/mspw                        # toolkit を sub-dir に配置
ln -s mspw/mspw-abstract            .claude/skills/mspw-abstract
ln -s mspw/mspw-introduction        .claude/skills/mspw-introduction
# ... 他の skill も同様に symlink
ln -s ../mspw/.claude/agents/mspw-abstract.md  .claude/agents/mspw-abstract.md
# ... 他の agent も同様に symlink
```

将来 MSPW 側が更新されたら `git pull .claude/mspw` で反映できます。

#### Option B: Git submodule として追加

```sh
cd my-paper
git submodule add <this-repo-url> .claude/mspw
```

論文リポジトリに MSPW のバージョンを固定できます。共同執筆時に全員が同じルールで動く保証が欲しい場合はこちら。

#### Option C: 直接コピー

MSPW の更新を追わないなら単純にコピーでも可：

```sh
cp -r manji-standard-paper-writing/mspw-*        my-paper/.claude/skills/
cp -r manji-standard-paper-writing/.claude/agents my-paper/.claude/
```

### Overleaf 側への影響

- `.claude/` は Overleaf にはpushしない。`.gitignore` に `.claude/` を追加しておく
- もしくは Overleaf Git の remote とは別の personal fork を持ち、そちらで MSPW を管理する

```sh
# my-paper/.gitignore に追加
.claude/
```

### 動作確認

Claude Code を `my-paper/` で起動し、Abstractの草稿を貼って「レビューして」と伝えると、
`mspw-abstract` skill が自動起動します。subagent も `use the mspw-abstract agent to review …` のように呼び出せます。

## Skill一覧

| Skill | 対応章 |
|-------|-------|
| `mspw-abstract` | Abstract |
| `mspw-introduction` | Introduction |
| `mspw-related-work` | Related Work |
| `mspw-method` | System / Method / Experimental Setup |
| `mspw-user-study` | User Study / Evaluation |
| `mspw-results` | Results |
| `mspw-discussion` | Discussion |
| `mspw-design-implications` | Design Implications |
| `mspw-limitations` | Limitations and Future Directions |
| `mspw-conclusion` | Conclusion |
| `mspw-writing-principles` | **横断的な英文執筆原則（必ず併用）** |

> ### ⚠️ 重要: `mspw-writing-principles` は常に併用する
>
> **どの章のSkill（`mspw-abstract` / `mspw-introduction` / ... ）を使う場合でも、
> `mspw-writing-principles` を必ず同時に参照してください。**
>
> 章別Skillは「その章の構造・必須要素」を扱い、`mspw-writing-principles` は
> 「英文スタイル・引用の正確さ・曖昧な最上級・時制統一・投稿前mechanical passなど、
> 全章に横断する執筆原則」を扱います。章別Skillだけでは横断原則が漏れるため、
> 片方だけを使うことは想定していません。
>
> **具体的な呼び出し方:**
> - 章を書くとき: 「Abstractを書きたい。`mspw-abstract` と `mspw-writing-principles` を適用して」
> - 章をレビューするとき: Subagentを使う場合も、`mspw-writing-principles` を
>   主subagentと並列に、または後段で必ず走らせる
> - 最終チェック: 章別Skill完了後に `mspw-writing-principles` のチェックリストを
>   必ず通す（壊れた参照、anonymization、比較級の比較対象、時制など）

## 使い方

Claude Codeで該当する章を書く/直すときに「Abstractを書きたい」「関連研究をブラッシュアップして」のように
依頼するとSkillが自動的にトリガーされます。明示的に `@mspw-abstract` のように指定することも可能です。

**章別Skill ＋ `mspw-writing-principles` の2本立てが基本**です。章別Skillだけを使っていると、
曖昧な最上級・引用の誤解釈・壊れた相互参照など、横断的な落とし穴を見逃します。

## 設計思想

- **章ごとに独立**: 各Skillは自章のことだけを扱い、他章には踏み込まない
- **Before/After例**: やってはいけない書き方と、推奨する書き方をペアで提示
- **トリガー明確化**: descriptionは「〜のときに使う」と具体的に書きunder-triggerを防ぐ
- **横断原則の分離**: 英文スタイル・引用解釈など章をまたぐルールは `mspw-writing-principles` に集約

## Subagent

各Skillに対応するsubagentが `.claude/agents/` に同じ名前で配置されています
（`.claude/agents/mspw-abstract.md` など）。Skillが「どう書くかのガイドライン」を
コンテキストとして提供するのに対し、Subagentは独立したコンテキストで章の
レビュー・執筆をまるごと任せたいときに使います。

Claude Codeでは `> use the mspw-abstract agent to review this abstract` の
ように指示するとsubagentが自動的に呼び出されます。大量の原稿を章ごとに
並列レビューしたい場合にも有効です（複数subagentを同時起動できます）。

Skillとsubagentは同じルール体系に基づくため、どちらを使っても同じ指針で
レビュー・執筆がされます。

**使い分け**:
- **Skill**: 自分で書きながら、ルールを参照したい
- **Subagent**: 章をまるごとレビューしてもらいたい、並列に複数章をレビューしたい

## CLAUDE.md の書き方（推奨）

Skill / Subagent は「どう書くか（How）」を知っているが、「**何の研究で、何を主張したいか（What）**」は
各プロジェクト固有の情報です。これはプロジェクトルートの `CLAUDE.md` に書いておくと、
Claude Codeが全セッションで自動的に参照してくれるため、毎回説明する必要がなくなります。

### 最低限書いてほしい項目

1. **研究の一言要約**: 何の領域で、何を明らかにしようとしているか
2. **投稿先**: 学会名（CHI, UIST, IEEE VR など）と締切
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
- <現象> は「XXX」と呼ぶ（「YYY」とは書かない）
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

- **Skill/Subagent は How を知っている** が、**What は知らない**。`CLAUDE.md` で What を渡すと、
  例えば `mspw-abstract` が Contribution 候補を知った上で 5部構成に整形してくれる。
- **整合性チェックが自動化される**: Abstract と Intro の contribution が一致しているかを
  subagent が `CLAUDE.md` の Contribution 候補と照合できる。
- **毎回の説明が不要**: 「私の研究は〜」という前置きを毎セッション書かなくて済む。

### 運用のコツ

- 研究が進んで仮説や contribution が変わったら `CLAUDE.md` を更新する（論文本文より先に）
- 締切や文字数制限は頻繁に参照されるので先頭に置く
- 執筆ステータスを書いておくと「次はどこを手伝うか」をClaudeが推測しやすい
