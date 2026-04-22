---
type: entity
updated: 2026-04-22
sources: [../sources/blog-yamapiiii.md]
aliases: [yamapiiii, やまぴー, y-ryuki]
---

# yamapiiii

株式会社ナハト所属の個人開発者。Qiita で 2026-03-01 公開の「[Claude Code で『第二の脳』を作り、最終的に AI が自律運用するようになった話](https://qiita.com/yamapiiii/items/cc2450f410b64329d275)」の著者。

## 本 Vault への貢献

[[blog-yamapiiii]] は [[karpathy-gist]] 公開（2026-04）より **1 ヶ月早い 2026-03** 公開のため、正確には [[llm-wiki]] 実装ではなく**姉妹パターン**として位置づけられる。ただし以下は llm-wiki の発展に寄与する知見：

- **[[second-brain-git]]** — Obsidian を意図的に不採用、Git リポジトリのみで運用するパターン
- **brain / project 分離** — 「brain = Why/What、project = How」の 2 リポジトリ戦略
- **14 コマンド × 13 エージェント** の具体例（Claude Code でここまで拡張できる実証）
- **3 層自律アーキテクチャ**（Execution / Judgment / Always-On）
- **ガードレール YAML** と **優先度スコアリング式**（`Score = 依存×3 + ラベル×2 + タイプ×2 + 工数 + 鮮度`）

## スタンス

- **Karpathy 非参照**: 独立に発展させた Second Brain 設計思想の延長線。PARA method の変形（番号付きフォルダ `00_Inbox` → `99_Archives` で「流れ」を作る）
- 「**AI の自律性は全か無かではなく、レイヤーごとに段階的に委譲**する」という設計知見
- **Obsidian 不採用の根拠**: Claude Code がファイルを直接読み書きできるため、閲覧 UI より**ファイルシステム直接性を優先**

## 所属

- 株式会社ナハト（広告・マーケティング系と推測）
- GitHub ユーザー名は `y-ryuki` と推測される（記事内で `Closes y-ryuki/brain#123` の例あり）

## 関連ページ

- [[llm-wiki]] — 姉妹パターン
- [[blog-yamapiiii]] — 本記事
- [[second-brain-git]] — 提唱パターン
- [[obsidian]] — **意図的に不採用**（比較対象）
- [[taichiendo]] — 対照的に Obsidian 採用
- [[hayatetakeda]] — 段階的自律化の思想を共有
