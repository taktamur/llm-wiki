---
type: concept
updated: 2026-04-22
sources: [../sources/blog-taichiendo.md, ../sources/karpathy-gist.md]
aliases: [3フェーズワークフロー, Ingest Compile Lint, 3段階ワークフロー]
---

# ingest-compile-lint

[[llm-wiki]] の **3 フェーズワークフロー**。[[taichiendo|遠藤太一]] が [[blog-taichiendo]]（2026-04-11）で **Ingest → Compile → Lint** として命名・定式化した。

## フェーズ定義

```
Phase 1: Ingest  → 生のテキストを inbox/ に投入（整理不要）
Phase 2: Compile → Claude Code が wiki/ に自動生成・[[WikiLink]] 付け
Phase 3: Lint    → 矛盾・孤立・鮮度・リンク抜けチェック
```

### Phase 1: Ingest（投入）

- 対象: YouTube 書き起こし、論文・記事メモ、会議メモ、アイデア断片
- 投入先: `inbox/`（整理不要）
- 人間の作業: **投入するだけ**

### Phase 2: Compile（統合）

Claude Code が自律実行：

1. コンテンツを理解し、Wikipedia 型の構造化ページを生成
2. 関連キーワード同士を `[[WikiLink]]` で自動接続
3. 生成ノートを `wiki/` 配下に配置
4. 処理済みファイルを `inbox/processed/` に移動

Claude Code への典型プロンプト：

```
Read all files in /vault/inbox/,
create structured wiki pages in /vault/wiki/ with [[WikiLinks]],
then move processed files to /vault/inbox/processed/
```

### Phase 3: Lint（品質チェック）

プログラミングの linter と同じ考え方で、知識ベース全体の品質をチェック：

1. **矛盾検出** — ページ間で食い違う記述
2. **孤立ページ検出** — どこからもリンクされていないノード
3. **未リンク検出** — 関連しているのにリンクされていないページ
4. **鮮度チェック** — 古くなっている可能性のある情報

Claude Code への典型プロンプト：

```
Analyze all wiki pages and:
1. Find contradictions between pages
2. Identify orphan pages (no incoming links)
3. Find pages that should be linked but aren't
4. Flag potentially outdated information
Output a lint report to /vault/lint_report.md
```

## 原典との関係

本 3 フェーズは実質的に [[karpathy-gist]] の標準ワークフローを言語化したもの。Karpathy 原典は英語で以下の語彙を使う：

- **Ingest** (same)
- **Compile** (原典では compile という語彙は薄い。本定式化が明示)
- **Lint** (same)

[[taichiendo]] の貢献は **「Compile」を独立フェーズとして命名**した点。原典では Ingest に含意されていた「構造化・リンク付け」を独立フェーズとして可視化。

## 本 Vault の対応関係

本 Vault の [[CLAUDE]] では以下の用語を使う：

| 本 Vault | [[taichiendo]] 命名 | 違い |
|---------|--------------------|------|
| **Ingest** | Ingest | 本 Vault の Ingest は「投入〜統合〜log 追記」まで広義 |
| （含意） | Compile | 本 Vault では Ingest の一部 |
| **Query** | （別系統） | 本 Vault は質問応答も独立フェーズ化 |
| **Lint** | Lint | 同義 |

本 Vault はさらに **Query** を独立フェーズとして加える（[[CLAUDE]] 参照）。Query も wiki 更新を伴うため、実質 4 フェーズ構成：`Ingest → Compile（Ingest に統合） → Query → Lint`。

## outputs/ 層の追加

[[taichiendo]] 実装のユニーク点として、`wiki/` とは別に `outputs/` フォルダを持つ：

```
vault/
├── inbox/           # Phase 1 投入先
├── wiki/            # Phase 2 生成先（知識ベース）
└── outputs/         # Phase 3 派生コンテンツ（記事・スライド・提案書）
```

**役割分離**: wiki/ は知識の蓄積、outputs/ は wiki を元に生成された**派生コンテンツ**の置き場。本 Vault には `outputs/` 相当はない（メタ wiki 用途のため不要）。

## 関連ページ

- [[llm-wiki]] — 親パターン
- [[karpathy-gist]] — 原典ワークフロー源
- [[blog-taichiendo]] — 命名・定式化の原典
- [[taichiendo]] — 命名者
- [[CLAUDE]] — 本 Vault のワークフロー定義
- [[taroumegane]] — 同時期の別定式化（ingest/query/lint の本 Vault 近似形）
