---
type: source
updated: 2026-04-22
url: https://kenhuangus.substack.com/p/what-andrej-karpathy-got-right-how
author: Ken Huang
fetched: 2026-04-22
lang: en
sources: [../../raw/blog-kenhuang-local-llm-wiki.md]
aliases: [Ken Huang post, Local LLM Wiki beats RAG]
---

# Ken Huang: "What Andrej Karpathy Got Right: How a Local LLM Wiki Beats RAG?"

個人 Substack で公開された肯定的論評。**ローカル LLM（Gemma 4）** を組み合わせたプライバシー保護型 llm-wiki の実装構想を提示する。

## 要旨

- Karpathy の「RAG は根本的に一時的（no accumulation, no persistent artifact）」という洞察を高く評価
- クラウドに依存しないローカル LLM で wiki 維持を回す提案
- 対象ドメイン: AI リサーチ、サイバーセキュリティ

## 実装パターン

| モード | 説明 |
|--------|------|
| 理想 | 人間が論文を読み、エージェントと協力しながら手動 ingest で index 更新 |
| 実用 | 時間制約時の自動 ingest プロトタイプ |

## 本 wiki への示唆

- Claude Code（クラウド）で運用している現状 + ローカル LLM オプションの可能性として記録
- Gemma 4 等の軽量モデルで wiki 維持がどこまで現実的かは要検証（本記事はまだ検証データを提示していない）

## 関連ページ

- [[llm-wiki]] — パターン本体
- [[karpathy-gist]] — 評価対象
- [[rag]] — 対比概念
