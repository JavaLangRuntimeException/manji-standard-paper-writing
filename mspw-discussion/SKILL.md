---
name: mspw-discussion
description: Use this skill whenever the user is writing or revising the Discussion section of a paper — the section that interprets the Results, explains underlying mechanisms, and connects findings to prior work. Triggers include "Discussion", "考察", "6章", "interpret results", "mechanism", or when the user is explaining *why* their results came out a particular way. Critically, this skill enforces precise interpretation of cited works (checking whether a prior study is truly contrasted or actually consistent with the current findings) and separates mechanistic explanation from the descriptive Results section. Also use this skill when explaining interaction effects, lack-of-interaction findings, or reconciling findings with or against prior literature.
---

# Paper Discussion Writing

学会論文のDiscussion章を書くときに使うSkill。

## この章の原則

Discussionは次の3つを行う章：

1. **Resultsで観察された事実に対して、なぜそうなったか（メカニズム）を説明**
2. **本研究の結果を先行研究の文脈に正確に位置づける**
3. **仮説との対応を再確認し、理論的示唆を引き出す**

特に重要なのは、**引用している先行研究の主張を正確に述べる** こと。
自分の結果に都合のよい方向に引用を誤読すると、論理が崩れるだけでなく、
査読者に「この著者は先行研究を理解していない」と判断される。

## 必須の構造

```
\section{Discussion}

[冒頭1段落]
  - 本研究で何を検証したかの簡潔な再掲

\subsection{発見1の解釈 (主効果)}
  - Resultsで観察された事実の簡潔な再述
  - なぜそうなったか（メカニズム）の説明
  - 先行研究の知見とどう整合するか

\subsection{発見2の解釈 (交互作用 or 欠如)}
  - 交互作用が「ない」ことも重要な発見
  - 理論的フレームワークで説明

\subsection{発見3の解釈 (新規知見 / 付随的発見)}
  - 本研究が特に新しく示した知見
  - 先行研究との位置関係を明確に
```

## 引用の正確な解釈（最重要ルール）

### ❌ Anti-pattern: 先行研究の主張を誤読

```
BAD:
"This finding contrasts with the previous study~\cite{A}, 
which demonstrated that <既存手法> reduced <指標X>."
```

問題：先行研究 [A] が実際に示したのは「<既存手法>が<指標Y>を enhance した」かもしれない。
"reduced" でも "<指標X>" でもないケースがある。**論文を読んだ内容と記憶ベースで書いた内容が
乖離することがしばしばある**。

### ✅ 解決策: 引用元の主張を Abstract レベルで確認してから書く

```
GOOD:
"This finding is consistent with the previous work~\cite{A}, 
which showed that <既存手法> enhanced <指標Y>.
Our results further suggest that, <本研究の条件下では>, such 
<既存手法の効果> can also <追加的な影響>."
```

### 引用動詞の使い分け

| 動詞 | 意味 | 使うとき |
|------|------|---------|
| "consistent with" | 整合的 | 本研究の結果が既存研究を支持・補強 |
| "contrasts with" | 対照的 | **本当に**反対の結果・主張がある（慎重に） |
| "extends" | 拡張 | 既存研究の範囲を拡げる |
| "builds on" | 基盤 | 既存研究を土台として新しい層を作る |
| "differs from" | 異なる | 手法や対象が違うだけで対立はしない |
| "complements" | 相補的 | 異なる側面を埋める |

### 実行時チェック

引用を入れる前に自問：
- [ ] その論文のAbstract / Conclusion を読み直したか？
- [ ] 主張の方向（enhance か reduce か、増か減か）を正確に把握しているか？
- [ ] "contrasts with" と書く前に、本当に対照的か？単に異なるだけではないか？
- [ ] 数値や具体的な条件（「条件Xのみ」「手法Yのみ」等）を誤解していないか？

## メカニズム説明のパターン

Resultsで **何が起こったか** を述べる代わりに、Discussionでは **なぜ起こったか** を説明する。

### ✅ GOOD の型

```
"For <従属変数>, both <要因1> and <要因2> were confirmed to have 
independent effects. <条件A> elicited higher <従属変数> than 
<条件B>, and <操作X> induced higher <従属変数> than <操作Y>. 
These results indicate that <一般的原則>.

The effect of <要因> can be attributed to <メカニズム仮説> rather 
than <対立仮説>. <両者の違いの具体的説明>. This difference 
manifests as <観察された効果> in <従属変数>."
```

