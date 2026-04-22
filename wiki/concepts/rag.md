---
type: concept
updated: 2026-04-22
sources: [../sources/karpathy-gist.md]
aliases: [RAG, Retrieval-Augmented Generation, 検索拡張生成]
---

# rag

**Retrieval-Augmented Generation**。LLM に外部文書を検索結果として与えて回答を生成させる手法。本 wiki では主に [[llm-wiki]] の**対比概念**として扱う。

## 一般的な仕組み

1. ドキュメント群を chunk 化して embedding で index 化
2. クエリ時にベクトル類似度で関連 chunk を検索
3. 検索結果を prompt に添えて LLM に回答させる

代表実装: NotebookLM、ChatGPT のファイルアップロード、多くの社内 QA ボット。

## [[llm-wiki]] から見た限界

[[karpathy-gist]] が指摘する RAG の本質的弱点:

- **知識が蓄積しない** — クエリのたびに chunk を探し直す
- **synthesis が毎回発生** — 5 文書をまたぐ質問では毎回ピースを集め直す
- **cross-reference が作られない** — 文書間の関係が永続化されない
- **矛盾が都度露呈** — 競合情報の整理が結果に残らない

> the LLM is rediscovering knowledge from scratch on every question. There's no accumulation.
> — Karpathy

## llm-wiki との関係

RAG を**完全否定するものではない**。llm-wiki も内部的に検索は必要で、大規模化した際は [[qmd]] のようなハイブリッド検索（BM25 + ベクトル + LLM 再ランキング）を併用する。違いは **検索対象が raw chunk ではなく、すでに LLM が整理・相互参照済みの wiki ページである** こと。

## 関連ページ

- [[llm-wiki]] — 対比概念
- [[karpathy-gist]] — RAG の限界を論じた原典
- [[qmd]] — llm-wiki スケール時に使うローカル検索
