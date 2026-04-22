---
type: raw
url: https://www.epsilla.com/blogs/llm-wiki-kills-rag-karpathy-enterprise-semantic-graph
author: Epsilla (company blog)
fetched: 2026-04-22
lang: en
note: WebFetch 経由で取得したサマリ（生 HTML 全文は保存していない。主要論点のみ）
---

# Did Karpathy's 'LLM Wiki' Just Kill RAG? The Enterprise Verdict

*Epsilla Blog post — retrieved summary*

## Premise

Epsilla は Karpathy の LLM Wiki が従来の RAG の限界を「正しく」指摘している点を認める。しかし「ローカル markdown ファイルはエンタープライズ向けではない」と主張し、自社が推進する **Semantic Graph** こそが本質的解決策だとする。

## RAG 1.0 の問題点（Epsilla の整理）

- ドキュメントを任意のチャンクに分割
- 数学的ベクトルに変換して保存
- 文脈情報が失われる
- 「ブラックボックス」化し、内容を人間が検証できない

## Karpathy の LLM Wiki の利点（Epsilla が認める点）

- 生データを LLM が「コンパイル」して構造化 markdown 群に変換
- 人間可読で監査可能
- LLM エージェントが継続的に「自己治癒」する（inspect / update / suggest new connections）

## エンタープライズ視点での致命的欠陥（Epsilla の批判）

- **アクセス制御**: RBAC（Role-Based Access Control）を markdown で実装できない
- **監査対応**: 変更履歴・コンプライアンス要件を満たさない
- **スケーラビリティ**: 数百万ドキュメントに耐えない
- **セキュリティ**: 機密情報の外部流出リスク

## Epsilla の代替案: Semantic Graph

Karpathy の 3 原則（構造化・相互接続・人間可読）を保持しつつ、エンタープライズ要件を満たす実装:

- ベクトルではなく人間可読な「ノード」が基本単位
- 関係は明示的・追跡可能
- RBAC・監査ログを組み込み
- リアルタイムデータ取り込み対応

## コメント

本 wiki にとっては「個人用 llm-wiki と、エンタープライズ向け別系統（semantic graph）」という分岐提示として記録する。Epsilla の主張は自社製品への誘導を含むため、批判的スタンスを保つ必要がある。
