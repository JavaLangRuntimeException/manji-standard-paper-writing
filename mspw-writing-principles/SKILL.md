---
name: mspw-writing-principles
description: Cross-cutting English academic writing principles that must be applied across EVERY section of a paper. This is a companion skill — use it in addition to section-specific skills (mspw-abstract, mspw-introduction, etc.) whenever the user is writing ANY part of an academic paper. Triggers on any paper-writing request in English, plus explicit mentions of "proofread", "polish", "check my writing", "grammar", "wording", "英文校正", "推敲", "文章チェック". This skill enforces: (1) replacing vague superlatives ("highest", "best") with comparatives when the comparison set is implicit, (2) precise interpretation of cited works, (3) explaining unfamiliar technical terms, (4) preserving the main claim when editing, and (5) balancing redundancy against reviewer-navigation needs.
---

# Academic Paper Writing Principles (Cross-cutting)

論文執筆の全章に共通する英文スタイル・論理の原則集。

**このSkillは他の章Skill（mspw-abstract等）と併用**してください。

---

## 原則1: 曖昧な最上級を避ける

### ❌ Anti-pattern: "highest", "best", "greatest" を無限定で使う

```
BAD:
"<手法X> provides the highest <指標A> but also causes the greatest 
<副作用B>."
```

問題：「何の中で最も〜なのか」が不明瞭。全ての既存手法の中？本研究で比較した範囲の中？

### ✅ 解決策1: 比較級にする

```
GOOD:
"<手法X> provides higher <指標A> but also causes greater <副作用B>."
```

### ✅ 解決策2: 比較対象を明示

```
GOOD:
"Among <method1>, <method2>, and <method3>, <method1> provided 
the highest <指標A>."
```

### ルール

- 比較対象が暗黙的 → 比較級（higher, greater, more）
- 比較対象を明示可能 → "highest among X, Y, Z"
- **単なる称賛** → "strong", "substantial" 等の絶対表現

### grep で発見すべきパターン

- `the highest` / `the best` / `the greatest`
- `最も〜` (日本語ドラフトから訳すとき)
- 形容詞のsuperlativeで比較対象の明示がないもの

---

## 原則2: 引用の正確な解釈

### ❌ Anti-pattern: 先行研究を自分の論旨に都合よく誤読

```
BAD:
"This finding contrasts with the previous study~\cite{A}, which 
demonstrated that <推測で書いた内容>."
```

記憶ベースで引用を書くと、実際の先行研究の主張と乖離することがよくある。
例えば "enhanced" と書くべきところを "reduced" と書いてしまう、
あるいは対照的でない結果を "contrasts with" と書いてしまう等。

### ✅ 解決策: 引用元の主張を確認してから書く

```
GOOD:
"This finding is consistent with the previous work~\cite{A}, which 
showed that <[A] が実際に示したこと>.
Our results further suggest that, <本研究の条件下では>, such 
<[A] の効果> can also <追加的な影響>."
```

### 引用動詞の使い分け

| 動詞 | 意味 | 使うとき |
|------|------|---------|
| "consistent with" | 整合的 | 本研究の結果が既存研究を支持・補強 |
| "contrasts with" | 対照的 | **本当に**反対の結果・主張がある |
| "extends" | 拡張 | 既存研究の範囲を拡げる |
| "builds on" | 基盤 | 既存研究を土台として新しい層を作る |
| "differs from" | 異なる | 手法や対象が違うだけで対立はしない |
| "complements" | 相補的 | 異なる側面を埋める |

### 実行時チェック

引用を入れる前に自問：
- [ ] その論文のAbstractを読んだか？
- [ ] 主張の方向（enhance か reduce か、増か減か）を正確に把握しているか？
- [ ] "contrasts" と書く前に、本当に対照的か？単に異なるだけではないか？

---

## 原則3: 専門用語を裸で使わない

### ❌ Anti-pattern: 引用元の用語を説明なしに持ち込む

```
BAD:
"...introducing load reduction measures such as the <引用先の固有名> 
proposed by <Author>~\cite{...}..."
```

読者が原著を読んでいないと、"<固有名>" が何をするものか分からない。

### ✅ 解決策: 用語を機能的に言い換え or 簡潔な説明を付ける

```
GOOD (機能で言い換え):
"...approaches that <目的を達成する機能的記述 (e.g., partially 
replicating the target's <特徴>)>~\cite{...}"
```

