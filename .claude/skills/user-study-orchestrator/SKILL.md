---
name: user-study-orchestrator
description: Orchestrator skill for the User Study section - delegates writing to the user-study subagent (this file is the rulebook the subagent loads at startup), routes open questions to paper-researcher and reviews to paper-reviewer1/2; the main conversation never writes prose itself. Use this skill whenever the user is writing, revising, or checking the User Study, Experiment, Evaluation, or Participant section of a paper. Triggers include "user study", "実験", "被験者実験", "evaluation", "experiment", "participants", "仮説", "hypothesis", "study design", "procedure", "measures", or "第4章". Also trigger when the user mentions standardized questionnaires, within-subjects, between-subjects, counterbalancing, or Latin Square. This skill enforces the standard HCI user-study structure (Participants / Study Design + Hypotheses / Measures / Procedure / Analysis) and requires explicit grounding of each hypothesis in prior work.
---

# Paper User Study Writing

学会論文のUser Study章を書くときに使うSkill。

## この skill の役目(オーケストレーション)

この skill はメイン会話で動く User Study 章のオーケストレーター。**メイン会話は本文を書かない**:

1. **執筆・修正** → `user-study` subagent に作業指示書(目的・対象範囲・確定/未確定事項)を Agent tool で dispatch する。subagent は起動時にこの SKILL.md を読み込む
2. **OQ の routing** → subagent が返す `[OQ-n]`(不明点)は `paper-researcher` へ、研究上の判断はユーザーへ(AskUserQuestion でまとめて)
3. **レビュー** → `paper-reviewer1`(高重要度なら `paper-reviewer2` も)に依頼し、所見の反映は再び `user-study` subagent へ
4. 複数章にまたがる作業・フルパイプラインは `/paper-orchestrator` へ

以下は本章の執筆ナレッジ(subagent が起動時に読み込む規範であり、reviewer の判定基準)。

## 必須の構造

```
\section{User Study}

[冒頭1段落]
  - System章で作ったシステムを使って何を評価するか
  - 独立変数と従属変数の概要

\subsection{Participants}
  - 人数、年齢（M, SD, range）、性別、関連経験
  - リクルート方法（大学メーリングリスト、SNS、クラウドワーカー等）
  - インクルージョン基準
  - 矯正視力の扱い（眼鏡・コンタクトが許容か、裸眼必須か）
  - 補償（金額 or 商品、あれば）

\subsection{Study Design and Hypotheses}
  - 実験デザイン（within-subjects / between-subjects）
  - 条件（図で示すことを推奨）
  - カウンターバランス（Latin Square など）
  - 仮説 H1, H2, ... (先行研究への引用付き)

\subsection{Measures}
  - 各従属変数をどう測るか（標準尺度＋引用、または自作質問）

\subsection{Procedure}
  - 時系列で: 入室 → インフォームドコンセント → 練習 → 本試行 × N → 質問紙 → 休憩 → ... → 退室

\subsection{Analysis}
  - 正規性の検定
  - 使用した統計手法とその条件
  - Post-hoc検定、補正法
  - 有意水準、使用ソフト、バージョン
```

## 仮説の書き方（最重要）

各仮説は必ず **先行研究に基づいた根拠付き** で書く。

### ✅ GOOD の型

```latex
We established the following hypotheses:
\begin{itemize}
    \item \textbf{H1}: <条件A> produces higher <従属変数> than <条件B>.
    \item \textbf{H2}: <要因X> produces higher <従属変数> than <要因Y>.
    \item \textbf{H3}: An interaction effect exists between <要因1> and <要因2>.
    \item \textbf{H4}: <効果Z> is enhanced under <条件>.
\end{itemize}

\noindent
\textbf{H1} and \textbf{H2} are based on prior findings that 
<根拠となる既存知見1>~\cite{...}, and that <根拠となる既存知見2>~\cite{...}.
\textbf{H3} follows from the possibility that <要因1> and <要因2> 
engage overlapping mechanisms that amplify each other.
\textbf{H4} is motivated by <既存知見3>~\cite{...}, while how 
<本研究の新しい観点> influences this effect remains unexplored.
```

### ポイント

- 仮説は **箇条書き** で明示
- 「A produces higher X than B」のような **比較形** で書く
- 各仮説に **引用付きの根拠** を別段落で説明
- 仮説数は Results の主効果・交互作用の数と対応させる

## 条件名の付け方

条件は abbreviation で統一：

```
✅ GOOD:
- C1 (A1-B1): <要因A 水準1> × <要因B 水準1>
- C2 (A1-B2): <要因A 水準1> × <要因B 水準2>
- C3 (A2-B1): <要因A 水準2> × <要因B 水準1>
- C4 (A2-B2): <要因A 水準2> × <要因B 水準2>
```

図表で条件を言及するたびに同じ省略形で統一する。
本文中で長い条件名と省略形が混在しないように。

## Measuresの書き方

各従属変数について、以下を明記：

1. **尺度名と引用** (標準尺度なら)
2. **項目数**
3. **リッカートの刻み**（もしあれば）
4. **スコア算出方法** (平均、合計など)

