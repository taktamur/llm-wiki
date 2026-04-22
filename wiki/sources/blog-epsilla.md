---
type: source
updated: 2026-04-22
url: https://www.epsilla.com/blogs/llm-wiki-kills-rag-karpathy-enterprise-semantic-graph
author: Epsilla
fetched: 2026-04-22
lang: en
sources: [../../raw/blog-epsilla-semantic-graph.md]
aliases: [Epsilla blog, LLM Wiki kills RAG]
---

# Epsilla: "Did Karpathy's LLM Wiki Just Kill RAG?"

[[epsilla]]（ベクトル DB/Semantic Graph を提供する企業）が公開したブログ記事。Karpathy を「RAG 1.0 の限界を正しく指摘した」と評価しつつ、**個人用途は妥当だがエンタープライズ向けには不十分**とし、自社推進の [[semantic-graph]] を代替案として提示する。

## 論旨

- RAG 1.0 の問題点（任意チャンク化 → ベクトル化 → 文脈喪失 → ブラックボックス化）を改めて整理
- Karpathy の LLM Wiki は「LLM が生データをコンパイルして人間可読・相互接続な markdown にする」点で優れる
- エンタープライズ運用には以下の致命的欠陥があると指摘:
  - **RBAC** 不可
  - **監査対応**の弱さ
  - 数百万ドキュメントへの**スケーラビリティ**
  - 機密情報の**外部流出リスク**

## Semantic Graph 提案

Karpathy の 3 原則（構造化・相互接続・人間可読）を保持しつつ、エンタープライズ要件を満たす実装を主張:

- 基本単位が不透明なベクトルではなく**人間可読なノード**
- **型付き・追跡可能な関係性**（[[knowledge-graph]] と同じ方向性）
- **RBAC** と**監査ログ**の組み込み
- **リアルタイムデータ取り込み**

## 評価メモ（本 wiki）

- ポジションバイアス: 自社製品（Epsilla）への誘導を含むため論調は割り引いて読む
- しかし批判の骨子（エンタープライズ要件）は妥当。本 wiki は**個人用途**なので現時点では影響小
- [[rohitg00-v2-gist]] の **Privacy and governance** セクションと論点が重複する。個人用途でも将来的に考慮

## 関連ページ

- [[semantic-graph]] — 提示された代替案
- [[epsilla]] — 企業
- [[llm-wiki]] — 批判対象
- [[rag]] — 記事が引き合いに出す従来手法
- [[knowledge-graph]] — Semantic Graph の技術的位置付け
