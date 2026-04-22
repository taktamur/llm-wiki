---
type: concept
updated: 2026-04-22
sources: [../sources/blog-hayatetakeda.md]
aliases: [自分OS, Self-OS, 3領域スキーマ, 内向きWiki]
---

# self-os（自分OS）

[[hayatetakeda|Hayate Takeda]] が [[blog-hayatetakeda]]（2026-04-19）で提示した [[llm-wiki]] 拡張パターン。[[karpathy-gist]] の「外向き設計」（外部知識の整理）に**「内向き＋外向き」3 領域構造**を追加し、自分コンテキスト（意思決定・行動パターン）も wiki に統合する。

## Karpathy 設計との差分

| 観点 | [[karpathy-gist]] | self-os |
|------|------------------|--------|
| 扱う情報 | 外部知識のみ（エンティティ・概念） | 外部知識 + 意思決定 + 行動パターン |
| 時系列性 | 「最新を保つ」前提 | **時系列を保存**（アーカイブ原則） |
| 自動化レベル | 一律 | 領域ごとに段階化 |
| 層 | raw → wiki の 2 層 | Raw → Sources → Wiki の 3 層 |

## 3 領域 + 1 メタ層

| 領域 | 蓄積内容 | 方向性 | 自動化レベル |
|------|---------|--------|-------------|
| `Knowledge/` | 技術・概念・外部情報 | 外向き | 自動 |
| `Decisions/` | 意思決定ログ | 内外混合 | 確認後・**書き換え禁止** |
| `Self/` | 思考・行動パターン | 内向き | 提案のみ |
| `Synthesis/` | 3 領域の交差点（メタ層） | メタ | Phase 2 以降 |

## 3 層（Raw / Sources / Wiki）

```
Raw/       → 生ソース・不変
Sources/   → 整形ソース（中間層）
Wiki/      → 知識統合層・LLM 完全管理
```

`Sources/` 中間層を挟むのが本パターンの特徴。Raw をそのまま Wiki に取り込むのではなく、**一度構造化した中間形式を経由**する。`Sources/` 配下は `Wiki/` と同じ入れ子構造をミラー。

## 各ジャンルの 3 点セット

各ジャンル配下に `_index.md` / `_log.md` / `_overview.md` を置く。LLM が次のセッションで呼ばれたとき、この 3 ファイルだけで「今 Wiki がどういう状態か」を把握できる設計。

```
Wiki/Knowledge/rdb/
  ├── _index.md       # ジャンル内のページカタログ
  ├── _log.md         # ジャンル別ログ
  ├── _overview.md    # ジャンル全体の要約
  └── SELECT最適化.md # 実ページ
```

[[two-layer-index]]（[[taroumegane]] 提唱）と類似するが、**`_overview.md` を追加**している点が差分。

## 領域別設計原則

### Knowledge/ — 外向き、自動処理

- 新規作成・追記・矛盾指摘は自動
- 書き換えは diff 表示 → 承認

### Decisions/ — [[decision-archive]] 原則

- **書き換え禁止**
- 新判断は新ページ、`superseded_by` で連鎖
- `date` は決断日固定

詳細は [[decision-archive]] 参照。

### Self/ — 自己成就的予言の回避

最もセンシティブな領域。パターン抽出を自動化すると LLM が「あなたはこういう人」を固定化し、Wiki が**自己成就的予言**になる危険。

**ハイブリッド運用**:

```
朝: 2分の自由記述（フレームなし）← 観測の生データ
夜: 構造化質問（朝の記述踏まえ）← 解釈と接続
```

自由記述で観測データを取り、構造化質問で解釈する非対称性が効く。

### Synthesis/ — 3 領域の交差点

Phase 2 以降に追加するメタパターン層。[[karpathy-gist]] にない独自層。データが溜まってから導入する。

## Pattern ページの信頼度管理

```yaml
---
title: "0→1集中型"
type: pattern
first_observed: 2024-06-01
last_observed: 2026-04-10
observation_count: 12
confidence: medium       # low | medium | high
status: active
evidence:
  - "2026-07_転職先選定"
  - "2025-12_ISMS推進着手"
---
```

- `confidence: high` は**観測 10 回以上かつ異なる状況で一貫**が条件
- 3 週間分溜まるまで `Patterns/` 抽出は行わない

[[memory-lifecycle]] の confidence 概念と親和的だが、`observation_count` の具体的閾値が追加されている。

## Phase 1 最小セット（1 週間で動かす）

1. `SKILL.md`（ingest / query / morning / evening の 4 コマンド）
2. `schema/knowledge-template.md`
3. `schema/decision-template.md`
4. `Wiki/<genre>/_index.md`
5. `Wiki/<genre>/_log.md`

あえて含めない:
- `Synthesis/`（データ蓄積後）
- lint（人間目視先行）
- Patterns 抽出（3 週間後）

## 本 Vault への含意

本 Vault は「llm-wiki という考え方」そのものが題材の**メタ wiki** のため：

- **Knowledge/ 相当**: 現行の `wiki/{concepts,entities,sources}/` がこれに該当
- **Decisions/ 相当**: ingest 時の判断記録（どのソースを採用したか、命名、cross-reference 方針等）は現状 `log.md` に混在。将来的に切り分ける余地あり
- **Self/ 相当**: 本 Vault では対象外（個人の思考パターンは題材外）
- **Synthesis/ 相当**: ソースを跨いだ比較・総合は既に `wiki/concepts/` に書き得ている（例: [[llm-wiki]] の実装バリエーション節）ため専用層は不要

## 関連ページ

- [[llm-wiki]] — 拡張元
- [[blog-hayatetakeda]] — 原典
- [[hayatetakeda]] — 提唱者
- [[decision-archive]] — 本パターンの下位原則
- [[two-layer-index]] — 類似の階層化パターン
- [[memory-lifecycle]] — confidence 概念で親和
- [[rohitg00-v2-gist]] — 別方向の拡張（knowledge-graph / lifecycle）
