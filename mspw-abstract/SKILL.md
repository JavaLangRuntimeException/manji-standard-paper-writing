---
name: mspw-abstract
description: Use this skill whenever the user is writing or revising an abstract for an academic paper (international conference full paper, journal article, workshop paper). Triggers include mentions of "abstract", "アブスト", "要旨", "要約", requests to "write/revise/fix the abstract", or when the user shares an abstract draft for review. Also trigger when the user is finalizing a paper and needs the abstract aligned with the rest of the manuscript. This skill enforces a 5-part structure that leads with the technical contribution (NOT the user study details) and keeps contributions structurally clear (numbered list OR prose enumeration — both are acceptable).
---

# Paper Abstract Writing

学会論文のAbstractを書く/直すときに使うSkill。

## このSkillが解決する問題

学生が書くAbstractでよくある失敗：

- User studyの詳細（N、条件数、within-subjects等）が冒頭に来て、**技術的貢献がどこか分からない**
- 発見が散文に埋没し、**何が主要なcontributionかを読者が読み取れない**
- 実験デザインの細部が過度に書かれ、**論文全体の意義がぼやける**

これらを防ぐため、構造と優先順位を強制するのがこのSkillの目的。

## 必須の構造：5部構成

Abstractは必ず次の順序で書く：

```
[1] 問題提起 (Problem + Research Gap)
    ↓
[2] 技術的アプローチ / システム設計 (Technical Approach)
    ↓
[3] 評価の概要 (Evaluation Overview — 詳細は書かない)
    ↓
[4] Contributions (構造的に明確に)
    ↓
[5] 意義 / Impact
```

各部分の比重の目安：
- [1] 問題提起：2-3文
- [2] 技術的アプローチ：2-3文（**ここが最重要**）
- [3] 評価概要：1文で「〜を検証した」程度。統計手法やN数の詳細は本文に回す
- [4] Contributions：番号付きリストでも、"We show that ..., that ..., and that ..." のような散文での明示的列挙でもよい。**形式は問わないが、何が発見/貢献かが読み取れることが必須**
- [5] 意義：1文

> **Note:** Abstractでは番号付き箇条書きは必須ではない。散文でも、各contributionが独立した節として読み取れれば OK。ただし Introduction の末尾では番号付き箇条書きが必須である点に注意（Introでは読者が結論を数えて確認できることが重要）。

## 絶対に避けるべきパターン

### ❌ Anti-pattern 1: User studyの詳細を冒頭に置く

```
BAD:
"We conducted a within-subjects experiment with N participants 
comparing condition A and condition B across two factors..."
```

→ 読者は「で、何が新しいの？技術的貢献は？」となる。

### ❌ Anti-pattern 2: Contributionsが散文に溶け込んで境界が見えない

```
BAD:
"Our results show that factor A matters and factor B matters 
and they combine in interesting ways, and also something 
about factor C..."
```

→ 散文自体が悪いのではなく、**各contributionの境界が見えない**のが問題。
番号を振らなくても、"We show that X. We further demonstrate that Y.
Finally, we find that Z." のように **文単位で独立**させれば散文でも可。

### ❌ Anti-pattern 3: 評価の過度な詳述

```
BAD:
"In a within-subjects experiment crossing two levels of factor A 
(X vs. Y) with two levels of factor B (P vs. Q), with N=XX 
participants using method M for Z minutes, we show that..."
```

→ Abstractで実験デザインを細かく書く必要はない。Contributions（**何が分かったか**）を前に出す。

## ✅ 推奨パターン（テンプレート）

```
[1] 問題提起:
<広い文脈>. <しかし>, <解決されていない根本的問題> remains challenging 
due to <根本的な理由>.

[1.5] 研究ギャップ:
Prior work has independently shown <既存知見A> and <既存知見B>, 
but <それらの組み合わせ / 境界条件> is unexplored.

[2] 技術的アプローチ（最重要）:
We address this gap by <作ったもの / 設計した実験プラットフォーム> 
that <鍵となる設計判断>, enabling <可能にしたこと>.

[3] 評価概要（簡潔に）:
Through <評価方法の一言要約>,

[4] Contributions（形式は自由、ただし境界を明示）:
we show that <発見1>, that <発見2>, and that <発見3>.
（または番号付きリストで箇条書きにしてもよい）

[5] 意義:
These results <先行知見との位置関係> and provide design implications 
for <応用領域>.
```

## Contributionsの書き方

各項目は以下の形式で：
- **結果を述べる動詞**: "we show that ...", "we demonstrate ...", "we reveal that ..."
- 「〜を提案する」ではなく「〜**であることを示した**」
- 抽象的な美化表現（"novel", "effective"）は避け、具体的な発見を書く

## チェックリスト

Abstractを書いた/直したあと、以下を必ず確認：

- [ ] 冒頭でUser studyの詳細（N、within-subjects、条件数など）を語っていないか？
- [ ] 技術的貢献（どういうシステム/手法/知見を作ったか）が構造の[2]で明確か？
- [ ] Contributionsの境界が読み取れるか？（番号付き、箇条書き、または文単位で独立した散文、いずれでもよい）
- [ ] 各Contributionが「結果/発見」として書かれているか？（手法説明だけになっていないか）
- [ ] 最後の1文が「この研究の意義」を示しているか？
- [ ] 学会指定の文字数/語数に収まっているか？
- [ ] Abstractのcontributionsが、Intro最後のcontributions箇条書きと一致しているか？

## 実行時の手順

1. 既存のAbstractがあれば、まず上記5部構成に分解してみせる（どこに問題があるか明示）
2. 欠けている要素・順序が違う要素を指摘
3. 修正案を提示する際は、[1]〜[5]のラベルを付けて構造が見えるようにする
4. ユーザーに確認してから統合版を作成
5. 完成後、Introの最後のContributions箇条書き部分と整合していることを確認

## 関連Skill

- `mspw-introduction`: Abstractで示したContributionsはIntroの最後に箇条書きで再掲する必要がある
- `mspw-writing-principles`: 曖昧な最上級や断定表現の回避など、横断的ルール
