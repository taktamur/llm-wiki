---
type: concept
updated: 2026-04-22
sources: [../sources/hn-karpathy-llm-wiki.md]
aliases: [Model Collapse, モデル崩壊]
---

# model-collapse

LLM が自身の生成出力で学習・参照を続けることで、分布の裾が失われ情報が劣化していく現象。[Nature 2024 (Shumailov et al.)](https://www.nature.com/articles/s41586-024-07566-y) で命名・分析された。

[[hn-karpathy-llm-wiki]] で **devnullbrain** が [[llm-wiki]] への主要懸念として挙げた論点。

## llm-wiki における懸念

- `wiki/` のページは LLM が繰り返し書き換える。新しい ingest のたびに既存ページに差分追記・再構成が入る
- この「**LLM が自分の出力を読んでさらに書き換える**」ループが本質的に model collapse の構図に近い
- 典型的な症状: 有効情報の平板化、例の具体性喪失、比喩・ニュアンスの消失、ページ間の同質化

## 緩和策

### 本 wiki の設計で既に採用しているもの

- **`raw/` の不変性**: 原典は常に元の形で参照可能。`wiki/` ページが劣化しても戻れる
- **`sources:` frontmatter**: 各 wiki ページから `raw/` への traceability
- **wikilink の手書き**: LLM 生成だが人間がレビューできる粒度に留める

### [[rohitg00-v2-gist]] が提案する追加対策

- **Confidence scoring**: 弱い claim が強い claim と混じらないようマーク
- **Supersession**: 書き換えではなく旧版保持で新版を重ねる（version control for knowledge）
- **Quality scoring**: 生成コンテンツが基準未満ならフラグ・再生成

### 運用上のガードレール

- ページの**核となる段落**は人間がレビューし、LLM の再書き換え対象から保護する（コメントで明示）
- 定期 lint で**同質化**（ページ間の表現が似すぎ）を検出
- 重要 claim は**原文引用**を残す（`> quote` 形式）

## 関連ページ

- [[llm-wiki]] — 舞台
- [[hn-karpathy-llm-wiki]] — 指摘元
- [[rohitg00-v2-gist]] — 緩和策の出典
