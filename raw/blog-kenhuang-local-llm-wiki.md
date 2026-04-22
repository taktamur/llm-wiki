---
type: raw
url: https://kenhuangus.substack.com/p/what-andrej-karpathy-got-right-how
author: Ken Huang (Substack)
fetched: 2026-04-22
lang: en
note: WebFetch 経由で取得したサマリ（生 HTML 全文は保存していない。主要論点のみ）
---

# What Andrej Karpathy Got Right: How a Local LLM Wiki Beats RAG?

*Substack post — retrieved summary*

## Karpathy への評価

Karpathy の洞察「従来 RAG は根本的に一時的である（no accumulation, no growing persistent artifact）」を高く評価。

## 提案アプローチ

- ローカル LLM（**Gemma 4**）で LLM 管理型 wiki を構築
- 生の情報源とユーザーの間に**永続的・相互リンクされた知識層**を挟む
- クラウド非依存のプライバシー保護アプローチ

## 実装パターン

| モード | 説明 |
|--------|------|
| 理想形 | 人間が論文を読み、エージェントと協力しながら手動で index 更新 |
| 実用形 | 時間制約がある場合の自動 ingest プロトタイプ |

## 想定ドメイン

- AI リサーチ
- サイバーセキュリティ

## コメント

本 wiki にとっては「ローカル LLM + llm-wiki」の実装バリエーションとして記録。Gemma 4 等の軽量モデルでどこまで wiki 維持が現実的かは未検証。