```
GOOD (用語 + 簡潔な説明):
"...the <固有名> (<1フレーズの機能説明>) proposed by <Author>~\cite{...}..."
```

### ルール

- 引用論文の用語を使うとき → 初出で1フレーズの機能説明をセット
- 複数の論文で異なる呼称がある概念 → 自分の論文で使う呼称を決めて一貫させる

---

## 原則4: 編集時にメインクレームを失わない

### ❌ Anti-pattern: 細部の修正に気を取られて主張が変わる

文章を書き直す過程で、もともと言いたかった主張（例：「AはBを高めるが同時にCを増やす」）が
書き換え後に別の主張（例：「Aの重要性」）に変わってしまうことがある。
特に協働者からのコメントを取り込む際に発生しやすい。

### ✅ 解決策: 編集前に主張を文書化

1. 編集前に「この段落/文で言いたい主張」を1文にまとめる
2. 編集後、改めて書いた文を読んで、その主張が残っているか確認
3. ズレていたら、細部表現は捨てて主張を優先

### 実行時の手順

文章を修正する前に：
```
この文章で主張したいこと:
「A と一致する特徴はXを高める一方で、同時にYも増加させる」

主張の骨格:
- 効果: X ↑
- 副作用: Y ↑
- スコープ: 特定の条件下で
```

これを書き留めてから編集することで、主張ぶれを防ぐ。

---

## 原則5: 冗長性と査読者ナビゲーションのバランス

### ジレンマ

査読者は論文を上から下まで読むとは限らず、Design Implications や Conclusion だけを読むこともある。
そのため、**冗長でも重要な情報は再掲する**ことで、査読者が拾い読みしても理解できるようにする。

一方、同一段落内の繰り返しや、直前に書いたことの再掲は「冗長」として削除するべき。

### 判断基準

**冗長でも残す**ケース：
- 離れたsection間の参照（例：手法章の条件定義をResults章で再掲）
- 査読者が拾い読みする可能性が高い箇所（Design Implications、Conclusion）
- 強調したいキーフレーズ

**冗長なら削る**ケース：
- 同一段落内での即時的な繰り返し
- すでに直前の段落で明示した前提
- 読者の作業記憶で保持できるスコープ

### 実行時のガイド

- セクション冒頭と末尾は、**冗長でも要約を置く**（査読者は途中から読むかもしれない）
- セクション中盤は、**冗長を削って流れを優先**

---

## 原則6: 比較級には必ず比較対象を

### ❌ Anti-pattern: 浮いた比較級

```
BAD:
"<条件A> produced higher <従属変数>."
```

### ✅ 解決策: "than X" を必ず付ける

```
GOOD:
"<条件A> produced higher <従属変数> than <条件B>."
```

比較対象が文脈で強く暗黙なら省略可だが、特に Results 章では徹底する。

---

## 原則7: 研究分野の専門用語を混同しない

同じ領域で似た用語があり、包含関係や意味が異なる場合、**論文内で一貫して使う**。

### 型の例

```
上位概念 X
├── 下位概念 X-a
├── 下位概念 X-b
└── 下位概念 X-c
```

- Abstract/Introで「X」全体の効果を主張したなら、Discussionで「X-a」に限定した話にすり替えない
- 論文全体で、同じ現象に対して複数の用語を混在させない（最初に決めて統一）

### よく混同されがちなペア（分野問わず）

- 客観的測定値 vs 主観的申告
- システム側の性質 vs ユーザー側の感覚
- 状態（state）vs 特性（trait）
- 短期効果 vs 長期効果

論文内で用語を選ぶとき、**自分の研究領域のreviewer/senior researcherが使う区別** を確認してから統一。

---

## 原則8: "suggest" vs "demonstrate" vs "show" vs "prove"

断定の度合いで使い分ける：

| 動詞 | 強さ | 使う場面 |
|------|------|---------|
| "prove" | 最強 | 数学的証明のみ。実験研究では基本使わない |
| "demonstrate" | 強 | 統計的に有意＆effect size大＆replication済 |
| "show" | 中 | 統計的に有意 |
| "suggest" | 弱 | 傾向あり、有意でないか、メカニズム仮説 |
| "indicate" | 中弱 | データが示唆する程度 |

Discussion のメカニズム説明では "suggest"、Results の主効果報告では "show" が基本。

---

## 原則9: 受動態と能動態の使い分け

