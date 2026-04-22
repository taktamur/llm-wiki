---
type: source
updated: 2026-04-22
url: https://zenn.dev/dely_jp/articles/8b55114cc0b958
author: たろう眼鏡 (dely / クラシル社)
fetched: 2026-04-22
lang: ja
sources: ["../../Clippings/【LLM Wiki】Obsidian x Claude Codeで学んだ知識を構造化し記憶媒体を脳からAIに移行する.md"]
aliases: [たろう眼鏡 LLM Wiki 記事, Zenn dely_jp LLM Wiki, Claude Code 実装例]
---

# たろう眼鏡: 【LLM Wiki】Obsidian x Claude Code で知識を構造化 (2026-04)

Zenn @dely_jp 公開記事（2026-04-14）。[[andrej-karpathy]] の [[karpathy-gist]] を受けて、[[taroumegane]]（クラシル社サーバサイドエンジニア）が **Claude Code の skill として** [[llm-wiki]] を実装した経緯と、gist にない実運用判断基準を 3 つ追加した記録。原典は Obsidian Web Clipper 経由で `Clippings/` に取り込んだため、本 Vault では [[clip-taroumegane-llm-wiki|このページ]] が日本語実装の参照点となる。

> 原典 frontmatter（Web Clipper デフォルト schema）では `source` = URL、`created` = 取得日。`raw/` 系の `url` / `fetched` に相当。

## このソースの位置付け

- **日本語圏で最初期の Claude Code 実装レポート**（HN [[hn-karpathy-llm-wiki]] は英語圏議論、[[blog-kenhuang]] は構想、[[blog-epsilla]] は批判）
- Karpathy gist の「実装は自分の LLM と共作せよ」を文字通り実行。`/skill-creator` に gist をそのまま投げて SKILL.md を生成
- **gist にない運用判断基準を 3 つ補完**（後述）。本 Vault のスキーマ拡張候補として重要

## 実装フロー

1. [Karpathy の gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) をそのまま Claude Code の `/skill-creator` に投入
2. 対話的 Q&A で SKILL.md を生成:
   - 用途: 技術リサーチ（MySQL, Rails 等）をジャンル別に分類
   - 閲覧: [[obsidian]]、`[[wikilinks]]` + YAML frontmatter
   - 配置: `~/Documents/Obsidian Vault/llm-wiki/`
   - 言語: 全て日本語
   - ソース: `~/Documents/Obsidian Vault/raw/Clippings/`（Web Clipper 経由）
3. 実 ingest で生成物をブラッシュアップし、独自の判断基準 3 点を組み込む

## SKILL.md の引数体系（参考）

| 入力 | 操作 |
|------|------|
| `ingest` | デフォルトソースから未取り込みを検出 |
| `ingest <パス>` | 指定ファイルを取り込み |
| `ingest <ディレクトリ>` | 配下を一括取り込み |
| `query <質問>` | wiki に質問 |
| `lint` | 健全性チェック |
| `init <ジャンル>` | 新ジャンルフォルダ初期化 |

## 3 つの独自設計判断（本 Vault への示唆）

### 1. [[two-layer-index]] — 2 層 index/log 構造

Karpathy gist は index.md / log.md を各 1 ファイルで設計。ジャンル混在で 20 ページ程度までは問題ないが、**100 ページ超で query 時に LLM がコンテキストに巨大 index を読み込む問題**が発生する。

- **全体 `index.md`** — ジャンル一覧と統計のみ（ページ数・ソース数）
- **ジャンル `wiki/<genre>/index.md`** — ジャンル内の個別ページ・ソースカタログ
- **ジャンル `wiki/<genre>/log.md`** — ジャンル別ログ。全体 log は廃止

LLM は「全体 index → ジャンル index → ページ」の 2 段ナビで到達。

### 2. [[page-decision-criteria]] — ページ作成・更新・分割の判断基準

「新規ページを作るか既存に追記するか」が Karpathy gist には無く、セッションによって挙動がブレる。明文化した criteria を SKILL.md に埋め込むことで一貫性を担保。

詳細は [[page-decision-criteria]] 参照（新規 / 更新 / 分割 / 横断ページ、それぞれの発火条件）。

### 3. query 結果の保存判断基準

Karpathy は「良い回答は wiki に保存」と書くが「何が良い回答か」の基準はない。基準がないと「全保存」か「全捨て」に偏り、wiki の質が下がる。

**保存すべき**: 新しい切り口・視点、新コンセプト導入、汎用的な分析。
**保存不要**: 既存知識の組み合わせのみ、個別具体すぎて再利用性低い、既存ページ追記で済む。

query のたびに LLM が保存提案し、最終判断はユーザー。

## 本 Vault との差分メモ

| 観点 | 本 Vault 現状 | たろう眼鏡実装 |
|------|-------------|---------------|
| ソース配置 | `raw/` + `Clippings/` 併存 | `raw/Clippings/` 一本化 |
| ジャンル分割 | `wiki/{entities,concepts,sources}/` の type 別 | `wiki/<genre>/` のドメイン別 |
| index/log | 単一（現状ページ数少なく十分） | 2 層（100 ページ超対応） |
| ページ判断基準 | CLAUDE.md に未記載 | SKILL.md に明記 |
| query 保存基準 | CLAUDE.md に未記載 | SKILL.md に明記 |

本 Vault がページ数 100 超に達したら [[two-layer-index]] の導入を検討する。type 別 vs ドメイン別の分割方針はユーザーの好みで選択可能（本 Vault は type 別を選択）。

## 引用図版（原典）

- [Obsidian グラフビュー](https://static.zenn.studio/user-upload/dc909503b6ad-20260413.png)
- [/llm-wiki query 実行例](https://static.zenn.studio/user-upload/beeff4cb6d0a-20260413.png)
- [クエリ結果グラフ](https://static.zenn.studio/user-upload/b9bb558ce3f6-20260413.png)
- [query 結果の wiki 保存確認](https://static.zenn.studio/user-upload/4707883ead87-20260413.png)

## 関連ページ

- [[llm-wiki]] — パターン本体
- [[karpathy-gist]] — 原典 gist（本記事の出発点）
- [[taroumegane]] — 著者
- [[obsidian]] — 本記事での推奨 UI。Web Clipper でソース投入
- [[two-layer-index]] — 本記事が提唱するスケーリングパターン
- [[page-decision-criteria]] — 本記事が提唱する ingest 判断基準
- [[rag]] — 対比概念
