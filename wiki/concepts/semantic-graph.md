---
type: concept
updated: 2026-04-22
sources: [../sources/blog-epsilla.md]
aliases: [Semantic Graph]
---

# semantic-graph

[[epsilla]] が提唱する、エンタープライズ向け RAG/知識ベース実装のアプローチ。[[rag]] の RAG 1.0（チャンク + ベクトル）を否定しつつ、[[llm-wiki]] の markdown 方式もエンタープライズには不十分として、**人間可読なノード + 型付きエッジ + RBAC/監査**を組み込んだグラフを提示する。

## 特徴

- 基本単位は**不透明なベクトル**ではなく**人間可読なノード**
- 関係は**型付き・追跡可能**
- **RBAC**（Role-Based Access Control）が組み込み
- **監査ログ**が組み込み
- **リアルタイムデータ取り込み**対応

## [[llm-wiki]] との異同

| 観点 | llm-wiki | semantic-graph |
|------|----------|----------------|
| 基本単位 | markdown ページ + wikilink | 型付きノード + 型付きエッジ |
| 人間可読性 | ◎ | ○（UI 次第） |
| RBAC | ✕（ファイルシステム権限のみ） | ◎ |
| 監査 | `log.md` の append-only のみ | ◎ |
| 想定規模 | 個人〜数百ページ | 数百万ドキュメント |
| 商用 | OSS / 自作 | Epsilla 製品 |

## [[knowledge-graph]] との異同

semantic-graph は knowledge-graph の企業向け実装・ブランド呼称と捉えてよい。[[rohitg00-v2-gist]] の knowledge-graph 提案と技術的方向性は一致（エンティティ型・関係型・グラフ走査）。

## 本 wiki への示唆

本 Vault は個人用途なので semantic-graph を採用する必要はない。ただし将来 llm-wiki を業務（チーム共有）に適用する場合は:

- RBAC と監査は markdown + git + ファイルシステム権限だけでは不十分
- [[rohitg00-v2-gist]] の **Privacy and governance** セクションと合わせて設計が必要
- ベンダーロックインを避けるなら、Neo4j のような OSS グラフ DB を markdown と併用する選択肢

## 関連ページ

- [[epsilla]] — 提唱企業
- [[knowledge-graph]] — 一般的概念としての同方向性
- [[llm-wiki]] — 対比される個人向けパターン
- [[rag]] — 共通の批判対象
