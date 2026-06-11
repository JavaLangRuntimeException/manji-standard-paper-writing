---
name: design-implications-orchestrator
description: Orchestrator skill for the Design Implications section - delegates writing to the design-implications subagent (this file is the rulebook the subagent loads at startup), routes open questions to paper-researcher and reviews to paper-reviewer1/2; the main conversation never writes prose itself. Use this skill whenever the user is writing, revising, or polishing the Design Implications section of a paper — the section that translates findings into actionable recommendations for designers and practitioners. Triggers include "Design Implications", "設計指針", "implications for design", "recommendations", "for designers", "practical implications", or when the user mentions educational applications, training systems, game design, or any practitioner-facing recommendation. This skill enforces: (a) avoiding vague superlatives like "highest" in favor of clear comparatives like "higher", (b) explaining unfamiliar technical terms on first mention rather than dropping them raw, (c) making trade-offs explicit (e.g., quality vs. workload), and (d) interpreting cited works precisely.
---

# Paper Design Implications Writing

学会論文のDesign Implicationsを書くときに使うSkill。

## この skill の役目(オーケストレーション)

この skill はメイン会話で動く Design Implications 章のオーケストレーター。**メイン会話は本文を書かない**:

1. **執筆・修正** → `design-implications` subagent に作業指示書(目的・対象範囲・確定/未確定事項)を Agent tool で dispatch する。subagent は起動時にこの SKILL.md を読み込む
2. **OQ の routing** → subagent が返す `[OQ-n]`(不明点)は `paper-researcher` へ、研究上の判断はユーザーへ(AskUserQuestion でまとめて)
3. **レビュー** → `paper-reviewer1`(高重要度なら `paper-reviewer2` も)に依頼し、所見の反映は再び `design-implications` subagent へ
4. 複数章にまたがる作業・フルパイプラインは `/paper-orchestrator` へ

以下は本章の執筆ナレッジ(subagent が起動時に読み込む規範であり、reviewer の判定基準)。

## この章の原則

Design Implications は、**研究結果を実務家・設計者向けの具体的推奨事項に翻訳する** 章。
ただし、以下の4つの罠に陥りやすい：

1. **曖昧な最上級の濫用**: "highest" / "best" / "greatest" が何の中で最も〜なのか不明瞭
2. **専門用語の裸使い**: 引用先の用語を説明なしに使って読者を困らせる
3. **トレードオフの隠蔽**: 推奨事項の副作用や限界を書かず、誠実でない印象を与える
4. **引用の誤読**: 先行研究の主張を自分の論旨に都合よく解釈してしまう

このSkillはこれらを防ぐためのルールを提供する。

## 必須の構造

```
\section{Design Implications}

[導入1文]
  - "Based on the results of this study, we provide the following 
     recommendations for designing <応用領域>."

[指針1] 主要知見に基づく推奨
  - 事実（Tableの参照）
  - 推奨事項
  - トレードオフ（副作用、コスト、疲労等）
  - 緩和策（関連研究の引用付き）

[指針2] 別の主要知見に基づく推奨
  ...

[指針3] 交互作用 or 部分適用
  - 片方の要因だけでも効果が得られる場合の選択肢

[指針4] 特定応用への実装
  - 教育、エンタメ、リハビリなど文脈別の推奨
```

## ルール1: 曖昧な最上級を避ける（最重要）

### ❌ Anti-pattern

```
BAD:
"<手法X> provides the highest <指標A> but also causes the greatest 
<副作用B>."
```

問題：「何の中で最も高いのか」が不明瞭。全ての既存手法の中？本研究で比較した手法の中？

### ✅ 解決策1: 比較級にする

```
GOOD:
"<手法X> provides higher <指標A> but also causes greater <副作用B>."
```

### ✅ 解決策2: 比較対象を明示

```
GOOD:
"Among the <N個> methods we compared (<method1>, <method2>, 
<method3>), <手法X> provided the highest <指標A>."
```

### ルール

- 比較対象が暗黙的 → 比較級（higher, greater, more）
- 比較対象を明示可能 → "highest among X, Y, Z"
- 単なる称賛（比較ではない）→ "strong", "substantial" 等の絶対表現

## ルール2: 専門用語を裸で使わない

### ❌ Anti-pattern

```
BAD:
"introducing load reduction measures such as the <手法の固有名> 
proposed by <Author> et al.~\cite{...} should be considered."
```

問題：読者が [原著論文] を知らないと、"<手法の固有名>" が何をするものか分からない。

### ✅ 解決策: 用語を機能的に言い換え or 簡潔な説明を付ける

```
GOOD:
"approaches that balance <目的A> and <目的B>, such as 
<機能で説明した記述 (e.g., partial mimicking of the target posture)>~\cite{...}, 
should be considered."
```

または：

```
GOOD:
"the <手法の固有名> (<1フレーズの機能説明>) proposed by 
<Author>~\cite{...}"
```

### ルール

- 引用論文の用語を使うとき → 初出で1フレーズの機能説明をセット
- 複数の論文で異なる呼称がある概念 → 自分の論文で使う呼称を決めて一貫させる

## ルール3: トレードオフを明示する

Design Implications の核心は「A を選ぶと B が犠牲になる」という **トレードオフ** の明示。

