---
type: source
updated: 2026-04-22
url: https://note.com/humble_bobcat51/n/na7bac12266ad
author: ゆいまる
fetched: 2026-04-22
published: 2026-04-13
lang: ja
sources: ["../../raw/blog-yuimaru-llm-wiki-intro.md"]
aliases: [ゆいまる LLM Wiki 解説, 英語化ingest, Dataview, 100資料運用]
---

# ゆいまる: LLM Wiki とは？ Karpathy が提唱した「自分専用AI知識基地」 (2026-04)

note 記事（2026-04-13）。非エンジニア向けの**解説記事**。[[andrej-karpathy]] と [[karpathy-gist]] を大枠解説しつつ、**著者自身の運用規模（約 100 資料・数 100 ページ）** と **英語化 ingest ノウハウ**が含まれる。「IT 界隈以外で AI を使いまくる 2005 年生まれ」を自称。

## このソースの位置付け

- 日本語圏で最初期の**一般向け解説**（対象読者が非エンジニア）
- 実装詳細は薄いが、**中規模運用（100 資料 / 数 100 ページ）の一次報告**が含まれる
- **「英語化してから ingest」**という日本語ユーザー特有の運用ノウハウを明記した貴重な記録
- [[karpathy-gist]] だけでなく **AutoResearch / Software 3.0** などの Karpathy 関連概念も解説し、**Karpathy の思想圏をまとめて紹介**する位置づけ

## 運用規模（一次報告）

> 中規模（約 100 資料、数 100 ページ程度）まではindex.mdという目次ファイルだけで十分にナビゲーション可能で、重いベクトル検索インフラを必要としません。

- 規模: 約 100 資料、数 100 ページ
- インフラ: index.md のみ（ベクトル検索なし）
- Karpathy 本人の事例として **40 万語規模** も言及

[[two-layer-index]] の「100 ページ超で index が肥大化」という [[taroumegane]] の観察と**規模感が一致**。ゆいまるの事例は「まだ 2 層化が必要ない規模」の実運用として貴重。

## 日本語特有の運用ノウハウ: 英語化 ingest

著者の実践：

> **英語での運用について**：英語がいいらしいので、著者は翻訳しておくようにしているとのこと。

- **ingest 時にソースを英語に翻訳してから投入**
- 理由: 「英語がいいらしい」（LLM の精度差を意識）
- Wiki 本体は英語で構築、出力時に日本語で呼び出すスタイル

日本語ユーザーの運用選択肢として重要：

| 方針 | 採用者 | トレードオフ |
|------|--------|------------|
| 全て日本語 | [[taroumegane]] / [[taichiendo]] / [[hayatetakeda]] / 本 Vault | LLM 精度はやや低い・可読性高 |
| ソース英語化 | ゆいまる | LLM 精度高・翻訳コスト |

本 Vault は「全て日本語」方針（CLAUDE.md 明記）だが、**ingest 時の言語選択は今後議論すべき論点**として記録。

## 使用ツール（具体的な組み合わせ）

- **Claude Code**（ingest / query / lint）
- **Obsidian**（閲覧 UI、グラフビュー）
- **[[obsidian]] Web Clipper**（Web 記事を Markdown 化して即投入）
- **Dataview プラグイン**（動的にページを集約・可視化）
- **画像保存**（ローカル保存で LLM が参照可能に）
- **qmd**（[[qmd]]、将来の大規模化に備えた CLI 検索ツールに言及）

[[obsidian]] + Claude Code + Web Clipper + Dataview の組み合わせは [[taroumegane]] と共通するが、**Dataview プラグイン**の活用は本記事が明示。

## 知識の複利成長論

著者の主張（Karpathy 論の平易な解説）：

- RAG は「毎回ゼロから再発見」= ステートレス
- LLM Wiki は「1 回コンパイル → 以後 wiki 固定化・更新」= 複利成長
- クロスリファレンスが既に張られ、矛盾はフラグ付け、合成は常に最新

[[rag]] / [[llm-wiki]] の対比として既に本 Vault で扱われている内容と整合。

## 類似ツールとの比較（6 種類）

1. **RAG** — ステートレス、大規模可、合成精度不安定
2. **Obsidian 単独 + Zettelkasten** — 人力メンテ、llm-wiki の「前身」
3. **Notion / Notion AI** — クラウド、UI 綺麗、合成はllm-wiki 劣勢
4. **Logseq / Roam** — ブロック粒度、日記統合
5. **専用 AI 知識ツール（Dume / Mem / Read AI）** — ノーコード UI、自由度低い
6. **OSS 拡張（LlamaIndex / Fabric / qmd）** — llm-wiki の補完

本 Vault 既存ページ [[rag]] / [[obsidian]] / [[semantic-graph]] / [[qmd]] と整合。

## 著者の感想（ガベージイン・ガベージアウト）

> 「これ使ってから、かなり知識の選定がしやすくなった。渡す知識がゴミすぎるとやっぱり出てくる結果もしょうもない。ガベージいん、ガベージあうと。」

**ソース選定の重要性**を端的に表現。[[blog-ashidayo|あーしだよ！]] の「合成錬金釜感覚」（順序依存）と呼応する一次教訓。

## 付随的に解説される Karpathy 他概念

記事後半では [[andrej-karpathy]] の他の提唱についても言及：

- **Software 1.0 / 2.0 / 3.0**（2025 YC AI Startup School keynote）: プロンプト（英語）がプログラム
- **AutoResearch / Karpathy Loop**（2026-03）: AI が自律的に研究・実験ループ
- **nanoGPT / micrograd / char-rnn**: 教育用ミニマム実装
- **A Recipe for Training Neural Networks**（2019）

本 Vault の [[andrej-karpathy]] ページに **Software 3.0 / AutoResearch** を追補する根拠として使える。

## 本 Vault との差分メモ

| 観点 | 本 Vault 現状 | ゆいまる運用 |
|------|-------------|------------|
| 言語方針 | 全て日本語（CLAUDE.md 明記） | ソース英語化 ingest |
| Dataview | 未使用 | 活用 |
| 規模 | 小（20 ページ規模） | 中（100 資料・数 100 ページ） |
| qmd | [[qmd]] として紹介のみ | 大規模化候補として言及 |

## 関連ページ

- [[llm-wiki]] — パターン本体
- [[karpathy-gist]] — 原典
- [[yuimaru]] — 著者
- [[andrej-karpathy]] — Software 3.0 / AutoResearch 言及
- [[obsidian]] — Web Clipper + Dataview 活用
- [[qmd]] — 大規模化候補ツール
- [[rag]] — 対比
- [[blog-ashidayo]] — 並行する初期ユーザーレポート
- [[two-layer-index]] — 100 ページ規模の実運用事例として援用可能
