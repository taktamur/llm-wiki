---
type: source
updated: 2026-04-22
url: https://news.ycombinator.com/item?id=47640875
author: Hacker News community
fetched: 2026-04-22
lang: en
points: 296
comments: 95
sources: [../../raw/hn-47640875-llm-wiki.md]
aliases: [HN 47640875, HN thread]
---

# HN thread 47640875: LLM Wiki gist の議論 (2026-04)

[[karpathy-gist]] を紹介する HN スレッド。296 pt / 95 コメント。肯定・懐疑・既実装報告・代替案が並ぶ主戦場。

## 主な論点

### 肯定・実践報告

- **vbarsoum1**: Alex Hormozi 著作 3 冊（155K ワード）を処理し、210 コンセプトページ・4,597 相互参照を生成、11 ファイル横断の矛盾検出に成功。「VectorDB は機械用、wiki は人間 + 機械の双方用」という整理
- **asakin**: メタデータ標準、30 日学習期間、stale content linting を自前実装済み
- **voidhorse**: 「agentic 開発者はすでにこれをやっている。harness が既に有効化している。知識ディレクトリを各会話終了時に更新するよう依頼すれば済む」

### 「これは RAG では？」論争

- **kenforthewin**: 「これは RAG。vector DB を使っていないだけで、階層的セマンティック構造で retrieval を補助しているに過ぎない」
- **mememememememo**: 「RAG の compaction」と短評
- 反論: wiki を **LLM が構築・更新する動的プロセス**である点が静的コーパス RAG と違う。詳しくは [[rag]]

### [[model-collapse]] 懸念

- **devnullbrain**: [Nature 2024 の論文](https://www.nature.com/articles/s41586-024-07566-y) を引き、LLM 自己参照で情報劣化が起こるはずと指摘。「LLM でドキュメントを書き続ければ、有効情報の書き換えによる slop 化が目に見えて起こる」
- 本 wiki は原典を `raw/` に不変保存することでこのリスクを緩和できるが、`wiki/` 内の再書き換えループでは残る論点

### [[pkm]] の価値 vs. 自動化

- **qaadika**（4,100 語の長大コメント）: Obsidian で 4,100 ノート自作。「AI が書いた知識ベースには個人性がない。**書く過程こそが価値**。思考を深める shower thoughts 的プロセスを失う」。AI 生成は `==BEGIN AI-GENERATED==` マーカーで隔離
- **kmaitreys**: 「自分の脳を機械に委ねるのか？」という素朴な問い

### 思想的源流

- **Vetch**: Licklider "Man-Computer Symbiosis" (1960) の引用。人間が目標・仮説・動機、機械がルーチン処理。[[memex]] と並ぶ源流候補

### 技術改善アイデア

- **j-pb**: トークン競合回避のため ASCII 接頭辞（△top など）で用語曖昧性排除・オートコンプリート重複削減
- **cyanydeez**: 「Context pollution が過剰。DAG で choose-your-own-adventure 的に LLM が自分で context を構築・剪定すべき」
- **gchamonlive**: 「コードも docs も planning も epics も ADR も全部リポに置け」（[リポ例](https://github.com/gchamon/archie/tree/main/docs)）

### 既存ツール・類似実装

- **atbpaca**: 「Confluence を LLM が維持するアイデアは面白い」
- **argee**: Semiont ([The-AI-Alliance/semiont](https://github.com/The-AI-Alliance/semiont)) が近い試み、と紹介
- **0123456789ABCDE**: grimoire-pt5.sprites.app を「validating」と紹介
- **nurettin**: Claude memory 等の既存機能との差別化が弱いと指摘
- **mbreese**: 自分の RAG で entity 抽出・関係抽出をやっている。LLM 生成 wiki をその上に載せる構想に賛同
- 関連 Show HN: [MCPTube](https://github.com/0xchamin/mcptube), [CacheZero](https://github.com/swarajbachu/cachezero), [Memoriki](https://github.com/AyanbekDos/memoriki), [llmwiki.app](https://llmwiki.app) など実装事例多数

## このソースから本 wiki に追加する知見

- **矛盾リスク**: wiki 自己書き換えによる [[model-collapse]] → raw 不変性で部分緩和、`CLAUDE.md` でループ防止
- **人間側の価値**: 書くこと自体が思考。llm-wiki は「思考を外注する」ではなく「ブックキーピングを外注する」と再強調が必要
- **既実装者の規模感**: 155K ワード → 200+ ページ / 4,600+ 相互参照は現実的
- **類似ツール群**: Semiont, MCPTube, CacheZero, Memoriki, llmwiki.app, grimoire 等を将来 ingest 候補に

## 関連ページ

- [[karpathy-gist]] — 議論の対象
- [[llm-wiki]] — パターン本体
- [[rag]] — 「これは RAG」論争の相手
- [[model-collapse]] — 懸念
- [[pkm]] — qaadika の反論軸
- [[memex]] — 思想的源流
