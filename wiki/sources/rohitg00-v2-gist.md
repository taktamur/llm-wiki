---
type: source
updated: 2026-04-22
url: https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2
author: rohitg00
fetched: 2026-04-22
lang: en
sources: [../../raw/rohitg00-llm-wiki-v2-gist.md]
aliases: [LLM Wiki v2, v2 gist]
---

# rohitg00: LLM Wiki v2 gist (2026-04)

[[karpathy-gist]] の拡張として公開された **LLM Wiki v2** gist。著者 rohitg00 が [agentmemory](https://github.com/rohitg00/agentmemory)（AI coding agent 向け永続メモリエンジン）で運用した知見を元に、スケール時の失敗モードと追加レイヤーを整理している。

> The Memex is finally buildable. Not because we have better documents or better search, but because we have librarians that actually do the work.

## 原典への立ち位置

- Karpathy の 3 層構造（raw / wiki / schema）と 3 操作（ingest / query / lint）はそのまま有効
- この gist が足すのは「本番運用で壊れる箇所」と「腐らない wiki と腐る wiki を分けるもの」

## 追加される 6 レイヤー

### 1. [[memory-lifecycle]] — メモリの寿命管理

原典は全コンテンツを永久に等価に扱うが、実際の知識にはライフサイクルがある。

- **Confidence scoring**: 全 fact に信頼度（ソース数・最終確認日・矛盾の有無）
- **Supersession**: 新情報は古い claim を明示的に supersede。旧版は timestamp 付きで保持
- **Forgetting**: Ebbinghaus の忘却曲線。アクセス・補強でリセット、未使用なら減衰
- **Consolidation tiers**: Working → Episodic → Semantic → Procedural の 4 段階で圧縮・昇格

### 2. [[knowledge-graph]] — 型付き構造

wikilink は良いがフラット。型付きノード・型付きエッジを重ねる。

- **Entity extraction**: person / project / library / concept / file / decision 等の型
- **Typed relationships**: uses / depends on / contradicts / caused / fixed / supersedes
- **Graph traversal**: キーワード検索では届かない構造的関係を辿る

### 3. スケール対応検索

`index.md` は 100〜200 ページで飽和する。

- BM25（キーワード）+ ベクトル（意味）+ グラフ走査の 3 本立て
- Reciprocal Rank Fusion で統合
- index.md は human-readable catalog として残し、LLM の主要検索経路にはしない

### 4. イベント駆動オートメーション

原典は全操作が手動。本番では以下のフックが必要:

- **On new source**: auto-ingest、エンティティ抽出、graph 更新
- **On session start**: 最近の活動から関連コンテキストをロード
- **On session end**: セッションを観察に圧縮、洞察を file
- **On query**: 回答の品質スコアが閾値超なら wiki に書き戻し
- **On memory write**: 矛盾チェック、supersession 起動
- **On schedule**: 定期 lint、consolidation、retention decay

### 5. 品質と自己修復

- 全 LLM 生成コンテンツに品質スコア
- Self-healing lint: orphan ページ自動リンク、stale claim 自動マーク、壊れた cross-ref 修復
- Contradiction resolution: 再 current・権威・支持観測数で自動判定（人間が override 可）

### 6. マルチエージェント・協調

- **Mesh sync**: 複数エージェントの観察をマージ。LWW + タイムスタンプ解決 + 手動 override
- **Shared vs. private**: 個人観察が昇格時に共有知識へ
- **Work coordination**: 軽量な作業分担追跡（フルタスク管理ではない）

### 7. プライバシーとガバナンス

- **Filter on ingest**: API キー・トークン・PII を自動 strip
- **Audit trail**: 全操作を timestamp 付きで log
- **Bulk operations with governance**: 一括削除・エクスポート・マージを監査・取り消し可能に

### 8. Crystallization

完了したワーク（リサーチ・デバッグ・分析）を自動で構造化ダイジェストに蒸留。質問・発見・関与ファイル・得た教訓を抽出し、first-class wiki ページとして file。

## Implementation spectrum

全機能は**モジュラー**。初日から全部必要ではない。

1. **Minimal viable wiki**: raw + pages + index.md + schema（原典相当）
2. **+Lifecycle**: confidence / supersession / retention decay
3. **+Structure**: entity 抽出 / 型付き関係 / graph
4. **+Automation**: hooks
5. **+Scale**: hybrid search / consolidation tiers / quality scoring
6. **+Collaboration**: mesh sync / private scoping / coordination

## Schema is the real product

> the schema document (CLAUDE.md, AGENTS.md) is the most important file in the system.

スキーマがドメインに合わせて共進化した結果が、**他の同種ドメインの人にも転用可能な資産**になる。

## 本 wiki への影響

本 Vault は現在 Minimal viable 段階。以下を中長期のオープン論点として [[llm-wiki]] に追記:

- confidence scoring の導入是非
- entity 型・relationship 型の定義
- セッション終了時の自動 crystallization
- `CLAUDE.md` をレベル 2 以降のスキーマにどう拡張するか

## 関連ページ

- [[karpathy-gist]] — 原典
- [[llm-wiki]] — パターン全体
- [[memory-lifecycle]] — v2 の最大の追加
- [[knowledge-graph]] — v2 が提案する構造化層
