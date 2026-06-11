---
name: conclusion-orchestrator
description: Orchestrator skill for the Conclusion section - delegates writing to the conclusion subagent (this file is the rulebook the subagent loads at startup), routes open questions to paper-researcher and reviews to paper-reviewer1/2; the main conversation never writes prose itself. Use this skill whenever the user is writing or revising the Conclusion section of a paper. Triggers include "Conclusion", "結論", "まとめ", "締め", "最終章", or when the user says "the paper is almost done, just need to write the conclusion". This skill ensures the conclusion does three things in order: (1) briefly restate the problem and approach, (2) summarize key findings in relation to the hypotheses, (3) gesture toward broader implications. It should be concise (typically 2-4 paragraphs) and must align with the Abstract and Contributions bullet list in the Introduction.
---

# Paper Conclusion Writing

学会論文のConclusion章を書くときに使うSkill。

## この skill の役目(オーケストレーション)

この skill はメイン会話で動く Conclusion 章のオーケストレーター。**メイン会話は本文を書かない**:

1. **執筆・修正** → `conclusion` subagent に作業指示書(目的・対象範囲・確定/未確定事項)を Agent tool で dispatch する。subagent は起動時にこの SKILL.md を読み込む
2. **OQ の routing** → subagent が返す `[OQ-n]`(不明点)は `paper-researcher` へ、研究上の判断はユーザーへ(AskUserQuestion でまとめて)
3. **レビュー** → `paper-reviewer1`(高重要度なら `paper-reviewer2` も)に依頼し、所見の反映は再び `conclusion` subagent へ
4. 複数章にまたがる作業・フルパイプラインは `/paper-orchestrator` へ

以下は本章の執筆ナレッジ(subagent が起動時に読み込む規範であり、reviewer の判定基準)。

## この章の原則

Conclusion は新しい情報を提示する場所ではなく、
**Abstract / Intro / Results / Discussion を総合して、読者に「この論文の要点」を持ち帰らせる場**。

したがって、内容は Abstract と Introduction の Contributions と整合している必要がある。

## 必須の構造

```
\section{Conclusion}

[段落1: 何を調べたか]
  - 問題提起を1文で再掲
  - 手法の概要を1-2文で
  - 被験者数、条件数などの基本情報（細かくは不要）

[段落2: 主要発見 (仮説との対応)]
  - H1, H2, ... について support/not support を明示
  - 重要な交互作用や主効果を1-2個に絞って再掲

[段落3: 新規知見]
  - 本研究で最もユニークな知見
  - 先行研究との位置関係
  - なぜこれが重要か

[段落4: 意義・応用]
  - Design implications の要約（教育、エンタメ、訓練等）
  - 研究分野への貢献
```

## ✅ GOOD の型

```latex
\section{Conclusion}

[段落1]
We investigated how <独立変数1> and <独立変数2> affect <従属変数> 
in <本研究の対象>, crossing <条件1> conditions with <条件2> 
conditions in a <実験形式> with <N> participants.

[段落2 - 仮説との対応]
Our results show that <主要発見1> and <主要発見2> (supporting 
<該当仮説>, but not <非支持仮説>). This means that <実践的含意>. 
<さらに強い発見> was observed when <特定条件>.

[段落3 - 新規知見]
Critically, we found that <独自の発見>. <その発見の意味>. Our 
results show that this <現象> is driven not only by <従来の要因> 
but also by <本研究が明らかにした新要因>, <該当仮説> supporting 
<仮説>. <本研究で最も強い効果が観察された条件> also showed 
<付随的発見>, suggesting that <メカニズム仮説>.

[段落4 - 意義]
These findings provide a unified account of how <独立変数> shape 
<対象現象> during <応用領域>, with practical design implications 
for applications in <応用1>, <応用2>, and <応用3>.
```

## Abstract / Introduction との整合性

Conclusion を書いたあと、**必ず** Abstract と Introduction の Contributions と照合：

- Abstract の contributions が3つ → Conclusion でも同じ3つが（言い換えで）出ているか
- Intro の段落4 の contributions list と、Conclusion の主要発見段落が一致しているか
- Abstract の「意義」部分と、Conclusion の最終段落が同じ方向を向いているか

**ズレがある場合は Conclusion ではなく Abstract / Intro を修正する**のが基本
（前者の方が読者のファーストインプレッションに関わるため）。

## ❌ Anti-patterns

### Anti-pattern 1: 新しい情報を Conclusion で出す

```
BAD:
"We also tested <新しい条件>, and found that <新しい結果>."
```

→ 新情報は Results で述べるべき。Conclusion は「まとめ」のみ。

### Anti-pattern 2: 過剰な一般化