多くの国際学会（HCI系、VR系、ACM/IEEE系）では **能動態（We ...） を優先** する傾向：

```
✅ GOOD (能動態):
"We conducted a within-subjects experiment..."
"We found that..."
"We argue that..."

❌ AVOID (無駄な受動態):
"A within-subjects experiment was conducted..."
"It was found that..."
```

ただし以下のケースでは受動態が自然：
- 実験参加者の操作（"Participants were asked to...", "The apparatus was calibrated...")
- 測定値を主語にする場合（"<従属変数> was measured using...")

---

## 原則10: "However" / "Therefore" を濫用しない

```
BAD (過度な接続詞):
"The result was significant. However, the effect size was small. 
Therefore, we need more data. However, we also need..."
```

- **However** は**1段落に1回まで**。文章の重要な転換点に使う。
- **Therefore** は論理的帰結が明確なときのみ。感覚的な「だから」には使わない。

代替案：
- "However" → "Yet", "Still", "Nonetheless", または単に新しい文で対比
- "Therefore" → "This suggests", "As a result", "Consequently"

---

---

## 原則11: 先行研究の知見を書くときは attribution を明示する

### ❌ Anti-pattern: 浮いた事実、誰の発見かが不明瞭

```
BAD:
"Imitating another's posture enhances the sense of embodiment."
```

読者は「これは本研究の主張？それとも既存知見？」と迷う。
引用だけ付いていても、誰が・どこで示したかが埋没する。

### ✅ 解決策: 出典を文の主語または節にする

```
GOOD:
"Schultz et al.~\cite{...} showed in their study that imitating 
another's posture enhances the sense of embodiment."
```

または：

```
GOOD:
"Prior work has demonstrated that imitating another's posture 
enhances the sense of embodiment~\cite{...}. In their study, 
participants <具体的な条件> reported <結果>."
```

### ルール

- Related Work / Discussion で既存知見を述べるとき → 著者名を主語にする、または "in their study"
  のような句を入れて attribution を明示
- 特に「先行研究はXを示した」と自分の発見のように書き始めてしまう文はreviewerに指摘される
- 引用だけ `\cite{...}` を末尾に置く形式は、どの情報がどこから来たかを曖昧にする

---

## 原則12: ドメイン環境（VR / AR / 実環境）は常に明示する

### ❌ Anti-pattern: 環境の曖昧さ

```
BAD:
"When users move their body, a strong sense of embodiment emerges."
```

これは VR 環境の話なのか、実世界のmotion studyの話なのか、ウェアラブル機器の話なのか不明。

### ✅ 解決策: 初出で環境を明示

```
GOOD:
"In VR, when users move their body with a tracked HMD, a strong 
sense of embodiment emerges."
```

### ルール

- 論文のドメインが VR / AR / MR / 実環境シミュレーター等のいずれかである場合、
  **現象を記述する文の初出では必ず環境名を含める**
- 以降の文は文脈で省略可だが、章が変わる（Related Work → Method など）ときは再掲
- 特に Related Work では「この知見は VR 内のものか、実世界の心理学研究か」を常に明示

---

## 原則13: Section / Subsection の冒頭にはブリッジ文を置く

### ❌ Anti-pattern: 突然の箇条書き / 技術詳細

```
BAD:
\subsection{Gestures}
\begin{itemize}
  \item Walking
  \item Swinging
  \item Rowing
\end{itemize}
```

読者は「なぜこの3つ？」「何のために読むのか」が分からない。

### ✅ 解決策: 1文で繋ぐ

```
GOOD:
\subsection{Gestures}
To cover a range of locomotion styles from <軸1> to <軸2>, we 
selected three gestures: walking, swinging, and rowing.
\begin{itemize}
  \item Walking ...
```

### ルール

- **section / subsection / paragraph の冒頭は "なぜこの節を読むのか" を1文で示す**
- 箇条書き、表、図の直前には常に1文の導入を置く
- 新しい章の最初の段落は、前章からの連続性と本章の目的を繋ぐ

---

## 原則14: Figure は新情報を持たなければ削除/再設計

### ❌ Anti-pattern: 本文と同じ情報の図

```
BAD:
(本文に「3つの条件 C1, C2, C3 を比較」と書いてあり、
 図にも「C1, C2, C3」の箱が3つ並んでいるだけ)
```

### ✅ ルール

