---
name: related-work-orchestrator
description: Orchestrator skill for the Related Work section - delegates writing to the related-work subagent (this file is the rulebook the subagent loads at startup), routes open questions to paper-researcher and reviews to paper-reviewer1/2; the main conversation never writes prose itself. Use this skill whenever the user is writing, revising, or restructuring the Related Work (related research) section of an academic paper. Triggers include "related work", "関連研究", "先行研究", "第2章", "2章を書く", requests to reorganize subsections, or when a reviewer/advisor points out that "it's unclear what perspective the paper takes" or "subsection structure is confusing". This skill requires (a) opening each Related Work section with a headline sentence that makes the organizing perspective explicit, (b) matching subsection titles to the actual content scope (not narrower, not broader), and (c) making the research gap crystal clear at the end of the section.
---

# Paper Related Work Writing

学会論文のRelated Workを書く/直すときに使うSkill。

## この skill の役目(オーケストレーション)

この skill はメイン会話で動く Related Work 章のオーケストレーター。**メイン会話は本文を書かない**:

1. **執筆・修正** → `related-work` subagent に作業指示書(目的・対象範囲・確定/未確定事項)を Agent tool で dispatch する。subagent は起動時にこの SKILL.md を読み込む
2. **OQ の routing** → subagent が返す `[OQ-n]`(不明点)は `paper-researcher` へ、研究上の判断はユーザーへ(AskUserQuestion でまとめて)
3. **レビュー** → `paper-reviewer1`(高重要度なら `paper-reviewer2` も)に依頼し、所見の反映は再び `related-work` subagent へ
4. 複数章にまたがる作業・フルパイプラインは `/paper-orchestrator` へ

以下は本章の執筆ナレッジ(subagent が起動時に読み込む規範であり、reviewer の判定基準)。

## このSkillが解決する問題

よくある失敗：

- **読者が「この論文は何の観点でまとめているのか」分からない**
  → 例：複数のトピック（領域Aと領域B）を扱っているが、論文の焦点がどこかが不明瞭
- **節タイトルが中身と合っていない**
  → narrowすぎるタイトル（特定の狭い対象）の中にbroadな内容（領域全体）が書かれている
- **ギャップが曖昧で、本研究の位置づけが読み取れない**
  → 単なる文献列挙で終わり、「だから本研究が必要」につながらない

これらを防ぐには、**冒頭のheadline**で枠組みを宣言し、**節タイトル**を中身に合わせ、
**節末でギャップを明確化**する。

## 必須の構造

```
\section{Related Work}

[冒頭 headline 段落] (必須)
  - この節は何の観点で文献をまとめているかを1〜2文で宣言
  - 既存研究が何を独立に示してきて、何がまだ検証されていないかを示唆

\subsection{既存研究の軸1}
  - 節タイトルは中身を正確に表現（narrow すぎず broad すぎず）
  
\subsection{既存研究の軸2}

\subsection{本研究と直結する crosspoint}
  - 節の最後で「これまで〜は検証されていない」というギャップを明確にする
  - "This study addresses this gap by ..." で本研究につなぐ
```

## Headline段落の書き方（最重要）

Section冒頭に必ず1-3文のheadlineを置き、**読者に論文の焦点を早期に理解させる**。

### ✅ GOOD (headline段落の型)

```
"This section provides an overview of <領域A> and <領域B>, which 
have largely been pursued independently, leaving <それらのリンク> 
underexplored. In addition, we review how <追加の観点> affects 
<対象現象>."
```

これにより読者は：
1. この節が「領域A」と「領域B」を扱う
2. それらは独立に研究されてきた
3. 「AとBのリンク」がギャップ
4. さらに「追加の観点」も見ていく
と一瞬で把握できる。

### ❌ Anti-pattern: Headlineなしでいきなり subsection から始める

```
BAD:
\section{Related Work}
\subsection{<最初のsubsection タイトル>}
<いきなり個別文献の話が始まる>...
```

→ 読者は「この節の観点は何？」と混乱する。

## Subsectionタイトルの付け方

### 決定手順

1. そのsubsectionの **中身**（引用している論文群）を箇条書きで列挙
2. それら全体をカバーする **最も適切な** カテゴリ名を選ぶ
   - **narrowすぎ**: 中身の一部しかタイトルでカバーしていない
   - **broadすぎ**: 中身より広く、実際に扱っていないトピックまで含意
3. その後、subsection末尾で「本研究との関係」を1文で示す

### ❌ Anti-pattern: 中身とタイトルのミスマッチ

例：タイトルが「<特定対象>に関する研究」なのに、中身は「その領域の技術全般」を扱っている

→ **タイトルを中身に合わせる**（中身を削るのではなく）

## Subsection末尾のgap articulation

各subsectionの最後（特に本研究に最も近いcrosspoint subsection）で、
「ここまで〜が個別に示されてきた。しかし〜はまだ検証されていない」
という形でギャップを明確化する。

### ✅ GOOD（型）

```
"These lines of research reveal a clear gap. <既存知見Aの限界>. 
<既存知見Bの限界>. Whether <AとBの交点 / 組み合わせ / 境界条件> 
remains unexplored. This study addresses this gap by <本研究の 
具体的アプローチ>."
```

ポイント：
- "remains unexplored" / "has not been examined" 等で明示的にギャップを書く
- "This study addresses this gap by ..." で本研究が何をするかを明言
- これによりIntroの段落2（Q2: 現状とギャップ）と整合する

## 引用の扱い

### ❌ Anti-pattern: Citation dump

```
BAD:
"Prior work has examined this area [1][2][3][4][5]."
```

### ✅ GOOD: 各引用に対して本文で何を示したかを述べる

```
GOOD:
"<手法1> was designed on the principle that <原理>~\cite{A}, and 
<手法2> outperforms <比較対象> in <指標>~\cite{B}. However, <手法1> 
and <手法2> differ in <重要な違い>~\cite{C}."
```

**ルール**: 1文で3個以上の引用が並ぶ場合は要注意。各引用について「何を示したか」を本文で述べる。

## チェックリスト

- [ ] Related Work冒頭に、節の観点（framing）を示すheadline段落があるか？
- [ ] Headlineは「この論文の焦点は何か」が1-2文で分かるように書かれているか？
- [ ] 各subsectionのタイトルは中身を正しく表しているか？（狭すぎず広すぎず）
- [ ] 本研究の crosspoint subsection（多くは最後）が置かれているか？
- [ ] そのcrosspoint subsection末尾で、ギャップが "remains unexplored" のように明確化されているか？
- [ ] "This study addresses this gap by ..." 等で本研究につながっているか？
- [ ] Citation dumpになっておらず、各引用について本文で何を示したかを述べているか？
- [ ] Introで述べたギャップと、Related Workで述べたギャップが一致しているか？

## 実行時の手順

1. まず既存のRelated Workを読み、各subsectionの中身を箇条書きで抽出
2. 中身に対してタイトルが合っているか確認（合っていなければタイトルを修正提案）
3. 冒頭にheadline段落がなければ追加を提案
4. 節末のgap articulationがあるか確認、なければ追加
5. Intro §段落2と整合しているか確認（同じギャップを別の言葉で述べているか）

## 関連Skill

- `introduction-orchestrator`: 段落2（Q2: 現状とギャップ）と Related Work のギャップ記述は整合させる
- `writing-principles-orchestrator`: 引用の正確な解釈
