---
name: mspw-method
description: Use this skill whenever the user is writing the Method, System, Experimental Setup, Apparatus, or Implementation section of a paper — the chapter that describes the technical system or platform built before running the user study. Triggers include "Method", "Experimental Setup", "System", "実装", "手法", "システム概要", "装置", "3章", or "setup". This section should justify design decisions (not merely describe them), explicitly link the system design back to the research gap from the Introduction, and set up the independent variables that the subsequent user study will manipulate.
---

# Paper Method / Experimental Setup Writing

学会論文のSystem / Experimental Setup / Method章を書くときに使うSkill。

## この章の目的

この章は「何を作ったか」を書くのではなく、**「なぜそれが本研究に適しているか」を示す**章。
後続のUser Study章が成立するために必要な条件をすべて満たすシステム/装置を、
**設計思想（rationale）と共に**説明する。

## 必須の構造

```
\section{Experimental Setup} (または System, Method)

[冒頭接続段落]
  - Related Workで述べたギャップを再掲
  - それを埋めるために、どういう条件を満たすシステム/装置が必要か
  - そのために何を選択・実装したか（章の概要）

\subsection{設計選択1 (例: 対象の選定)}
  - Requirements（箇条書き）
  - 候補に対する評価
  - 選択理由（根拠＋引用）

\subsection{設計選択2 (例: 入力インターフェース)}
  - 条件の数だけ実装方式を示す
  - 各実装の詳細（閾値、パラメータなど）
  - パラメータ決定の根拠（pilot testing / 先行研究）

\subsection{共通部分 (例: Rendering, Feedback)}
  - 全条件で共通する技術要素
```

## 冒頭接続段落の型

```
"As discussed in Section~2, prior work has shown that <軸A> and 
<軸B> each <効果>, but <AとBの組み合わせ / 境界条件> remains 
unexplored. Our goal is to <具体的な操作> while independently 
varying these two factors to measure their effects on <従属変数>.
To this end, we need a system where 
(1) <要件1>, (2) <要件2>, and (3) <要件3>.
Rather than proposing a novel <手法のカテゴリ>, we built a simple 
experimental platform using established components (<要素1>, 
<要素2>) to isolate the perceptual/behavioral variables of interest."
```

ポイント：
- Related Workのギャップを再掲して連続性を保つ
- 「必要な条件」を番号付きで列挙
- **「novelな手法を提案するのではなく、既知の要素を組み合わせた experimental platform だ」と明言**
  （これにより查読者に「新規性はここではなく知見の方」と示せる。手法の新規性を過剰に主張しない）

## 設計選択の説明パターン: Requirements → Candidates → Decision

各設計判断について、**要件 → 候補 → 決定理由** の順で書く：

```
\subsection{<設計対象>}
We chose <決定結果> based on the following requirements:
\begin{itemize}
    \item \textbf{<要件1>:} <要件の具体内容>.
    \item \textbf{<要件2>:} <要件の具体内容>.
\end{itemize}

[数値/理論的根拠]
<要件を満たすための定量的根拠>~\cite{...}.

[代替案の却下理由]
While <代替案> would also satisfy <要件1>, it fails to <理由>, 
making it unsuitable for this study.
```

ポイント：
- Requirementsは必ず **具体的 & 測定可能**
- 各Requirementに対して数値または理論的根拠で裏付け
- **「なぜ他の候補ではダメか」を明示的にでも暗黙的にでも示す**

## パラメータ決定の根拠

速度閾値、サンプリング周波数、提示時間など具体的パラメータは、
**pilot testing または先行研究** に基づいて決定したと明記する。

### ✅ GOOD

```
"A <イベント> was detected when <条件> exceeded a threshold of 
<値>. Each <イベント> initiated <応答>. <閾値の根拠>: this value 
corresponds to <先行研究の範囲>~\cite{...}, and was further 
validated through pilot testing with <N人の予備参加者>."
```

### ❌ Anti-pattern: 根拠なき値

```
BAD:
"We used a threshold of 0.5."
```

→ なぜ0.5なのか、他の値でないのか、の根拠が必要。

## ❌ Anti-patterns

### Anti-pattern 1: 作ったものをただ列挙

```
BAD:
"We used hardware X. We used software Y. We used library Z..."
```

→ 何のためにそれを選んだか分からない。設計判断の根拠を常にセットに。

### Anti-pattern 2: Noveltyを過剰に主張

```
BAD:
"We propose a novel <手法カテゴリ>..."
```

→ System/Method章で技術を前面に出すと、研究の主張点（知見）がぼやける。
   「established components を組み合わせた platform」とクリーンに書く方が誠実で、
   査読者にも本研究の新規性（知見）が伝わりやすい。

### Anti-pattern 3: User Studyの詳細を混ぜる

```
BAD:
"We tested N participants..."
```

→ それは User Study章の話。この章では装置・システムに専念する。

### Anti-pattern 4: 各条件間の差異が不明瞭

```
BAD:
"We implemented several versions of the system."
(複数の条件を作ったが、それらの差異が明示されていない)
```

→ User Studyで操作する独立変数と対応する形で、**条件ごとに独立したparagraphや subsection で** 差異を明示。

### Anti-pattern 5: 特定のハードウェアで記述してしまう

```
BAD (System章):
"Users grab and release the Meta Quest 3 controller to initiate 
the swinging gesture."
```

問題：System/Method 章で具体的なハードウェア名を出すと、
(a) 手法の一般性が狭くなる、(b) 他のHMDでも再現できることが伝わらない、
(c) User Study 章との重複が増える。

### ✅ 解決策: インタラクションは抽象化、ハードは User Study で指定

```
GOOD (System章):
"Users initiate the swinging gesture by grasping and releasing 
with their hand."

GOOD (User Study章):
"Participants used a Meta Quest 3 (Meta Inc., USA) with its 
controllers to perform the gestures."
```

### ルール

- **System / Method 章**: 一般的なインタラクション概念で記述
  （"the user's hand", "a tracked controller", "a handheld device"）
- **User Study / Experimental Setup 章**: 使用したハードウェアの型番・メーカーを明記
- Apparatus 情報（Meta Quest 3, Unity version, etc.）は User Study 章にまとめる

## チェックリスト

- [ ] 冒頭で前章のギャップを再掲し、それを埋めるための必要条件を列挙したか？
- [ ] 各設計選択について「要件 → 候補 → 決定理由（根拠付き）」の構造になっているか？
- [ ] パラメータ値（閾値、速度など）に pilot test または先行研究からの根拠があるか？
- [ ] User studyの話（参加者、Nなど）が混ざっていないか？
- [ ] 「novelな手法の提案」ではなく「perceptual/behavioral variableをisolateするplatform」
      という位置付けが明確か？
- [ ] 条件間で共通する部分が独立して書かれているか？
- [ ] 条件ごとの差異（User Studyで操作する独立変数）が明示されているか？
- [ ] インタラクションが抽象概念（"the user's hand" 等）で記述され、具体的なハードウェア名（Meta Quest 3等）は User Study 章に回されているか？

## 関連Skill

- `mspw-user-study`: この章で定義した独立変数をどう操作したか
- `mspw-writing-principles`: 曖昧な最上級を避け、数値で具体化