- Figure はその caption を読んだだけで「この図から何が分かるか」が明確になるべき
- 本文で既に述べた関係の再掲のみの図は削除 or 情報を足す
- System overview 図であれば、本文で言及しない要素（hardware配置、信号フロー、寸法）を含める

---

## 原則15: 時制の一貫性

### ルール

- **本研究で行ったこと**: 過去形 (We conducted..., We measured...)
- **先行研究の知見**: 現在完了 or 過去形 (Prior work has shown..., Smith showed...)
- **一般的な現象/定義**: 現在形 (Embodiment emerges when..., The system uses...)
- **図表の説明**: 現在形 (Figure 3 shows..., Table 2 summarizes...)

同一段落内で時制が混ざる場合、意味的に意図したものか確認する。

---

## 原則16: 提出前の mechanical final pass

投稿直前に **必ず** 実行する機械的チェック：

### (a) 壊れた相互参照

```
grep 対象: "??"  (PDF レンダリング後に出るパターン)
LaTeX ソース grep 対象: \ref{未定義ラベル}, \cite{未登録キー}
```

相互参照が `??` になっていると reviewer は即座に気づく。投稿前に PDF を全ページ目視する。

### (b) Anonymization

Double-blind の学会では、以下を grep で洗い出し：

```
- 所属機関名（大学名、研究室名）
- 著者名（自分・共著者）
- 既発表論文の自己引用を著者名ありで引用していないか
- 謝辞（funding source、人名）
- プロジェクト名、システムの固有名（社内コードネーム）
- GitHub/URL（個人リポジトリへのリンク）
```

### (c) 文字落ち / 接続詞不整合

```
- "combining ." のように動詞の目的語が消えている
- "However,  ." のように空の接続
- LaTeX マクロ展開の失敗 (\Cref{...} が \ref{} になっていない等)
```

### (d) 時制 / 用語統一の最終確認

```
- 同じ用語の綴り揺れ（"embodiment" vs "embodiement"）
- 条件名の略称混在（"C1" と "Condition 1"）
- 略語の初出定義があるか（INS, SoE, SoBO など）
```

---

## チェックリスト（全章共通）

論文全体のどの章を仕上げた後も実行：

- [ ] "highest" / "best" / "greatest" を grep → 比較級 or 比較対象明示に修正
- [ ] 比較級（"higher", "greater"）が全て "than X" 付きか確認
- [ ] 引用している先行研究の主張を本当に正しく述べているか（特に "contrasts with"）
- [ ] 先行研究の知見に対して著者への attribution が明示されているか（"in their study" 等）
- [ ] 専門用語・略語を裸で使っていないか（初出で説明があるか）
- [ ] ドメイン環境（VR / AR / 実環境）が初出で明示されているか
- [ ] Section / Subsection の冒頭にブリッジ文があるか（いきなり箇条書き/表にしていないか）
- [ ] Figure が新情報を持っているか（本文の繰り返しでないか）
- [ ] 時制が一貫しているか（本研究=過去、先行研究=現在完了/過去、一般現象=現在）
- [ ] "prove" を使っていないか（ほぼ必ず "show" or "suggest" に置換）
- [ ] 受動態の乱用はないか（能動態にできるか）
- [ ] "However" が1段落に2回以上出ていないか
- [ ] 論文内で用語統一ができているか（上位概念と下位概念の混同なし）

### 投稿直前の mechanical pass（原則16）

- [ ] PDF に `??` が出ていないか（全ページ目視）
- [ ] LaTeX ソースの `\ref{}`, `\cite{}` が全て解決しているか
- [ ] Anonymization: 所属機関名 / 著者名 / GitHub URL / funding を grep
- [ ] 文字落ち・空の接続詞がないか（"combining ." "However, ."）
- [ ] 略語が全て初出で定義されているか

## 実行時の手順

1. ユーザーから受け取った文章について、まず上記チェックリストを順に当てる
2. ヒットした箇所を列挙
3. 各箇所の修正案を提示
4. 原則番号（例: 原則1, 原則3）を明示して「なぜ修正が必要か」をユーザーに説明
5. 主張（原則4）がぶれていないか、最後に全体を見直す

## 関連Skill

この原則Skillは、全ての章Skill（mspw-abstract〜mspw-conclusion）と併用されることを想定。
各章Skillがセクション固有の構造を扱い、このSkillが横断的な英文スタイルを扱う。
