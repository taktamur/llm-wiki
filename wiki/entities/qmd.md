---
type: entity
updated: 2026-04-22
sources: [../sources/karpathy-gist.md]
url: https://github.com/tobi/qmd
aliases: [qmd]
---

# qmd

tobi（Shopify の Tobias Lütke とされる）が公開した、**ローカル markdown ファイル向け検索エンジン**。[[karpathy-gist]] で [[llm-wiki]] の規模拡大時の推奨ツールとして挙げられている。

## 特徴（gist の記述）

- **ハイブリッド検索**: BM25 + ベクトル + LLM 再ランキング
- **すべてオンデバイス**（クラウド非依存）
- **CLI** として LLM からシェル経由で呼べる
- **MCP サーバー**としても提供、LLM のネイティブツールとして使える

## llm-wiki における位置付け

- 小規模（~100 ソース）: `index.md` だけで十分
- 大規模化: qmd でハイブリッド検索を導入
- 独自の簡易検索スクリプトを LLM に vibe-code させる選択肢もある、と gist は補足

本 wiki は現時点で 1 ソースのみ。qmd 導入は未来の判断事項として [[llm-wiki]] のオープン論点に記録。

## 未取得情報

- 実際のインストール手順・ライセンス・内部アーキテクチャは未 ingest
- README を取り込むのは Phase 4 の範囲（必要に応じて後日）

## 関連ページ

- [[llm-wiki]] — スケール時に使う道具
- [[karpathy-gist]] — 推奨の出典
- [[rag]] — qmd のハイブリッド検索が属する技術カテゴリ
