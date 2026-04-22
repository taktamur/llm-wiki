---
type: concept
updated: 2026-04-22
sources: [../sources/blog-hayatetakeda.md]
aliases: [アーカイブ原則, 意思決定アーカイブ, 化石層]
---

# decision-archive（アーカイブ原則）

[[hayatetakeda|Hayate Takeda]] が [[blog-hayatetakeda]] で提唱した [[llm-wiki]] 設計原則。**意思決定ログは書き換えない。新判断は新ページを作り `superseded_by` で連鎖する**。Wiki に「**思考の化石層**」を残す設計思想。

## 動機

[[karpathy-gist]] の原典設計は「Wiki を常に最新に保つ」方針だが、これは**過去の自分の意思決定を上書きしてしまう**：

- 過去判断と現在判断は異なる文脈から出る
- 「常に最新」は過去の自分を消す
- 決断の理由（当時の制約・価値観）が失われる

## 原則

1. **書き換え禁止**: `Decisions/` 配下のページは一度作成したら本文を上書きしない
2. **新判断は新ページ**: 方針変更時は新ページを作る
3. **`superseded_by` で連鎖**: 新ページの frontmatter で旧ページを参照
4. **`date` は決断日で固定**: 「更新日」ではなく「決断日」のため不変

## Frontmatter 例

```yaml
---
title: "2026-07 転職先選定"
type: decision
date: 2026-04-11           # 決断日で固定（更新しない）
status: active             # active | superseded
superseded_by: null        # 後続判断で更新される
context_snapshot: "SES2年目、SA志向が固まった時期"
options_considered:
  - "フリーランス"
decision: "Sierへの転職決断"
---
```

後継判断が出た場合：

```yaml
---
title: "2027-03 転職先選定（再検討）"
type: decision
date: 2027-03-05
status: active
superseded_by: null
supersedes: "2026-07_転職先選定"   # 旧ページへの逆リンク
---
```

旧ページ側は `status: superseded`, `superseded_by: "2027-03_転職先選定"` に更新する（**これは書き換えではなくメタデータ更新**）。

## 効果

- Wiki が「**思考の化石**」と「**現在の地図**」の両方として機能する
- 過去判断が「間違いだった」と否定されず、当時の合理性が残る
- 自分の意思決定の変遷を時系列で追える
- [[model-collapse]] の LLM 自己参照劣化への部分的対策（過去判断が上書きされない）

## 適用範囲

- 必須: `Decisions/` 領域
- 任意: 重要な設計文書・ADR（Architecture Decision Record）的な記録
- 不要: `Knowledge/` 領域（事実・技術情報は最新化して OK）

## 既存概念との関係

- **ADR**（Architecture Decision Record）: ソフトウェア設計領域で既知のパターン。本原則はその個人知識版
- [[rohitg00-v2-gist]] の **supersession**: 片方向の supersede リンクを持つ点は類似。ただし rohitg00 は knowledge にも適用、本原則は decision に限定

## 本 Vault への含意

本 Vault の [[log.md]] が近い機能を果たしている：
- ingest/query/lint の判断をタイムスタンプ付きで追記型記録
- 過去の log エントリは書き換えない（append-only）

ただし [[log.md]] は混在ログであり、意思決定だけの層ではない。将来的に `wiki/decisions/` を新設する余地はある（現状は不要）。

## 関連ページ

- [[llm-wiki]] — 親パターン
- [[self-os]] — 本原則を内包する拡張パターン
- [[blog-hayatetakeda]] — 原典
- [[hayatetakeda]] — 提唱者
- [[model-collapse]] — 対策として機能する側面
- [[rohitg00-v2-gist]] — supersession 概念の類似
