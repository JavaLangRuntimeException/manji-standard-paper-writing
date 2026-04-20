---
name: mspw-limitations
description: Use this skill whenever the user is writing or revising the Limitations, Limitations and Future Work, or Future Directions section of a paper. Triggers include "Limitations", "Future Work", "limitations", "将来研究", "今後の課題", "制約", "将来の方向性". Also trigger when a reviewer asks the authors to "discuss generalizability" or "address sample limitations". This skill helps the user acknowledge limitations honestly (avoiding both over-apology and hiding weaknesses) and frame them as motivated future directions rather than pure weaknesses.
---

# Paper Limitations / Future Directions Writing

学会論文のLimitations章を書くときに使うSkill。

## 核心的な方針

Limitations章は次の2つの間でバランスを取る：

1. **正直に限界を認める**（査読者に見透かされないため）
2. **Future Work として前向きに提示する**（過剰な self-deprecation を避けるため）

**完全に隠す**のも**自虐的に書く**のも両方とも悪手。
「現在の研究でできなかったことを、将来の研究機会として positioning する」のが基本姿勢。

## 必須の構造

```
\section{Limitations and Future Directions}

[導入1文]
  - "Our results raise several questions that remain important for 
     advancing <領域>." (前向きな導入)

\textbf{<Limitation 1 のラベル>.}
  - 事実としての制約
  - なぜこの制約がこの研究では許容される or 不可避だったか（簡潔に）
  - これに関する将来研究の方向性

\textbf{<Limitation 2 のラベル>.}
...

\textbf{<Limitation 3 のラベル>.}
...
```

## 典型的な Limitations カテゴリ

学会論文では以下のカテゴリから3-5個を選ぶことが多い：

1. **サンプルの偏り** (性別、年齢、文化、経験)
2. **時間的制約** (試行時間が短い、長期効果が未検証)
3. **対象範囲の狭さ** (特定の対象や条件のみ検証)
4. **測定手法の限界** (自己申告、生理指標なし)
5. **技術的制約** (ハードウェア性能、検出精度)
6. **生態学的妥当性** (実験室 vs 実応用)
7. **実験結果から浮上した限界** (予想外の null / 交互作用の不在が示す境界)

### カテゴリ7（結果由来の限界）の重要性

Reviewer は「**実験結果が明らかにした本研究の境界**」を最も評価する。
「サンプル数が少ない」のような generic な limitation だけでは不十分で、
**今回の実験をやって初めて分かった限界** を少なくとも1つは含めること。

### ✅ 例: 結果由来の limitation

```
\textbf{Boundary conditions revealed by null interaction.}
We found no interaction between <factor A> and <factor B> for 
<DV>, suggesting that their effects are additive within the 
tested range. However, this may hold only within <testing range>; 
at the extremes of <A> or <B>, <mechanism> could shift the 
additive relationship to a multiplicative one. Future work 
varying <A> and <B> over a wider range would clarify whether 
the additive relationship generalizes.
```

このタイプの limitation は：
- Results で観察された事実（null interaction）を出発点にしている
- なぜそれが限界なのかを mechanism で説明している
- Future work と接続している

### 各 limitation を「なぜ field にとって重要か」と結ぶ

Reviewer はしばしば「その limitation が解消されると、この分野に何が進むのか」を知りたがる。
単に「〜を検証していない」で終わらせず、**それが解消されることの field への意義** を1文で添える。

```
GOOD:
"Investigating <未解決の問い> would advance <分野の次の段階>, for 
example by <具体的な波及効果>."
```

## 各 Limitation の書き方

### ✅ GOOD の型

```
\textbf{<カテゴリラベル>.}
<制約の事実>. <なぜこの研究ではその範囲にとどめたか>. An open 
question is whether <未解決の問い>. <技術的 or 理論的な根拠 で、
その問いが意味を持つ理由>~\cite{...}. Investigating this would 
clarify whether <本研究の知見> generalize across <範囲> or are 
specific to <検証した範囲>.
```

構造：
1. **制約の事実**（何を検証しなかったか）
2. **許容される理由**（なぜその範囲でよかったか）
3. **未解決の問い**（次に答えるべき問い）
4. **技術的根拠**（なぜその問いが意味を持つか）
5. **将来研究の意義**（答えが分かると何が進むか）

### 例：時間的制約

```
\textbf{Temporal dynamics of <現象>.}
Each trial lasted <時間>, which was sufficient to elicit measurable 
differences but did not reveal whether <現象> adapts, strengthens, 
or decays over longer exposure. Since <現象> may require sustained 
<条件> to influence <応用への影響> meaningfully, understanding 
temporal dynamics is critical for applications such as <応用1> 
or <応用2> that involve extended sessions.
```

### 例：サンプルの偏り

```
\textbf{Participant diversity.}
The sample was skewed in <属性1> (<具体的な偏り>) and <属性2> 
(<具体的な偏り>). Prior work suggests that <属性> influences 
<関連する応答>~\cite{...}, and future studies should recruit a 
more balanced sample to assess generalizability.
```

### 例：対象範囲の狭さ

```
\textbf{Effect of <変動しうる要因> on <本研究の効果>.}
<本研究が対象とした範囲>, which made <手法の選択> feasible. An 
open question is whether the degree of <要因> modulates the 
effect of <本研究で検証した要因> on <現象>. <対象を変えた場合> 
have fundamentally different <性質>~\cite{...}, and the same 
approach may yield weaker results if <理由>. Investigating this 
would clarify whether our findings generalize across <範囲> or 
are specific to <検証した範囲>.
```

## ❌ Anti-patterns

### Anti-pattern 1: 過剰な self-deprecation

```
BAD:
"Our study has numerous severe limitations. Our sample is too 
small, our measures are inadequate, and our conclusions may not 
generalize at all."
```

→ これは誠実ではなく、却って査読が厳しくなる。論文の価値を下げる必要はない。

### Anti-pattern 2: 制約を隠す

```
BAD:
(Limitations章がない、または "no significant limitations" と書く)
```

→ 査読者は必ず limitations を指摘する。先に書いた方がよい。

### Anti-pattern 3: 制約だけ書いて Future Work につなげない

```
BAD:
"Our sample was biased (<偏り>)."
(ここで終わり)
```

→ 「だから future work で balanced sample を使うべき」と前向きに接続。

### Anti-pattern 4: 漠然とした future work

```
BAD:
"Future work should explore more scenarios."
```

→ 具体的なトピックと、なぜそれが重要かを書く。

### Anti-pattern 5: 本文の他の章で Claim した範囲を超える限界を主張

```
例: Abstract で「we generalize to all X」と書いたのに、
    Limitations で「we only tested a subset of X」と矛盾させる
```

→ Abstract / Intro / Discussion の claim 範囲と Limitations の制約範囲は整合させる。
   Claim を先に調整する方が良い。

## 既知の指摘に対する Rebuttal 準備

査読で指摘されそうな点を先回りして Limitations に書くことで、reviewer を preempt できる：

- 「統計検出力は十分か？」→ 「with N=<数>, we had power=<値> for detecting <effect size> effects」
- 「交絡要因は？」→ 「We controlled for X and Y, though Z remains a potential confound」
- 「生態学的妥当性は？」→ 「our lab setting may not capture <実環境特性>; future field studies would ...」

## チェックリスト

- [ ] 3-5個の limitations が書かれているか？
- [ ] 各 limitation が **具体的**（数値、範囲、対象）に書かれているか？
- [ ] 各 limitation が **future direction** に接続されているか？
- [ ] Future direction が漠然とした "more research is needed" で終わっていないか？
- [ ] Abstract / Intro / Discussion の claim 範囲と矛盾していないか？
- [ ] Self-deprecation が過剰でないか？
- [ ] 査読者が指摘しそうな既知の弱点をカバーしているか？
- [ ] 太字ラベル（\textbf{}）でカテゴリが分かりやすくなっているか？
- [ ] 実験結果から浮上した limitation（null / unexpected interaction / 境界条件）を少なくとも1つ含めたか？
- [ ] 各 limitation が解消されると field に何が進むのかが1文で示されているか？

## 実行時の手順

1. 本研究で「できなかった」ことを列挙（5-10個）
2. そのうち「査読者が必ず指摘するであろう」「この分野で重要な」3-5個を選ぶ
3. 各 limitation について、カテゴリラベル → 事実 → 理由 → future direction の形式で書く
4. Abstract / Intro の claim 範囲と矛盾していないか確認
5. Conclusion とも整合するよう調整

## 関連Skill

- `mspw-conclusion`: Limitations の論旨と Conclusion の主張の整合性
- `mspw-writing-principles`: 誠実な記述、曖昧表現の回避
