---
name: results-orchestrator
description: Orchestrator skill for the Results section - delegates writing to the results subagent (this file is the rulebook the subagent loads at startup), routes open questions to paper-researcher and reviews to paper-reviewer1/2; the main conversation never writes prose itself. Use this skill whenever the user is writing, formatting, or revising the Results section of a paper. Triggers include "Results", "結果", "5章", "result section", or any context involving ANOVA tables, post-hoc tables, p-values, effect sizes, or Likert-scale boxplots. Also trigger when the user is preparing results for camera-ready submission and needs to double-check that hypotheses stated in the Study Design section are each explicitly judged as supported/partially supported/not supported. This skill enforces: (a) one paragraph per dependent measure, (b) explicit reference to each hypothesis, (c) strict separation of facts (Results) from interpretation (Discussion).
---

# Paper Results Writing

学会論文のResults章を書くときに使うSkill。

## この skill の役目(オーケストレーション)

この skill はメイン会話で動く Results 章のオーケストレーター。**メイン会話は本文を書かない**:

1. **執筆・修正** → `results` subagent に作業指示書(目的・対象範囲・確定/未確定事項)を Agent tool で dispatch する。subagent は起動時にこの SKILL.md を読み込む
2. **OQ の routing** → subagent が返す `[OQ-n]`(不明点)は `paper-researcher` へ、研究上の判断はユーザーへ(AskUserQuestion でまとめて)
3. **レビュー** → `paper-reviewer1`(高重要度なら `paper-reviewer2` も)に依頼し、所見の反映は再び `results` subagent へ
4. 複数章にまたがる作業・フルパイプラインは `/paper-orchestrator` へ

以下は本章の執筆ナレッジ(subagent が起動時に読み込む規範であり、reviewer の判定基準)。

## この章の原則

Results章は **事実のみ** を書く章。解釈は Discussion に分離する。
具体的には：

- **事実（Results）**: どのような統計的差があったか、どの条件が高かったか
- **解釈（Discussion）**: なぜそうなったか、既存研究とどう整合するか

この境界を厳密に守ることで、読者が「データ」と「著者の主張」を明確に区別できる。

## 必須の構造

```
\section{Results}

[冒頭1文]
  - 要約表・post-hoc表への参照をまず示す
  - "Table X summarizes the ANOVA results ... and Table Y reports 
     the post-hoc pairwise comparisons."

\paragraph{<従属変数1>}
  - 主効果・交互作用の F, p, ES（詳細は表に）
  - Post-hoc検定結果（どの条件がどう異なったか）
  - 図・表への参照

\paragraph{<従属変数2>}
  ...

[仮説との対応を明示する段落]
  "These results supported H1 ... However, the absence of 
   interaction effects did not support H3."

\paragraph{<従属変数3, 4, ...>}
  - 測定した全ての従属変数について paragraph を作る

\paragraph{Comments from the Participants}
  - 各条件の代表的な定性コメント
```

## 各従属変数 paragraph の書き方

### ✅ GOOD の型

```
\paragraph{<従属変数名>}
Figure~\ref{fig:results} shows the results for <従属変数> across 
the <条件数> conditions. <主効果・交互作用の要約: どの要因に有意、ES大/小>.
Post-hoc tests confirmed that <条件A> yielded significantly higher 
<従属変数> than <条件B>, and <条件C> yielded significantly higher 
scores than <条件D>.
```

### ポイント

- **図表への参照** を最初に置く
- 主効果 → 交互作用 → Post-hoc の順で書く
- 「significantly higher/lower」は必ず "than X" で比較対象を明示
- F値, p値, effect size は表に集約し、本文では繰り返さない（冗長を避ける）

## 仮説との対応

各仮説グループが終わったら、**supported / partially supported / did not support** を明示する。

### ✅ GOOD

```
"These results supported \textbf{H1} (<H1の内容を短く>) and 
\textbf{H2} (<H2の内容を短く>). However, the absence of 
interaction effects did not support \textbf{H3}."
```

```
"These results partially supported \textbf{H4}: <どの部分が支持され、どの部分が支持されなかったか>."
```

