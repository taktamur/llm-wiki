---
type: concept
updated: 2026-04-22
sources: [../sources/rohitg00-v2-gist.md, ../sources/blog-epsilla.md]
aliases: [Knowledge Graph, 知識グラフ]
---

# knowledge-graph

エンティティをノード、関係を型付きエッジとして表現する構造。[[rohitg00-v2-gist]] が [[llm-wiki]] のフラットな markdown + wikilink に追加を推奨するレイヤーであり、[[semantic-graph]]（[[epsilla]] のエンタープライズ版）も同方向性。

## [[llm-wiki]] の wikilink との違い

- **wikilink**: ファイル名ベース、型なし、エッジ情報なし
- **knowledge-graph**: ノードに型・属性、エッジに型・重み・出典

| 要素 | wikilink | knowledge-graph |
|------|----------|-----------------|
| ノード | markdown ファイル | 型付きエンティティ（person / project / library / concept / file / decision） |
| エッジ | 単純リンク | 型付き関係（uses / depends on / contradicts / caused / fixed / supersedes） |
| 走査 | 人間の目で追う | graph traversal クエリ可能 |
| 発見 | 手動 | 構造的関係から自動発見 |

## クエリ例

> "Redis をアップグレードしたら何に影響する？"

- wikilink 方式: "Redis" ページを開き、backlinks を人間が読む
- knowledge-graph 方式: Redis ノードから `used-by` / `depends-on` / `impacts` エッジを辿って**全下流を列挙**

キーワード検索では届かない「3 ホップ先の構造的依存」を拾える。

## ページとグラフの併存

[[rohitg00-v2-gist]] の重要な主張:

> The graph doesn't replace the wiki pages. It augments them. Pages are for reading. The graph is for navigation and discovery.

markdown ページは**読む**ため、グラフは**ナビゲートと発見**のため。併存が正しい。

## 本 wiki への適用可能性

- Obsidian の graph view は「ファイル + 単純リンク」のグラフ。型は frontmatter の `type:` のみで色分け可能
- 本格的な knowledge-graph には [Neo4j](https://neo4j.com/) 等が必要（ユーザーのローカル環境に `neo4j-local` skill あり）
- 現時点では「frontmatter で最低限の型を持つ markdown」で十分。本ページ自体が将来の拡張参照点

## 関連ページ

- [[rohitg00-v2-gist]] — 主要出典
- [[semantic-graph]] — エンタープライズ版
- [[llm-wiki]] — 適用先
- [[obsidian]] — 部分実装として graph view
