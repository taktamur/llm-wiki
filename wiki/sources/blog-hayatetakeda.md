---
type: source
updated: 2026-04-22
url: https://zenn.dev/hayatetakeda/articles/e0254eeb4cd15f
author: Hayate Takeda
fetched: 2026-04-22
published: 2026-04-19
lang: ja
sources: ["../../raw/blog-hayatetakeda-self-os.md"]
aliases: [LLM Wiki 自分OS, 3領域スキーマ, Self-OS]
---

# Hayate Takeda: LLM Wiki を「自分OS」に昇華させた話 (2026-04)

Zenn 記事（2026-04-19）。[[andrej-karpathy]] の [[karpathy-gist]] を **「外向き（外部知識）の設計」** と批判的に受けとめ、[[hayatetakeda|Takeda]] が **「内向き＋外向き」の 3 領域スキーマ** に再構成した実装記録。Karpathy gist にない「**アーカイブ原則**」「**自己成就的予言回避**」「**Synthesis 層**」の 3 つを独自拡張として提示。

## このソースの位置付け

- 日本語圏で「[[karpathy-gist]] を設計批判的に継承」した最初期の事例
- 既存 [[taroumegane]] の実装がスキーマ粒度の運用改善（[[two-layer-index]] / [[page-decision-criteria]]）だったのに対し、本記事は**設計原理の拡張**で差別化
- [[rohitg00-v2-gist]] と並ぶ「gist 拡張案」の位置だが、方向性が違う: rohitg00 は memory-lifecycle / knowledge-graph（運用技術）、Takeda は領域分離 / 自分コンテキスト（人間観）

## Karpathy 設計の診断

著者は [[karpathy-gist]] の 3 層（raw / wiki / schema）を「外向き」と定義し、次を欠点とする：

- エンティティと概念の関係は精密に書けるが、**「いつ・どういう状態で考えたか」の時系列が残らない**
- 自分コンテキスト（当時の制約・価値観）が注入できないため、意思決定の再現性が低い
- Wiki が「最新を保つ」方針のため、**過去の判断が上書きされる**

## 3 領域 × 3 層スキーマ

### 領域分割

| 領域 | 蓄積内容 | 方向性 |
|------|---------|--------|
| `Knowledge/` | 技術・概念・外部情報 | 外向き |
| `Decisions/` | 意思決定ログ | 内向き × 外向き |
| `Self/` | 思考・行動パターン | 内向き |
| `Synthesis/` | 3 領域の交差点（独自メタ層） | メタ |

### 層分割

```
Raw/       → 生ソース・不変・LLMはread専用
Sources/   → 整形ソース・Raw を構造化・LLMはread専用（中間層）
Wiki/      → 知識統合層・LLM完全管理・自己増殖
```

**[[karpathy-gist]] との差分**: 原典は「raw → wiki」の 2 層だが、本実装は **Sources/ を中間層として挟む**。Raw を丁寧に整形してから Wiki に取り込むプロセスを明示化。

### ジャンル階層規約

- フロントマター `genre: aws/dynamodb` のスラッシュ区切り
- 2 階層まで入れ子、3 階層目はページとして扱う
- 各ジャンルに `_index.md` / `_log.md` / `_overview.md` の 3 点セット
- `Sources/` 配下も `Wiki/` と同じ入れ子構造をミラー

## 独自拡張 3 点

### 1. アーカイブ原則（[[decision-archive]]）

`Decisions/` ページは**書き換えない**。新判断は新ページで `superseded_by` 連鎖。`date` は決断日で固定。

```yaml
---
title: "2026-07 転職先選定"
type: decision
date: 2026-04-11
status: active
superseded_by: null
context_snapshot: "SES2年目、SA志向が固まった時期"
options_considered: ["フリーランス"]
decision: "Sierへの転職決断"
---
```

「Wiki を常に最新」ではなく「**思考の化石と現在の地図を両立**」させる設計思想。[[model-collapse]] の LLM 自己参照劣化にも部分的に効く（過去判断が上書きされないため）。

### 2. 自己成就的予言の回避

`Self/` 領域は最も慎重に扱う。理由は「パターン抽出を自動化すると LLM が "あなたはこういう人" を固定化し、Wiki が自己成就的予言になる」から。

ハイブリッド運用：

```
朝: 2分だけ自由記述（フレームなし）  ← 観測の生データ
  ↓ LLMが裏で構造化
夜: 構造化質問（朝の記述を踏まえる）   ← 解釈と接続
```

パターンページは `confidence: low | medium | high` と `observation_count` で信頼度管理。10 回以上かつ異なる状況で一貫して出現したものだけが `high` に昇格。[[memory-lifecycle]] の confidence 概念と親和的。

### 3. レイヤー別自動化レベル

| 操作 | Knowledge | Decision | Self |
|------|-----------|----------|------|
| 新規作成 | 自動 | 確認後 | 提案のみ |
| 追記 | 自動 | 確認後 | 提案のみ |
| 書き換え | diff 表示→承認 | **禁止** | 提案のみ |
| 矛盾指摘 | 必ず上げる | 必ず上げる | 必ず上げる |
| パターン抽出 | — | — | 提案のみ |

「自律性は領域によって段階化する」という明示的な方針。[[yamapiiii]] の「自律性は段階的委譲」と共通する知見。

## Skill 構成

Phase 1 最小セット（1 週間で動かす）：

1. `SKILL.md`（`ingest` / `query` / `morning` / `evening` の 4 コマンド）
2. `schema/knowledge-template.md`
3. `schema/decision-template.md`
4. `Wiki/<genre>/_index.md`
5. `Wiki/<genre>/_log.md`

あえて含めないもの: `Synthesis/`（データ蓄積後）、lint（人間の目視先行）、Patterns 抽出（3 週間後）。

## ingest フロー（8 ステップ）

```
[1] ソースを読む
[2] ジャンル判定
[3] 既存Wikiの状態把握（_index.md読み込み）
[3.5] 影響範囲アセスメント（内部処理・ユーザー非表示）
[4] レイヤー判定で分岐
     ├ Knowledge → 自動
     ├ Decision  → 確認
     └ Self      → 提案
[5] Sources/に要約ページ作成
[6] Wiki/にページ作成・更新
[7] _index.md 更新
[8] _log.md 記録
```

## 本 Vault との差分メモ

| 観点 | 本 Vault 現状 | Takeda 実装 |
|------|-------------|------------|
| 領域分離 | type 別（entities/concepts/sources） | 領域別（Knowledge/Decisions/Self/Synthesis） |
| 層 | raw → wiki の 2 層 | Raw → Sources → Wiki の 3 層 |
| 自動化レベル | 一律（LLM が全面書き換え） | 領域で段階化 |
| 意思決定の扱い | 未対応 | [[decision-archive]] |
| 自己観察 | 未対応 | Self/ 領域 |

本 Vault は**外向きメタwiki（「llm-wiki という考え方」）**が題材のため、Decisions/Self 領域は現状対象外。ただし、ingest 履歴における判断記録（どのソースを採用したか等）を将来的に `Decisions/` 相当にすることは検討余地あり。

## 関連ページ

- [[llm-wiki]] — パターン本体
- [[karpathy-gist]] — 原典（本記事の出発点）
- [[hayatetakeda]] — 著者
- [[decision-archive]] — 本記事提唱の独自原則
- [[self-os]] — 本記事全体の設計パターン名
- [[taroumegane]] — 同時期の日本語実装者（異なる拡張方向）
- [[rohitg00-v2-gist]] — 別方向の拡張案（運用技術）
- [[memory-lifecycle]] — confidence / supersession 概念で親和
