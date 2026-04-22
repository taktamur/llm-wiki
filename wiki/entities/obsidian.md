---
type: entity
updated: 2026-04-22
sources: [../sources/karpathy-gist.md, ../sources/clip-taroumegane-llm-wiki.md, ../sources/blog-taichiendo.md, ../sources/blog-yuimaru.md, ../sources/blog-yamapiiii.md]
aliases: [Obsidian]
---

# obsidian

ローカル markdown ファイルを wikilink で繋いで扱えるノートアプリ。[[karpathy-gist]] で [[llm-wiki]] の推奨 UI として挙げられている。本 Vault もこの UI で閲覧することを前提に設計している。

## llm-wiki 文脈での強み

- **wikilink** `[[ページ名]]` でファイルパス非依存の相互参照
- **graph view** で wiki の形状（ハブ・孤立ページ）を可視化
- **[Web Clipper](https://obsidian.md/clipper)**（公式ブラウザ拡張）で記事を markdown 化し、Vault ルートの `Clippings/` に自動保存。YAML frontmatter（title / source / author / published / created / tags）が自動付与されるため **そのまま ingest のソースとして使える**
- **Attachment folder 設定**で画像を `raw/assets/` 等に固定保存
- **Dataview プラグイン**で frontmatter に対する動的クエリ（タグ・日付・source 数での集計）
- **Marp プラグイン**で wiki コンテンツからスライドを生成

## Web Clipper の運用サイクル（[[clip-taroumegane-llm-wiki]]）

[[taroumegane]] の実装では次のサイクルが回る:

1. Web で気になる技術記事を見つける
2. **Obsidian Web Clipper でワンクリック保存** → `Clippings/<記事タイトル>.md` が生成
3. Claude Code で `ingest` → LLM がソースを読み、要約ページ作成・概念ページ更新・相互参照追加
4. Obsidian でグラフビュー確認
5. `query` で知識を統合、良い回答は wiki に書き戻す
6. 定期的に `lint`

本 Vault でも同じサイクルが成立するよう、`CLAUDE.md` の 3 層構造テーブルに `Clippings/` を正式な原典層として組み込み済み（2026-04-22 更新）。

## 本 Vault でのセットアップ想定

ユーザーが Obsidian アプリで「フォルダを Vault として開く」→ `/Users/tak/wiki/llm-wiki/` を選択。Claude 側は `.obsidian/` を触らない。

推奨設定:
- Settings → Files and links → Attachment folder path = `raw/assets/`
- （任意）Dataview プラグイン導入 → `type:`/`updated:` で集計可能
- （任意）Marp プラグイン導入 → 資料化

## 日本語圏の採用パターン

- [[taroumegane]]（[[clip-taroumegane-llm-wiki]]）: Web Clipper → Claude Code skill → Obsidian 閲覧
- [[taichiendo]]（[[blog-taichiendo]]）: 「思考は Obsidian、実行は Claude Code」の役割分担を明示。Obsidian Git で GitHub 同期
- [[yuimaru]]（[[blog-yuimaru]]）: Web Clipper + **Dataview プラグイン活用**。中規模（約 100 資料）運用
- [[ahshi-dayo]]（[[blog-ashidayo]]）: Obsidian + [[nvk-llm-wiki]] の組み合わせ

## 意図的不採用パターン

- [[yamapiiii]]（[[blog-yamapiiii]] / [[second-brain-git]]）: **Obsidian を意図的に捨てる**。Git リポジトリ + Claude Code のみ。Claude Code がファイルを直接読み書きできる以上、閲覧 UI より**ファイルシステム直接性**を優先。GitHub Actions / Issues 統合が得られる代わりに、グラフビューなどの視覚的恩恵は失う

## 関連ページ

- [[llm-wiki]] — 本 UI で閲覧するパターン
- [[karpathy-gist]] — Obsidian 推奨の出典
- [[clip-taroumegane-llm-wiki]] — Web Clipper + Claude Code の運用サイクル実装例
- [[taroumegane]] — 上記記事の著者
- [[blog-taichiendo]] — 「思考は Obsidian、実行は Claude Code」
- [[blog-yuimaru]] — Dataview 活用例
- [[blog-yamapiiii]] / [[second-brain-git]] — 意図的不採用の対照
