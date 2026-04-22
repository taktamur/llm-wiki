---
type: concept
updated: 2026-04-22
sources: [../sources/hn-karpathy-llm-wiki.md]
aliases: [PKM, Personal Knowledge Management, 個人知識管理]
---

# pkm

**Personal Knowledge Management** — 個人が自分で書き、繋ぎ、維持するノートシステム。Obsidian、Roam Research、Logseq、Scrapbox、Tiddlywiki、Zettelkasten 等の文脈で語られる。

[[hn-karpathy-llm-wiki]] で **qaadika** が [[llm-wiki]] への主要な反論軸として提示した立場。

## qaadika の主張（要約）

> You never (or rarely) write the wiki yourself — the LLM writes and maintains all of it.

この記述に対し、

- **書く過程こそが価値**である。ノート作成は思考を深める行為で、shower thoughts 的な予期せぬ洞察を生む
- **4,100 ノート**を Obsidian で自作しているが、AI 生成コンテンツは `==BEGIN AI-GENERATED==` マーカーで隔離している
- LLM に書かせた知識ベースは**個人性**（personality）を失う

## [[llm-wiki]] との両立

Karpathy は「人間はソース選定・探索・問いを立てることに集中、LLM はブックキーピング」と分業を提案している。qaadika の批判は「**書く＝思考**を外注してはならない」という point で、llm-wiki は「**ブックキーピングを外注する**」と位置付ければ原理的には両立可能。

ただし実運用上の境界は曖昧:

- wiki ページの要約は「書く」に入るのか、「ブックキーピング」に入るのか
- LLM に 100% 任せると思考機会を失う
- 人間がドラフトを書き、LLM が cross-reference・矛盾検出・書式統一を担う役割分担が現実的な中道

## 本 wiki の方針

本 Vault は**メタ wiki**（llm-wiki について学ぶ wiki）なので、LLM に書かせることに抵抗は小さい。しかし、以下は人間（ユーザー）側に残す:

- 何をソースとして選ぶか
- どの主張を強調するか（ingest 時の短いレビュー）
- 自分の実運用上の意見・判断（LLM には見えない体験）

ユーザー固有の意見・評価は `wiki/` ページ内に節として追加し、LLM の書き換え対象から保護する運用を検討中。

## 関連ページ

- [[llm-wiki]] — 対比される分業モデル
- [[hn-karpathy-llm-wiki]] — qaadika 批判の出典
- [[obsidian]] — qaadika の使用ツール
