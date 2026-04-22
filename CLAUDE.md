# llm-wiki スキーマ

このディレクトリは Andrej Karpathy が提唱した **LLM Wiki パターン** に沿って運用される個人ナレッジベースです。題材は**「llm-wiki という考え方それ自体」**（メタwiki）。原典は [karpathy/442a6bf555914893e9891c11519de94f](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。

Claude はセッション開始時にこのファイルを読み、以下のルールに従って wiki を維持・拡張してください。

## 3 層構造

| 層 | パス | 所有 | ルール |
|----|------|------|--------|
| 原典（手動） | `raw/` | ユーザー | **不変**。LLM は読むのみ。改変・削除禁止 |
| 原典（Web Clipper） | `Clippings/` | Obsidian Web Clipper | **不変**。LLM は読むのみ。パス・ファイル名は Web Clipper 出力をそのまま使う（リネーム禁止） |
| wiki | `wiki/` | LLM | 全ページ LLM が生成・更新。日本語 |
| スキーマ | `CLAUDE.md`（本ファイル）, `index.md`, `log.md` | 共同 | LLM が更新、ユーザーがレビュー |

## ディレクトリ

```
raw/
  <source>.md              # 1 ソース = 1 ファイル、原文そのまま保存
  assets/                  # Obsidian の attachment folder（画像等）
Clippings/
  <記事タイトル>.md        # Obsidian Web Clipper が自動保存。日本語・スペース可
wiki/
  entities/                # 人物・ツール・組織・製品
  concepts/                # 概念・方法論・パターン
  sources/                 # ソース単位の日本語要約（raw/ または Clippings/ と対応）
```

サブディレクトリは現状の 3 分類で十分。Obsidian の wikilink はフォルダを跨いで解決されるため深追いしない。

## 言語ルール

- `wiki/**` はすべて**日本語**で記述する
- `raw/**` は**原典言語そのまま**（英語なら英語）
- `Clippings/**` は Web Clipper が保存した原典言語そのまま（編集しない）
- frontmatter のキーは英語、値は内容に応じて日英どちらでも

## ファイル命名規約

- `raw/`: `karpathy-llm-wiki-gist.md`, `hn-<id>-<slug>.md`, `x-<author>-<tweet_id>.md`, `blog-<slug>.md` 等、出典種別 + 識別子で始める
- `Clippings/`: **Web Clipper のデフォルトに従う**（記事タイトルそのまま、日本語・スペース・記号可）。LLM からはリネームしない
- `wiki/entities/`: 小文字ハイフン区切り (`andrej-karpathy.md`, `obsidian.md`)
- `wiki/concepts/`: 小文字ハイフン区切り (`llm-wiki.md`, `rag.md`, `memex.md`)
- `wiki/sources/`:
  - `raw/` 対応: `raw/` のファイル名に対応 (`karpathy-gist.md` ↔ `raw/karpathy-llm-wiki-gist.md`)
  - `Clippings/` 対応: `clip-<slug>.md` 形式（英数字ハイフン、slug は ingest 時に命名）。frontmatter の `sources:` に `../../Clippings/<原ファイル名>.md` をフルパスで記載して対応関係を明示

## Frontmatter

全 `wiki/**` ページに YAML frontmatter を付ける。

**共通:**
```yaml
---
type: concept | entity | source
updated: YYYY-MM-DD
sources: [<raw/xxx.md への相対パス or source ページ>, ...]
aliases: [別名, ...]           # 任意。wikilink の表記揺れ吸収
---
```

**source ページ追加:**
```yaml
url: <原典URL>
author: <著者>
fetched: YYYY-MM-DD
lang: en | ja | ...
```

**`Clippings/**` の frontmatter は Obsidian Web Clipper 出力のまま受け入れる**（編集・正規化しない）。参考までに Web Clipper のデフォルト schema:

```yaml
---
title: "記事タイトル"
source: <URL>
author:
published: YYYY-MM-DD
created: YYYY-MM-DD    # Web Clipper が記録する取得日
description:
tags:
  - "clippings"
---
```

`raw/` 系の frontmatter キー（`url` / `fetched`）との差分（`source` / `created`）は、`wiki/sources/` 側の source ページで吸収する（ページ本文や frontmatter に「原典の Web Clipper schema では `source` = URL」等を記載）。

## wikilink の規約

- Obsidian 形式 `[[ページ名]]` を使う（ファイル名の拡張子なし・パスなし）
- 同名衝突を避けるため wiki 内のページタイトルはユニークに保つ
- エンティティ/概念ページが存在しない参照は**赤リンク**として残してよい（未作成ページの可視化）

## 標準ワークフロー

### Ingest（新しいソースを取り込む）

1. ソース原文を原典層に配置：
   - **Web Clipper 経由の場合**: 既に `Clippings/<記事タイトル>.md` に保存済み。このステップはスキップ（ファイル名・frontmatter・本文いずれも編集しない）
   - **手動 URL / ファイルの場合**: `raw/<file>.md` に保存（frontmatter で URL・著者・取得日を付与、本文は改変しない）
2. ユーザーと要点について短く確認（「このソースで強調すべき点は？」）
3. `wiki/sources/` に日本語の要約ページを作成（5〜15 分で読めるサイズ）：
   - `raw/<file>.md` → `wiki/sources/<同名またはスキーマ準拠の短縮名>.md`
   - `Clippings/<タイトル>.md` → `wiki/sources/clip-<slug>.md`（slug は ingest 時に命名）
   - source ページの frontmatter `sources:` に原典ファイルへの相対パスを記載
4. 既存の `wiki/entities/` `wiki/concepts/` を**更新**：
   - 新情報・補強・反証を差分追記
   - 矛盾があれば `> [!warning]` 等で明記し両論併記
   - 新登場のエンティティ/概念は新規ページ作成
5. `index.md` の該当カテゴリに追加／更新
6. `log.md` に追記：
   ```
   ## [YYYY-MM-DD] ingest | <ソースタイトル>
   - raw: [[<raw-file or clippings-file>]]
   - summary: [[<source-page>]]
   - touched: [[page1]], [[page2]], ... (<N>ページ更新)
   ```

### Query（質問に答える）

1. まず `index.md` を読んで関連ページを特定
2. 該当ページを読み、必要なら `raw/` または `Clippings/` の原典も参照
3. 回答は引用元（wikilink）を添えて返す
4. 回答が新しい視点・比較・分析を生んだ場合は、それも wiki に書き戻す（`wiki/concepts/` や専用ページとして）
5. `log.md` に追記：
   ```
   ## [YYYY-MM-DD] query | <質問の要旨>
   - answered via: [[page1]], [[page2]]
   - new page: [[page3]] (生成した場合)
   ```

### Lint（健康診断）

ユーザーから「lint して」と依頼されたら:

1. **まず Obsidian CLI で read-only の構造チェックを実施**（Obsidian アプリ起動中のみ。非起動時は従来どおり `Grep`/`Read` でフォールバック）:
   ```
   obsidian vault=llm-wiki orphans                        # 孤立ページ（Clippings/ と README.md は設計上除外）
   obsidian vault=llm-wiki unresolved counts              # 赤リンク集計（code block 内の例は CLAUDE.md のみ）
   obsidian vault=llm-wiki tags counts                    # タグ健全性
   obsidian vault=llm-wiki properties name=updated sort=count format=tsv  # 古い updated 順
   obsidian vault=llm-wiki backlinks file=<hub>           # ハブページの inbound 確認
   ```
2. CLI の結果と `wiki/**` の本文走査を突き合わせて、以下を報告：
   - **矛盾**: ページ間で食い違う記述（LLM 読解）
   - **孤立ページ**: `orphans` 結果（`Clippings/` 配下と `README.md` は想定内、それ以外は要確認）
   - **赤リンク多発**: `unresolved counts` 結果（閾値: 同一リンク 2 件以上の出現で要ページ化検討）
   - **古い claim**: `properties name=updated sort=count` の古い順でレビュー候補抽出 → 新ソースとの差分を LLM 確認
   - **cross-reference 不足**: 本来リンクすべき箇所で平文になっている記述（LLM 読解 + `backlinks` 結果で補強）
3. 直す前にユーザーに提示して方針確認
4. `log.md` に追記

## Obsidian CLI の利用方針

本 Vault は Obsidian に登録済み（`vault=llm-wiki` で呼び出し可能）。Obsidian 1.12.7 以降の CLI を**構造分析の補助**として使う。ただしページの書き込みは従来どおり `Write` / `Edit` を主にする（frontmatter と wikilink の繊細な制御のため）。

**使う場面:**
- Lint 初手の自動チェック（`orphans` / `unresolved` / `tags` / `properties` / `backlinks`）
- Ingest 直後の健全性確認（新規赤リンク・孤立が混入していないか）
- Query 時の関連ページ探索補助（`search` / `backlinks` / `links`）
- `log.md` への構造化追記（必要に応じて `obsidian vault=llm-wiki append path=log.md content="..."` が使えるが、通常は `Edit` で十分）

**使わない場面:**
- `wiki/**` 配下のページ本体の生成・更新（`create` / `append` / `prepend`）— frontmatter 制御のため `Write` / `Edit` で実施
- 破壊的操作（`delete` / `move` / `rename`）— 手動または確認付き `Bash` で実施
- `Clippings/**` への書き込み一切（スキーマ上不変）

**フォールバック:** Obsidian アプリ非起動時は従来どおり `Grep` / `Glob` / `Read` で同等の情報を得る。

## 運用上の方針

- **1 ソースで 10〜15 ページに波及して当然**。横着して sources ページだけ作って終わりにしない
- ページは**成長するもの**。既存ページを上書きせず、新情報を節として追加
- 完璧を目指さない。次の ingest で補強・訂正される前提でよい
- 出典は必ず frontmatter の `sources:` か本文 wikilink で辿れるようにする
- 一時メモや探索スクラッチは `~/tmp/` に置く（この Vault には入れない）

## 参考: 関連グローバルルール

- `~/.claude/CLAUDE.md`（ユーザーのグローバル設定）— 日本語優先、`~/tmp/` 利用など
- WebFetch で robots.txt に拒否されたら Playwright MCP で代替
- 一時ファイルは `~/tmp/` を使う