```
BAD:
"Our findings prove that <一般原理>."
```

→ 単一の研究で "prove" はできない。"suggest", "provide evidence for" に。

### Anti-pattern 3: 仮説との対応を省略

```
BAD:
"We found interesting effects."
```

→ H1, H2, H3, H4 について support/not support を明示。

### Anti-pattern 4: Future Work を Conclusion に混ぜる

```
BAD:
"In conclusion, we found X. Future work should explore Y and Z 
and also A, B, C..."
```

→ Future work は §Limitations に。Conclusion は本研究の結論に専念。

### Anti-pattern 5: Abstract のコピペ

```
BAD:
(Abstract と一字一句同じ文を並べる)
```

→ Abstract は論文を読む前の要約、Conclusion は読んだ後のまとめ。
   同じ事実でも異なる角度・詳細度で書く。

### Anti-pattern 6: 長すぎる

```
BAD:
(Conclusion が半ページ以上)
```

→ 多くても2-4段落。読者はここで「締め」を期待している。

### Anti-pattern 7: 内部ラベル（C1/C2/H1/H2）を Conclusion で使う

```
BAD:
"C1 and C2 showed higher <DV> than C3 and C4, supporting H1 and H2."
```

問題：条件名（C1, C2）や仮説ラベル（H1, H2）は論文内部の参照用記号。
**Conclusion は「この論文を通読していない読者でも要点を持ち帰れる」場所**であり、
内部ラベルで語ると部外者には暗号になる。

### ✅ 解決策: 条件・仮説の内容を記述的に書く

```
GOOD:
"Postures matching the target's body shape, combined with 
locomotion-style gestures, elicited higher embodiment than 
mismatched postures alone — confirming that posture matching 
and locomotion act additively."
```

### ルール

- **Conclusion では C1/C2/H1/H2 のような内部ラベルを原則使わない**
- 条件名を使いたい場合も、"the locomotion condition", "the posture-matched condition"
  のような **意味が分かる名称** に置き換える
- 仮説への参照も「H1 supported」ではなく「consistent with our prediction that <内容>」
- Results / Discussion では内部ラベル可、**Conclusion だけは記述的な言い換えが原則**

### Anti-pattern 8: 本研究が主張していないフレーミングを出す

```
BAD (本文で "one-size-fits-all approach" を議論していないのに):
"Our findings suggest that a one-size-fits-all approach is insufficient."
```

問題：論文本文で使っていない枠組みを Conclusion で唐突に出すと、
reviewer に「これはどこから来た？」と指摘される。

### ✅ 解決策

Conclusion で使うフレーミング・概念語は、**Abstract / Intro / Discussion のいずれかで
既出のもののみ** を使う。新しい概念語を導入してはならない。

## Intro 4段落 / Abstract 5部 / Conclusion 4段落の対応表

| Intro段落 | Abstract部 | Conclusion段落 |
|----------|-----------|---------------|
| 段落1 (Q1: Problem) | [1] 問題提起 | 段落1 (問題と手法の再掲) |
| 段落2 (Q2: Gap) | [2] 技術的アプローチ | (暗黙) |
| 段落3 (Q3: Approach) | [3] 評価概要 | 段落1後半 |
| 段落4 (Contributions) | [4] Contributions | 段落2-3 (仮説との対応+新規知見) |
| (暗黙) | [5] 意義 | 段落4 (意義) |

この対応を守ると、論文全体の論理構造が一貫する。

## チェックリスト

- [ ] 2-4段落で簡潔に書かれているか？
- [ ] 段落1で問題・手法を再掲しているか？
- [ ] H1, H2, ... について supported/not supported を明示しているか？
- [ ] 本研究で最もユニークな知見（新規性）を強調しているか？
- [ ] Abstract の contributions と整合しているか？
- [ ] Intro 段落4 の contributions リストと整合しているか？
- [ ] 新しい情報（結果、引用）を出していないか？
- [ ] Future work を混ぜていないか？
- [ ] "prove" のような過剰な断定を避けているか？
- [ ] 内部ラベル（C1/C2/H1/H2）を使っていないか？（記述的な言い換えに）
- [ ] Abstract / Intro / Discussion で登場しなかった概念語を Conclusion で唐突に導入していないか？

## 実行時の手順

1. Abstract / Intro / Results / Discussion を参照して、論点の一貫性を確認
2. 段落1〜4の骨格を書く
3. Abstract との整合性を verify
4. 新情報・future work の混入を削除
5. 長さが2-4段落に収まっているか確認

## 関連Skill

- `abstract-orchestrator`: Abstract との整合性は必須
- `introduction-orchestrator`: Intro のcontributions list との整合性
- `limitations-orchestrator`: Future work はこちらに分離
