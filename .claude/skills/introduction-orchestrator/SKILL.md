---
name: introduction-orchestrator
description: Orchestrator skill for the Introduction section - delegates writing to the introduction subagent (this file is the rulebook the subagent loads at startup), routes open questions to paper-researcher and reviews to paper-reviewer1/2; the main conversation never writes prose itself. Use this skill whenever the user is writing or revising the Introduction section of an academic paper. Triggers include "write the intro", "イントロを書く", "introduction を直す", "第1章", "Heilmeier", requests to review intro drafts, or when a user explicitly says the abstract is done and they want to move to the introduction. This skill enforces the Heilmeier's Catechism structure (4 paragraphs corresponding to questions 1-4), and critically, requires Contributions at the end as a bulleted list (not Research Questions). Readers want to know conclusions, not questions — so the intro ends with what was found, not what was asked.
---

# Paper Introduction Writing

学会論文のIntroductionを書く/直すときに使うSkill。

## この skill の役目(オーケストレーション)

この skill はメイン会話で動く Introduction 章のオーケストレーター。**メイン会話は本文を書かない**:

1. **執筆・修正** → `introduction` subagent に作業指示書(目的・対象範囲・確定/未確定事項)を Agent tool で dispatch する。subagent は起動時にこの SKILL.md を読み込む
2. **OQ の routing** → subagent が返す `[OQ-n]`(不明点)は `paper-researcher` へ、研究上の判断はユーザーへ(AskUserQuestion でまとめて)
3. **レビュー** → `paper-reviewer1`(高重要度なら `paper-reviewer2` も)に依頼し、所見の反映は再び `introduction` subagent へ
4. 複数章にまたがる作業・フルパイプラインは `/paper-orchestrator` へ

以下は本章の執筆ナレッジ(subagent が起動時に読み込む規範であり、reviewer の判定基準)。

## このSkillが解決する問題

- Introの最後でRQ (Research Questions) を列挙してしまい、「で、結論は？」と読者を煙に巻く
- 問題提起、ギャップ、アイデア、貢献の順序が崩れて読み手が混乱する
- 各段落が何を答えているか論理的に整理されていない

**読み手（特に査読者）は「結論」を知りたい**。RQで終わると「それで？」となる。
Contributionsを箇条書きで終わらせることで、読了時点で論文の主張が明確になる。

## Heilmeier's Catechism（1〜4）

参照: https://www.csee.umbc.edu/~finin/home/heilmeyerCatechism.html

論文Introが答えるべき4つの問い：

1. **What is the problem, why is it hard?** (問題は何か、なぜ難しいか)
2. **How is it solved today?** (現状ではどう解決されているか)
3. **What is the new technical idea; why can we succeed now?** (新しい技術アイデアは何か、なぜ今なら成功するか)
4. **What is the impact if successful?** (成功したらどういうインパクトがあるか)

これらに対応した **4段落+Contributions箇条書き** の構成にする。

## 必須の構造

```
段落1: 問題提起 [Q1対応]
  - 領域で何ができるようになったか
  - しかしまだ解決されていない根本的問題は何か
  - なぜそれが hard / 重要か

段落2: 現状とギャップ [Q2対応]
  - 既存研究A、既存研究Bが独立に何を示しているか
  - しかし「これらの交差点」「組み合わせ」「境界条件」は検証されていない
  - → 明確なリサーチギャップを提示

段落3: 本研究の技術的アイデア [Q3対応]
  - どんな実験/システムでこのギャップを埋めるか
  - なぜこのアプローチで成功できるか（鍵となる insight）
  - 評価の簡単な概要（詳細は User Study 章に）

段落4: Contributionsの箇条書き [Q4対応]
  - "Overall, we make the following N contributions:"
  - 箇条書きで番号付き
  - 各contributionは「結果/発見」として書く
```

## 絶対に避けるべきパターン

### ❌ Anti-pattern 1: RQで終わる

```
BAD:
"We investigate the following research questions:
 RQ1: Does factor A affect outcome Y?
 RQ2: Does factor B affect outcome Y?
 RQ3: Do A and B interact?"
```