**査読者は仮説 → 結果の対応を必ずチェックする** ため、曖昧にせず断定的に書く。

## 統計表の作り方

### ANOVA summary table

必須要素：
- F値（自由度付き: F_{1,N}）
- p値（有意水準マーカー付き: */**/***）
- Effect size（η²_G, η²_p, Cohen's d など、手法に応じて）
- **正規性検定の結果に応じて使った手法を脚注で示す**（例: †parametric, ‡non-parametric）
- **有意水準の定義**を脚注で明記: *: p<.05, **: p<.01, ***: p<.001

### Post-hoc table

- Means for each factor level
- p値（補正後）
- Effect size
- 主効果が有意でなかった measure は `---` と明示的に空欄を説明

### ✅ 表の脚注の型

```
\begin{flushleft}
    \footnotesize
    †Analyzed using <手法> (ES = <型>) because the Shapiro-Wilk test 
    rejected normality. All others (no †) satisfied normality and 
    used <手法> (ES = <型>). The choice was made per measure, so 
    measures within the same questionnaire may use different tests.
    *: p<.05, **: p<.01, ***: p<.001.
\end{flushleft}
```

## Qualitative Data (インタビュー/コメント)

Semi-structured interview の結果は **条件ごと** にまとめる：

### ✅ GOOD の型

```
<条件1略称>: <その条件で多く得られた定性的知見の要約>. <特徴的な点 
についての補足>. Comments included ``<直接引用1>'' and ``<直接引用2>.''

<条件2略称>: <別の条件での知見の要約>. ...
```

ポイント：
- 条件名（省略形）で始める
- 要約 → 代表的な直接引用の順
- 引用は ``...'' 形式で（LaTeXの typographic quotes）

## ❌ Anti-patterns

### Anti-pattern 1: 結果の解釈を Results に混ぜる

```
BAD:
"<条件A> yielded higher <従属変数>. This suggests that <メカニズム> 
because <理由>."
```

→ 「This suggests...」以降は Discussion に回す。Resultsは事実のみ。

### Anti-pattern 2: 仮説との対応が曖昧

```
BAD:
"We found some interesting effects."
```

→ 必ず「supported H1」「did not support H3」等で明示。

### Anti-pattern 3: 効果量なしで p値のみ

```
BAD: "There was a significant effect (p=.02)."
```

→ effect size（η², Cohen's d, r など）を必ず併記。効果量なしだと効果の大きさが不明。

### Anti-pattern 4: 比較対象なしの比較級

```
BAD: "<条件A> produced higher <従属変数>."
GOOD: "<条件A> produced higher <従属変数> than <条件B>."
```

→ 何と比べて higher なのかを必ず明示。

### Anti-pattern 5: 一部の従属変数だけ報告

```
BAD:
(測定したはずの従属変数のうち、有意でなかったものを省略する)
```

→ 全ての従属変数について結果を報告。有意でなかった場合もその事実を述べる
（査読者はMeasures章とResults章の対応を確認する）。

## チェックリスト

- [ ] 冒頭で要約表・post-hoc表への参照があるか？
- [ ] Measures章に出てきた全ての従属変数について専用のparagraphがあるか？
- [ ] 各paragraphで主効果・交互作用・post-hocの順序で書いているか？
- [ ] すべての仮説について supported / partially supported / did not support を明示したか？
- [ ] ANOVA表に F, p, ES が入っているか？
- [ ] 正規性検定の結果に応じて使った手法が脚注で説明されているか？
- [ ] Post-hoc表で非有意の主効果が `---` 等で明示されているか？
- [ ] 「higher than X」のように比較対象が明示されているか？
- [ ] 結果の**解釈**を Discussion に分離できているか？（「This suggests…」が混ざっていないか）
- [ ] Qualitative な参加者コメントが条件ごとにまとめられているか？

## 関連Skill

- `user-study-orchestrator`: 仮説の番号と Results の supported/not supported の対応
- `discussion-orchestrator`: Resultsでは事実のみ、解釈は Discussion に分離
- `writing-principles-orchestrator`: 比較対象の明示、曖昧な最上級の回避