### ✅ GOOD の型

```
"<条件A> elicited higher <主要指標> compared to <条件B>. In 
particular, when combined with <条件C>, <複数指標> were enhanced.
For applications using <対象>, <推奨事項1> is recommended. 
However, <条件A>/<条件C> imposes <副作用> (<根拠: 定量値 or 
参加者コメント>).
As <先行研究>~\cite{...} reported, <類似のトレードオフ>. For use 
cases involving <延長的 or 特殊条件>, combining with <緩和策1>~\cite{...}, 
or implementing <緩和策2> is necessary."
```

構造：
1. **事実**（効果の大きさ、Tableへの参照）
2. **推奨事項**（「〜を推奨」）
3. **However, トレードオフ**（副作用、コスト、疲労など）
4. **緩和策**（関連研究の引用付き）

### ❌ Anti-pattern: 推奨のみでトレードオフを隠す

```
BAD:
"<条件A> produced better results. Therefore, we recommend <条件A> 
for all applications."
```

→ 副作用を隠すと誠実でない。査読者に「現実的でない推奨」と判断される。

## ルール4: 引用の正確な解釈

Discussion と同様、Design Implications でも **引用元の主張を正確に述べる**。

### ❌ Anti-pattern（よく発生するミス）

引用論文の主張を自分の結果に都合よく解釈してしまう：

```
BAD (記憶ベースで書いたもの):
"This finding contrasts with the previous study~\cite{A}, which 
demonstrated that <推測した内容>."
```

→ 実際の [A] の主張を確認すると、「contrasts」ではなく「consistent」だった、
   あるいは [A] はそもそもその話をしていなかった、ということが起こる。

### ✅ 解決策

```
GOOD の思考手順:
1. その引用を入れる前に、[A] のAbstractを確認
2. [A] が実際に何を主張したかを一言で要約
3. 本研究の結果と [A] の関係が consistent / contrasts / extends / 
   complements のどれか判断
4. その動詞を使って文を書く
```

```
GOOD の型:
"This finding is consistent with the previous work~\cite{A}, which 
showed that <[A] の実際の主張>. Our results further suggest that, 
when <本研究の条件>, such <[A] で示された現象> can also <追加の 
影響>.
Therefore, in <本研究の応用領域>, it is important to adopt 
approaches that balance <目的X> and <目的Y>, such as <具体的手法>~\cite{B}."
```

## ルール5: 冗長性 vs 査読者ナビゲーションのバランス

### ジレンマ

一度 Discussion で述べた事実を Design Implications で再掲するのは「冗長」になる可能性がある一方、
**査読者は Design Implications だけを読むことがある**ため、完結した記述も必要。

### 判断基準

**冗長でも残す**ケース：
- Table/Figure への参照（前章で見た表を読者が思い出せないかもしれない）
- 査読者が拾い読みする可能性が高い箇所（Design Implications、Conclusion）
- 推奨事項の根拠となる主要発見

**冗長なら削る**ケース：
- Discussion で詳しく書いたメカニズムの再掲
- 同一段落内での即時的な繰り返し

## 応用文脈別の推奨

最後のパラグラフで、**具体的な応用シナリオ別の推奨**を書く：

```
"The combination of <条件A> and <条件B> led to <本研究で最大化 
された効果>. In particular, <特定条件> showed higher <指標> 
compared to all other conditions.
For <応用1: 教育>, such as <具体例>, <効果> can be maximized by 
<推奨設定>.
For <応用2: エンタメ/訓練>, <別の推奨>.
For <応用3: 長時間利用>, <疲労を考慮した推奨>."
```

典型的な応用文脈：
- **教育**: 学習効果を最大化したい
- **エンタメ/ゲーム**: 体験の質を最大化したい
- **訓練/リハビリ**: 効果の持続性と安全性を重視
- **長時間利用**: 疲労・負荷を抑えたい

## チェックリスト

- [ ] "highest" / "best" / "greatest" を grep し、比較級または比較対象明示に修正したか？
- [ ] 引用している手法/コンセプトに簡潔な機能説明を付けたか？
- [ ] 推奨事項ごとに **トレードオフ** を明示したか？
- [ ] トレードオフに対する **緩和策**（関連研究付き）を示したか？
- [ ] 引用元の先行研究の主張を正確に述べているか？（"consistent with" vs "contrasts with" の使い分け）
- [ ] 応用文脈別（教育、ゲーム、訓練等）の推奨が書かれているか？
- [ ] Table/Figure への参照が適切に入っているか？

## 実行時の手順

1. 既存の Design Implications を読み、**"highest", "best", "greatest"** を grep で洗い出す
2. 各箇所について「比較対象は何か？」を考え、比較級または明示に修正
3. 引用している手法名について、読者が理解できる説明があるか確認
4. 各推奨事項がトレードオフと緩和策のペアで書かれているか確認
5. 引用している先行研究の主張を再確認（Abstract を再読）
6. Discussion の結論から、Design Implications への接続が自然か確認

## 関連Skill

- `discussion-orchestrator`: Discussionのメカニズム解釈を簡潔に反映
- `writing-principles-orchestrator`: 曖昧語の回避、比較対象の明示、引用の正確な解釈
