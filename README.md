# llm-wiki

Andrej Karpathy が提唱した **[LLM Wiki パターン](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)** に沿って運用する個人ナレッジベース。題材は「llm-wiki という考え方それ自体」（メタ wiki）。

Obsidian で Vault として開いて閲覧、Claude Code で更新する。

## 迷ったらこの 3 つだけ

1. **「これ ingest して」** — 新しいソース（URL / ファイル / 会話内容）を取り込んで wiki に反映
2. **「これ lint して」** — 矛盾・孤立ページ・古い記述を検出して健康診断
3. **「これ wiki に書き戻して」** — 今の探索・議論結果を `wiki/` の新規ページとして蓄積

Claude Code をこのディレクトリで起動すれば [`CLAUDE.md`](./CLAUDE.md) が自動で読み込まれ、ワークフローに従って動きます。

## ディレクトリ構造

| パス | 役割 |
|------|------|
| [`raw/`](./raw/) | 手動取り込みの原典ソース（不変・LLM は読むのみ） |
| [`Clippings/`](./Clippings/) | Obsidian Web Clipper が自動保存する原典（不変・LLM は読むのみ） |
| [`wiki/`](./wiki/) | LLM が生成・維持する日本語ページ（entities / concepts / sources） |
| [`CLAUDE.md`](./CLAUDE.md) | 運用スキーマ（Claude への指示書） |
| [`index.md`](./index.md) | ページカタログ。まずここから |
| [`log.md`](./log.md) | ingest / query / lint の時系列ログ |

## 閲覧の入口

- [`index.md`](./index.md) — 全ページのカタログ
- [`wiki/concepts/llm-wiki.md`](./wiki/concepts/llm-wiki.md) — 本 Vault の中核ページ

## セットアップ（初回のみ）

1. Obsidian アプリで「フォルダを Vault として開く」→ このディレクトリを選択
2. Settings → Files and links → Attachment folder path = `raw/assets/`
3. （任意）Dataview / Marp プラグイン導入