ポイント：
- 最初に事実を要約（Resultsの再述は最小限）
- 次にメカニズム（なぜそうなるか）を提示
- 対立仮説を排除する議論を入れる（「単なるXではなくYである」）
- 可能なら理論的フレームワークで説明

## 交互作用がない場合の扱い

「有意差なし」「交互作用なし」も重要な発見。**正面から議論する**。

### ✅ GOOD

```
"No interaction between <要因1> and <要因2> was detected for 
<従属変数>, failing to support <該当仮説>. This suggests that 
<要因1> and <要因2> have independent additive effects, with no 
synergistic (multiplicative) effect, and one factor does not 
modulate the effect of the other.

One possible reason for the lack of interaction is that <要因1> 
and <要因2> may contribute to <現象> through independent pathways. 
<理論的フレームワークの引用と説明>~\cite{...}. In this framework, 
<関連原理の説明>. Similarly, <本研究の文脈> may be weighted 
independently and integrated additively."
```

ポイント：
- 「failing to support H3」と仮説との対応を明示
- 「suggests」「may」で断定を避けつつ、理論的フレームワークで説明
- 引用は慎重に（引用する理論の内容を正しく述べる）

## ❌ Anti-patterns

### Anti-pattern 1: Resultsのコピー

```
BAD:
"F=42.33, p<.001 for <要因1>. F=22.16, p<.001 for <要因2>."
```

→ Resultsで既に書いたこと。Discussionではメカニズムと解釈を書く。

### Anti-pattern 2: 過度な断定

```
BAD:
"This proves that <要因> is the primary driver of <現象>."
```

→ 1つの研究で prove はできない。"suggests", "indicates", "is consistent with" を使う。

### Anti-pattern 3: 引用の誤解釈

```
BAD:
"This contrasts with [A] who showed <Xの効果>..."
(実際には整合的 or Xの話ではない)
```

→ 必ず引用元の主張を確認してから書く。自分の結果に都合よく解釈しない。

### Anti-pattern 4: 仮説との対応の欠如

```
BAD:
"We found interesting effects."
```

→ 「H1 を支持した」「H3 を支持しなかった」を明示。

### Anti-pattern 5: 先行研究を全く参照しない

```
BAD:
(本研究の結果だけを述べて他の文献に言及しない)
```

→ Discussion は「本研究が既存の知識体系の中でどこに位置するか」を示す章。
   先行研究との対比・整合・拡張を必ず議論。

## 付随的発見の扱い

主要仮説以外の発見（副次的測定、予期しない観察）も独立した subsection で議論：

```
\subsection{Effects on <付随的現象>}
[事実の要約]
[先行研究との位置関係]
[メカニズム仮説]
[主要発見との関連性]
```

これにより、査読者が「副次的発見は議論されていない」と指摘する余地がなくなる。

## チェックリスト

- [ ] Resultsで述べた事実を単に繰り返すのではなく、メカニズム/解釈を書いているか？
- [ ] 各仮説について supported / not supported を再確認したか？
- [ ] 引用している先行研究の主張を、Abstractまで確認して正確に述べているか？
- [ ] "consistent with" / "contrasts with" / "extends" の使い分けを慎重に行ったか？
- [ ] 交互作用がない場合、それを独立した発見として正面から議論しているか？
- [ ] 断定を避け、"suggests", "indicates", "may" 等の慎重な表現を使っているか？
- [ ] 理論的フレームワークを少なくとも1つ参照しているか？
- [ ] Main effects / Interaction / 付随的発見 の3つがそれぞれ subsection になっているか？
- [ ] 主要結果だけでなく、副次的発見も議論しているか？

## 実行時の手順

1. 既存のDiscussionがあれば、Results との重複をチェック（Resultsの繰り返しは削除）
2. 引用されている先行研究それぞれについて、その論文のAbstractを確認
3. "contrasts with" / "consistent with" の使い分けを再検証
4. 仮説との対応が明示されているか確認
5. メカニズム説明が欠けている箇所を補う
6. 副次的発見が省略されていないか確認

## 関連Skill

- `mspw-results`: Resultsで事実を述べ、Discussionでは解釈
- `mspw-design-implications`: DiscussionとDesign Implicationsは切り分ける
- `mspw-writing-principles`: 引用の正確な解釈、曖昧語の回避
