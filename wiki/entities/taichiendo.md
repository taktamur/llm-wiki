---
type: entity
updated: 2026-04-22
sources: [../sources/blog-taichiendo.md]
aliases: [遠藤太一, TaichiEndoh, 遠藤 太一]
---

# 遠藤太一（@TaichiEndoh）

Qiita で情報発信する個人エンジニア。2026-04-11 公開の「[Claude Code × Obsidian で作る『自己増殖型ナレッジベース』——LLM Wiki の実装ガイド](https://qiita.com/TaichiEndoh/items/eddc358e42d6c575fded)」が [[llm-wiki]] の日本語実装ガイドとして最も早い公開日。

## 本 Vault への貢献

[[blog-taichiendo]] で以下を提示：

- **[[ingest-compile-lint]]** — 3 フェーズワークフローの命名（Ingest → Compile → Lint）
- **シンプル 3 フォルダ構成** — inbox / wiki / outputs の役割分離（outputs は派生コンテンツ置き場）
- **統合アーキテクチャ構想** — n8n + Claude Code + Bonsai-8B + Gemma 4 ADK + Obsidian Git の完全自律型
- **クラウド/ローカル LLM ハイブリッド** — プライバシー境界で LLM を切り替え

## スタンス

- NotebookLM の「**閉じた箱**」問題（情報がそこで止まる）を批判の出発点に置く
- 「**思考は Obsidian、実行は Claude Code**」の役割分担を明示
- **ハルシネーション対策**としての llm-wiki を強調（自分の投入データをグラウンドトゥルースにする）
- 医療・研究分野での運用を想定し、**機密情報はローカル LLM**（Bonsai-8B）で処理する設計

## 他記事（記事内言及）

- 「【完全自動化】n8n×Obsidian で最強の情報収集システムを作った話」
- 「【実装公開】医療現場のニーズを匿名で集める掲示板——Bonsai-8B」
- 「Gemma 4 徹底解説：Google のオープンモデル最新版」

複数プラットフォーム・複数 LLM を組み合わせる**統合アーキテクト**志向の発信者。

## 関連ページ

- [[llm-wiki]] — 実装対象
- [[andrej-karpathy]] — 原典提唱者
- [[blog-taichiendo]] — 本記事
- [[ingest-compile-lint]] — 提唱ワークフロー命名
- [[obsidian]] — 推奨 UI
- [[taroumegane]] — 並行する日本語実装者
- [[hayatetakeda]] — 並行する日本語実装者（別方向）
