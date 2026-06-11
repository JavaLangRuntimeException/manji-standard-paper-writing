---
id: NNNN
title: <変更の短いタイトル>
status: draft   # draft -> approved -> implemented
created: <YYYY-MM-DD>
author: paper-spec-writer
related-specs: []
---

# NNNN: <タイトル>

## 1. 背景・動機

<なぜこの変更が必要か。ユーザー要求・査読コメント・発見された不整合など>

## 2. 現状

<対象箇所の現状。必ず実ファイルに基づいて書く>

- `paper.tex:L120-L145` — <現状の要旨 or 短い引用>

## 3. 変更内容(差分)

### 3.1 <章/ファイル名>

- **Before**: <現状の論旨 or 引用>
- **After**: <変更後の論旨 or 文案>
- **理由**: <根拠。リサーチ結果や skill 原則への参照>

### 3.2 <次の変更箇所>

...

## 4. 波及・整合性チェック

- [ ] Abstract ↔ Intro contributions の一致
- [ ] 用語統一(CLAUDE.md の用語表)
- [ ] 相互参照(\ref / \cite)の破損なし
- [ ] <この変更固有の波及項目>

## 5. リサーチ結果(出典付き)

| # | 問い | 回答要旨 | 出典 | 確度 |
|---|------|---------|------|------|
| R-1 | <...> | <...> | <URL / DOI / bibkey> | verified / likely / unverified |

## 6. Open questions

| # | 質問 | 解決経路 | 状態 |
|---|------|----------|------|
| OQ-1 | <...> | researcher / user | open / resolved: <結論> |

## 7. 受け入れチェックリスト

- [ ] <実装後に検証可能な条件>
- [ ] 原稿の grep で `[OQ` が 0 件
- [ ] writing-principles の mechanical pass 通過

## 8. 実装メモ(implemented にする時に記入)

- 実装日:
- 実際の変更:
- spec からの逸脱:
