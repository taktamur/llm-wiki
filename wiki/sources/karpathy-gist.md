---
type: source
updated: 2026-04-22
url: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
author: Andrej Karpathy
fetched: 2026-04-22
lang: en
sources: [../../raw/karpathy-llm-wiki-gist.md]
aliases: [Karpathy gist, LLM Wiki gist]
---

# Karpathy: LLM Wiki gist (2026-04)

[[andrej-karpathy]] が公開した個人用ナレッジベース構築パターン「LLM Wiki」の原典 gist。本 wiki の出発点。

## このソースの位置付け

- 具体的な実装ではなく**アイデアファイル**として設計されている。LLM エージェント（Claude Code / Codex / OpenCode 等）にそのままコピペして使うことを想定
- ディレクトリ構造・スキーマ規約・ページ形式は「ドメインと好みに応じて自分の LLM と一緒に具体化せよ」と明記されている

## 主張の核

従来の [[rag]] は質問のたびに知識をゼロから再発見するが、[[llm-wiki]] は **LLM が読むたびに永続的な wiki を段階的に構築・維持**することで知識を複利で蓄積する。人間は「ソース選定・探索・問いを立てる」に集中し、LLM は「要約・相互参照・ブックキーピング」という退屈な作業を担う。Karpathy 自身は Obsidian を横に並べて LLM エージェントと対話しながら使っている。

> Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase.

## 3 層アーキテクチャ

| 層 | 役割 | 所有 |
|----|------|------|
| Raw sources | 原典（記事・論文・画像・データ）。不変 | ユーザー |
| The wiki | LLM 生成の markdown 群（要約・エンティティ・コンセプト・比較） | LLM |
| The schema | CLAUDE.md / AGENTS.md 等の運用ルール | 両者で共進化 |

## 3 つの操作

- **Ingest**: 新ソースを取り込み、1 ソースで 10〜15 ページに差分波及させる
- **Query**: 質問に答え、**良い回答は wiki に書き戻す**（探索も蓄積）
- **Lint**: 矛盾・孤立ページ・古い記述・不足 cross-reference の洗い出し

## Index と Log

- `index.md`: コンテンツカタログ。100 ソース / 数百ページ規模までは embedding RAG なしで十分機能する
- `log.md`: append-only。`## [YYYY-MM-DD] ingest | タイトル` 形式で `grep "^## \["` から履歴取得

## ツール言及

- [[qmd]] — ローカル markdown 検索エンジン（BM25 + ベクトル + LLM 再ランキング）。CLI と MCP の両対応
- [[obsidian]] — 筆者の推奨 UI。Web Clipper、graph view、Dataview、Marp 等との親和性
- 画像は attachment folder を `raw/assets/` 等に固定し、ホットキーでローカル保存
- wiki は git リポジトリで運用可能（version 管理・branching 無料）

## 適用ドメインの例

- 個人: 目標・健康・自己理解の継続的記録
- リサーチ: 数週〜数ヶ月の論文・レポート読み込み
- 読書: 章ごとに登場人物・テーマ・筋を wiki 化（[Tolkien Gateway](https://tolkiengateway.net/wiki/Main_Page) のような fan wiki を個人規模で）
- 業務: Slack スレッド・議事録・顧客通話を LLM が維持する社内 wiki
- 競合分析・デューデリ・旅行計画・講義ノート・趣味の深掘り

## なぜ機能するか

ナレッジベース維持の苦痛は**読む・考える**ではなく**ブックキーピング**にある。人間は維持コストが価値増加を上回った時点で wiki を放棄する。LLM は飽きず・忘れず・1 パスで 15 ファイル触れるため、維持コストが near-zero に落ちる。

思想的源流は [[memex]]（Vannevar Bush, 1945）。Bush が解けなかった「誰が維持するか」問題を LLM が担う、という整理。

## 重要な注意書き

このドキュメントは**意図的に抽象的**で、特定の実装を指示していない。ディレクトリ構造・スキーマ規約・ページ形式・ツール選定はすべて**オプショナルでモジュラー**。自分のドメインに合わせて LLM と共作せよ、というのがメタメッセージ。

## 関連ページ

- [[llm-wiki]] — パターンそのものの解説（本 wiki の中核ページ）
- [[rag]] — 対比概念
- [[memex]] — 思想的源流
- [[andrej-karpathy]] — 著者
- [[obsidian]] — 推奨 UI
- [[qmd]] — 推奨検索ツール
