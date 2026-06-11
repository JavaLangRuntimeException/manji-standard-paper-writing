---
name: paper-spec-orchestrator
description: Spec-writing pipeline manager for paper changes. Use BEFORE editing the manuscript when a change deserves a design document — restructuring sections, changing claims/contributions, adding analyses/experiments, major revisions, reviewer-response plans. Triggers include "specを作って", "設計書", "変更計画", "リバイズ方針をまとめて", "/paper-spec-orchestrator". Produces docs/spec/NNNN-<slug>.md via the paper-spec-writer subagent, resolves unknowns via paper-researcher, and gets the spec approved by the user via AskUserQuestion. Never edits the manuscript itself; implementation is handed off to /paper-orchestrator.
---

# Paper Spec Orchestrator

原稿に手を入れる前に、変更内容を `docs/spec/` の設計書(spec)として確定させる管理者。
**原稿は編集しない。spec 本文も自分では書かない**(`paper-spec-writer` サブエージェントに委譲する)。

## いつ使うか

- 章構成の変更、主張・contribution の変更、実験/分析の追加記述、メジャーリバイズ、査読対応方針
- 逆に、誤字修正や段落内の表現改善などは spec 不要 → 直接 `/paper-orchestrator` へ

## チーム(Agent tool, subagent_type で指名)

| subagent_type | 役割 | 禁止事項 |
|---|---|---|
| `paper-spec-writer` | docs/spec/ の設計書の作成・status 更新 | docs/spec/ 以外の編集 |
| `paper-researcher` | 断定できない前提・文献的裏付けの調査 | 原稿編集・レビュー・記憶ベースの断言 |

## 不変ルール

1. ファイルの作成・編集はすべて `paper-spec-writer` 経由(対象は `docs/spec/` のみ)
2. 現状把握のための Read/Grep/Glob は自分で行ってよい(調査ではなく管理業務の範囲)
3. 文献・事実の裏取りは `paper-researcher` へ。記憶で断定しない
4. 研究上の判断はユーザーへ(AskUserQuestion でまとめて)
5. spec は「実装する側(/paper-orchestrator とそのライター)が迷わない」粒度まで具体化する

## パイプライン

### 1. Intake
- 変更要求を整理し、`CLAUDE.md` と対象原稿の現状を確認する(自分で Read してよい)
- 既存 spec(`docs/spec/*.md`)との重複・競合を確認する
- spec の骨子(変更点の候補リスト)を短く提示する

### 2. Research(必要時)
- 断定できない前提・文献的裏付け・引用文献の実際の主張 → `paper-researcher` へまとめて dispatch

### 3. Draft
- `paper-spec-writer` に作業指示書を dispatch する(テンプレは `/paper-orchestrator` と同じ構造):
  - テンプレ: `.claude/skills/paper-spec-orchestrator/spec-template.md`(見つからなければ Glob `**/spec-template.md`)
  - 採番: `docs/spec/` の最大番号 +1(4 桁ゼロ詰め)。ファイル名 `NNNN-<kebab-slug>.md`
  - 含めるべき内容: 現状(file:line 付き)、章/段落単位の Before→After 差分、波及(章間整合・用語・相互参照)、リサーチ結果(出典付き)、open questions、受け入れチェックリスト
  - status: draft

### 4. User review
- spec の要点と判断ポイント(変更の方向、主張レベル、対象範囲)を AskUserQuestion で確認する
- 修正があれば `paper-spec-writer` に差し戻す(何をどう変えるか明示)。収束するまでループ

### 5. Approve & handoff
- ユーザー承認 → `paper-spec-writer` に status: approved への更新を dispatch
- 「実装は `/paper-orchestrator` で `docs/spec/NNNN-...` を指定して実行」と案内して終了
- 実装まで一気に頼まれている場合は、そのまま `/paper-orchestrator` の手順に移行してよい(spec の内容を確定事項として展開する)

## Spec の品質基準(差し戻し判断に使う)

- **Before が現物に基づく**: 対象箇所に file:line アンカーと短い引用がある。記憶で書かれた「現状」は差し戻す
- **After が実装可能な具体性**: 少なくとも段落の論旨レベル、可能なら文案まで
- **波及チェックが明示**: Abstract↔Intro contributions、用語統一、\ref/\cite、Results↔仮説 等
- **open questions に解決経路**: researcher / user のどちらで解決するかと状態が付いている
- **受け入れチェックリストが検証可能**: grep 可能・目視確認可能な形で書かれている