→ 読み手は結論を知りたい。RQで終わるとAbstract以上の情報が得られない。

### ✅ GOOD (Contributions箇条書きで終わる):

```
"Overall, we make the following three contributions:
- We integrate framework X and framework Y and reveal that 
  <具体的な発見>.
- We demonstrate that <現象Z> is driven by <新しい要因>, 
  extending prior work focused on <従来の要因>.
- We extend <従来のスコープ> to <新しいスコープ>, providing 
  design implications for <応用領域>."
```

### ❌ Anti-pattern 2: Q1〜Q4の順序を崩す

問題→ギャップ→アイデア→貢献 の順序が崩れると読者が迷う。
特に **「今回の実験条件の説明」が段落1（問題）や段落2（現状）に紛れ込む**のは厳禁。

### ❌ Anti-pattern 3: Abstractと不整合

Introの段落4のContributionsとAbstractの[4]Contributionsは **同じ数・同じ順序** で書く。
ズレていると査読者が混乱する。

## ✅ 推奨パターン（段落ごとのテンプレート）

```latex
\section{Introduction}

% 段落1: Q1 - 問題
<領域の最近の進展>. When users do <X>, <望ましい効果> has been shown 
to emerge~\cite{...}. To achieve these effects, <必要条件1>, <必要条件2>.
<しかし、根本的なギャップが何か>

% 段落2: Q2 - 現状とギャップ  
<本研究が埋めるギャップの技術的性質>.
In the literature, <既存研究の軸1> has been identified~\cite{A}, 
and <既存研究の軸2> has been studied~\cite{B}. However, <それらの交点 
or 境界条件> has been examined only under <限定条件1>, and <軸2> 
only under <限定条件2>. Whether these two factors interact, and 
whether they jointly influence <付随的効果>, remains unknown.

% 段落3: Q3 - 本研究の技術アイデア
We address this gap by <何をしたか>. <選択した対象 or 手法> was 
chosen because <鍵となる特徴>. Rather than <重い代替案> (which 
would cause <問題>), we <軽量化した本研究のアプローチ>. In a 
<実験形式> (N=XX), we <独立変数の操作>.

% 段落4: Q4 - Contributions箇条書き
Overall, we make the following N contributions:
\begin{itemize}
    \item We <動詞> <発見1>.
    \item We <動詞> <発見2>.
    \item We <動詞> <発見3>.
\end{itemize}
```

## Contributionsの書き方

各項目は以下を満たす：

- **動詞から始める**: "We show that...", "We demonstrate...", "We extend...", "We integrate..."
- **結果を述べる**: 「〜を提案する」ではなく「〜であることを示した」
- **具体的**: 「XXXを改善した」ではなく「XXXがYYYという挙動を示した」
- **先行研究との関係を明示**: 「既存研究を〜に拡張した」「既存研究をと〜を統合した」など

## チェックリスト

- [ ] 4段落構成になっているか？（Q1, Q2, Q3, Q4 + Contributions）
- [ ] 段落1が「問題とその難しさ」で、手法の詳細を早出ししていないか？
- [ ] 段落2で既存研究の現状を説明し、明確にギャップを示しているか？
- [ ] 段落3で本研究のアイデアを説明し、なぜこのアプローチが機能するかを述べているか？
- [ ] 最後がContributions箇条書きで終わっているか？（RQで終わっていないか）
- [ ] Contributionsの項目はAbstractと整合しているか？
- [ ] 各Contributionが「動詞＋結果」の形式になっているか？

## 実行時の手順

1. 既存Introがあれば、まず4段落構成にマッピングしてみせる（段落1=Q1？段落2=Q2？など）
2. 欠けている問い、順序が崩れている箇所、RQで終わっている箇所を指摘
3. 段落1〜段落4の骨組みを先に書き、各段落のトピックセンテンスを確定させてから中身を書く
4. Contributionsは最後に書く（Abstractと合わせるため）
5. 完成後、Abstractのcontributionsと番号・順序・内容が一致していることを確認

## 関連Skill

- `abstract-orchestrator`: Contributionsの内容・順序はAbstractと必ず一致させる
- `writing-principles-orchestrator`: 冗長表現、曖昧語の回避など
