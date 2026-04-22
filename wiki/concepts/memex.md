---
type: concept
updated: 2026-04-22
sources: [../sources/karpathy-gist.md]
aliases: [Memex, メメックス]
---

# memex

Vannevar Bush が 1945 年の論文 "As We May Think"（The Atlantic 掲載）で提唱した**個人知識装置**の構想。[[llm-wiki]] の思想的源流として [[karpathy-gist]] 末尾で明示的に参照されている。

## Bush の構想

- 個人が curate する知識ストア
- ドキュメント間に **associative trails**（連想的な繋がり）を張る
- 繋がり（trails）そのものがドキュメントと同等の価値を持つ
- プライベートで能動的に維持される

## web との対比

Karpathy の整理:

> Bush's vision was closer to this than to what the web became: private, actively curated, with the connections between documents as valuable as the documents themselves.

実現した web は「公開・受動的・リンクが薄い」方向へ進化した。Bush が思い描いたのはむしろ個人ナレッジベースに近い姿だった、という読み替え。

## Bush が解けなかった問題と [[llm-wiki]] の貢献

> The part he couldn't solve was who does the maintenance. The LLM handles that.

memex の唯一の難所は「誰が関連付けを張り、更新し続けるか」という**維持者問題**。[[llm-wiki]] はこれに対し LLM を maintainer として据えることで答えを出した、というのが Karpathy の主張。

## 関連ページ

- [[llm-wiki]] — memex の現代的実装と位置付けられるパターン
- [[karpathy-gist]] — memex への言及元