### ✅ GOOD の型

```
\item \textbf{<従属変数名>.} We used the <尺度名>~\cite{...}, 
extracting <N items on 構成概念A> and <M items on 構成概念B> 
(<共通項目があれば言及>). Each item was rated on a <X-point> 
Likert scale (1: <最小>, X: <最大>). We computed the <構成概念A> 
score as <算出方法>.
```

### 自作質問の扱い

標準尺度がない場合は、作る必要があった理由と、各項目が何を測るかを明示：

```
\item \textbf{<自作測定の対象>.} To examine <評価したい現象>, we 
created <N個の質問>. Each question was designed to assess 
<測定したい側面> and was rated on a <X-point> Likert scale...:
(1) <質問1>.
(2) <質問2>.
...
```

## Procedureの書き方

時系列で **参加者の動きを追えるように** 書く：

```
Participants firstly read and signed the consent form.
We then started task briefing.
Next, participants <装置の装着 / セットアップ>.

Before the main trials, participants completed a practice phase 
lasting up to <分数> minutes...

Participants then experienced the <条件数> experimental conditions 
in a counterbalanced order using a Latin Square design. In each 
condition, participants <主タスク> for <時間>.

After each trial, participants <質問紙記入 / 休憩>. Participants 
took a <時間> break to <理由>, and then they proceeded to the 
next condition.

The experimental protocol followed the guidelines provided by 
the ethics review committee at our institution. All participants 
provided written informed consent.
```

必須要素：
- **practice phase** があったか
- **試行時間** と **休憩時間** が明記されているか
- **自己ペース vs 強制ペース** か、なぜそうしたか（交絡要因への対処）
- 倫理審査への言及

## Analysisの書き方

```
We analyzed the data using <parametric/non-parametric手法> when 
the Shapiro-Wilk test confirmed normal distribution, followed by 
<post-hoc検定> with <補正法> as post-hoc tests. When normality 
was not met, we applied <代替手法>~\cite{...} with <対応する 
post-hoc>. All analyses were carried out in <ソフトウェア> 
(version <バージョン>), with statistical significance set at α = 0.05.
```

必須要素：
- [ ] 正規性検定の明記
- [ ] 正規性が満たされた場合／満たされなかった場合の両方の手法
- [ ] Post-hoc検定と補正法
- [ ] 使用ソフトウェアのバージョン
- [ ] 有意水準

## ❌ Anti-patterns

### Anti-pattern 1: 仮説に根拠がない

```
BAD:
"H1: A produces higher X."
(...仮説の根拠なし)
```

→ 先行研究にgroundされていない仮説は、査読で「なぜそう予測するのか？」と必ず指摘される。

### Anti-pattern 2: 参加者情報を隠す

```
BAD: "We tested students."
```

→ 人数、年齢（平均とSDと範囲）、性別、関連経験、は必須情報。

### Anti-pattern 2b: 矯正視力・リクルート方法の情報欠落

```
BAD:
"We recruited 24 participants."
```

→ reviewer は必ず「どうやってリクルートしたか」「視力矯正の条件は？」を質問する。
先回りして書く：

```
GOOD:
"We recruited 24 participants through the university mailing list. 
All participants had normal or corrected-to-normal vision 
(contact lenses were allowed inside the HMD; participants wearing 
glasses were excluded if the HMD did not fit over them)."
```

### Anti-pattern 3: Procedureの時間情報が抜ける

```
BAD: "Participants experienced the conditions."
```

→ 各条件何分か、休憩何分か、全体で何分か、は再現性のため必須。

### Anti-pattern 4: 仮説が曖昧

```
BAD: "H1: A might affect X."
```

→ 「higher / lower / different」のように方向性を明示。

## チェックリスト

- [ ] Participants, Study Design+Hypotheses, Measures, Procedure, Analysis の5つがあるか？
- [ ] 参加者の人数、年齢（M, SD, range）、性別、経験が明記されているか？
- [ ] リクルート方法が書かれているか？（大学メーリングリスト、SNS、クラウドワーカー等）
- [ ] 矯正視力の扱いが書かれているか？（corrected-to-normal vision、眼鏡の扱い等）
- [ ] 補償の有無が書かれているか？
- [ ] within-subjects / between-subjects が明示され、カウンターバランス方法も書かれているか？
- [ ] 仮説が番号付きで箇条書きされ、各仮説に引用付き根拠があるか？
- [ ] 条件名が省略形で統一されているか？
- [ ] 各Measureについて尺度・項目数・スコア算出が明記されているか？
- [ ] Procedureが時系列順で、各ステップの時間が書かれているか？
- [ ] 倫理審査に言及しているか？
- [ ] Analysisで正規性検定・使用手法・post-hoc・ソフトウェアバージョンが明記されているか？

## 関連Skill

- `method-orchestrator`: System章と User Study章の独立変数の定義は完全に一致させる
- `results-orchestrator`: 仮説と結果は1対1で対応させる
- `writing-principles-orchestrator`: 曖昧な最上級の回避
