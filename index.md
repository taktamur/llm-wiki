# index

llm-wiki のページカタログ。Claude は query 時にまずこれを読み、関連ページへドリルする。各エントリは「[[ページ名]] — 一言サマリ」形式。

## Concepts

- [[llm-wiki]] — Karpathy が提唱した、LLM が恒久的に維持する個人 wiki パターン（本 Vault の中核ページ）
- [[rag]] — Retrieval-Augmented Generation。llm-wiki が対比する従来手法
- [[memex]] — Vannevar Bush が 1945 年に提唱した個人知識装置。llm-wiki の思想的源流
- [[memory-lifecycle]] — v2 が追加する confidence / supersession / forgetting / consolidation tier
- [[knowledge-graph]] — 型付きノード + 型付きエッジ。v2 が推奨する構造化レイヤー
- [[model-collapse]] — LLM 自己参照ループによる情報劣化。llm-wiki 最大の技術リスク
- [[pkm]] — Personal Knowledge Management。「書く＝思考」派からの反論軸
- [[semantic-graph]] — Epsilla が提示するエンタープライズ向け代替
- [[two-layer-index]] — 100 ページ超規模での index/log 階層化パターン
- [[page-decision-criteria]] — ingest 時の「新規 / 更新 / 分割 / 横断」の明文化判断基準
- [[self-os]] — Hayate Takeda 提唱。自分自身を OS として扱う 3 領域×3 層のパターン拡張
- [[decision-archive]] — 意思決定を削除せず superseded_by で鎖状に保持する思想
- [[ingest-compile-lint]] — Taichi Endo が定式化した 3 フェーズワークフロー命名
- [[second-brain-git]] — Yamapiiii の Obsidian 非採用 Git リポジトリ第二の脳パターン（姉妹）

## Entities

- [[andrej-karpathy]] — llm-wiki パターンの提唱者
- [[obsidian]] — 本 Vault の UI。wiki 閲覧・編集クライアント
- [[qmd]] — tobi 作のローカル markdown 検索エンジン（gist で言及）
- [[epsilla]] — Semantic Graph を提唱するエンタープライズ検索ベンダー
- [[taroumegane]] — Claude Code skill 形式で llm-wiki を実装した日本語著者（dely / クラシル社）
- [[hayatetakeda]] — [[self-os]] / [[decision-archive]] を提唱した日本語著者
- [[taichiendo]] — [[ingest-compile-lint]] を定式化し「思考は Obsidian、実行は Claude Code」を唱える著者
- [[yamapiiii]] — [[second-brain-git]] パターンを提唱したナハト社の著者
- [[ahshi-dayo]] — [[nvk-llm-wiki]] 利用体験をレポートした日本語エンドユーザー
- [[yuimaru]] — Dataview 活用・中規模運用の llm-wiki 一般向け解説者
- [[nvk-llm-wiki]] — 既成の llm-wiki 実装スキル（Claude Code 向け）

## Sources

- [[karpathy-gist]] — Karpathy が公開した llm-wiki の原典 gist（2026-04 取得）
- [[rohitg00-v2-gist]] — agentmemory の運用知見を元にした拡張版 gist
- [[hn-karpathy-llm-wiki]] — HN メインスレッド 47640875（296 pt / 95 cmt）
- [[blog-epsilla]] — エンタープライズ批判と Semantic Graph 代替提示
- [[blog-kenhuang]] — ローカル LLM (Gemma 4) 実装案
- [[clip-taroumegane-llm-wiki]] — Zenn @dely_jp の Claude Code skill 実装記事（2026-04-14）
- [[blog-hayatetakeda]] — self-os / decision-archive を提唱した設計原理批判拡張（2026-04-19）
- [[blog-taichiendo]] — ingest-compile-lint 定式化と統合アーキテクチャ構想（2026-04-11）
- [[blog-yamapiiii]] — Obsidian 非採用の Git リポジトリ第二の脳（2026-03-01、Karpathy 非参照の姉妹パターン）
- [[blog-ashidayo]] — nvk-llm-wiki 利用エンドユーザー体験レポート（2026-04-16）
- [[blog-yuimaru]] — 一般向け解説と中規模（約 100 資料）運用実測（2026-04-13）

## Meta

- [[CLAUDE]] — 本 wiki の運用スキーマ
- [[log]] — ingest/query/lint の時系列ログ
