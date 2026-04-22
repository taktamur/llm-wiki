---
type: concept
updated: 2026-04-22
sources: [../sources/clip-taroumegane-llm-wiki.md]
aliases: [2層index, 2層 index/log, two-layer index, hierarchical index]
---

# two-layer-index

[[llm-wiki]] が**ページ数 100 超**の規模に達したときにコンテキスト肥大化を防ぐための、index.md / log.md の階層化パターン。[[taroumegane]] が [[clip-taroumegane-llm-wiki]] で提唱。

## 問題設定

[[karpathy-gist]] の原典は:

- **単一の `index.md`** にすべてのページを列挙
- **単一の `log.md`** にすべての操作を記録

この設計は 20〜50 ページ規模までは快適に動く。しかし、

- ページ数が 100〜数百に達すると、**query のたびに LLM が肥大化した index 全体をコンテキストに読み込む**ことになる
- 関係ないジャンルのエントリも毎回読むため、トークン消費が増え、関連ページ特定の精度も落ちる可能性

[[rohitg00-v2-gist]] は [[knowledge-graph]] や [[memory-lifecycle]] で検索精度側から対処するが、**index 構造側からの対処**は本パターン固有。

## 解決策 — 2 層化

```
llm-wiki/
├── index.md                # 全体: ジャンル一覧 + 統計のみ。個別ページは載せない
├── wiki/
│   ├── rdb/
│   │   ├── index.md        # ジャンル内の全ページ + ソースの詳細カタログ
│   │   ├── log.md          # ジャンル内の操作ログ
│   │   └── ...pages...
│   ├── rails/
│   │   ├── index.md
│   │   ├── log.md
│   │   └── ...pages...
│   └── general/            # 横断的な知識
│       └── ...
```

**全体 index.md のフォーマット例:**

```markdown
# LLM Wiki Index

## ジャンル
| ジャンル | ページ数 | ソース数 | 詳細 |
|---|---|---|---|
| RDB | 10 | 4 | [[wiki/rdb/index]] |
| Rails | 3 | 2 | [[wiki/rails/index]] |
```

**ジャンル index.md のフォーマット例:**

```markdown
# RDB Index

## ページ一覧
- [[SELECT最適化]] — SELECT 文の最適化の全体像（ソース: 3）
- [[インデックス戦略]] — インデックス設計の原則（ソース: 3）

## ソース一覧
- [[mysql-8-select-optimization]] — MySQL 8.0 Reference Manual 10.2.1
```

## LLM のナビゲーション動線

1. query 時、LLM はまず**全体 index.md** を読む → どのジャンルに関連するか判断
2. 該当ジャンルの `wiki/<genre>/index.md` を読む → 個別ページを特定
3. 個別ページを読む

トークン消費は「全体 index（小）+ ジャンル index（中）+ ページ群（対象のみ）」となり、**無関係ジャンルの index を毎回読まない**ことが効く。

## 全体 log.md の廃止

[[taroumegane]] の実装では**全体 log.md は作らず**、ジャンルごとの log.md のみ持つ。理由:

- 操作履歴はジャンル内で閉じるケースが大半
- 「最近 wiki 全体で何があったか」はジャンル log を横断 grep で得られる
- 単一 log は append-only で成長し続けるため、ジャンル別に分割したほうが管理しやすい

## 本 Vault への適用可否

本 Vault（`/Users/tak/wiki/llm-wiki/`）の現状:

- **17 ページ規模 / 5 ソース**（2026-04-22 時点）
- `wiki/{entities,concepts,sources}/` の**type 別分割**を採用（[[taroumegane]] のドメイン別分割とは方針が異なる）

現時点では単一 index.md で十分機能しており、2 層化の導入は不要。ただし:

- **発火条件**: ページ数 100 超、または `index.md` が 200 行を超えたら検討
- **導入時の選択肢**:
  - type 別維持 → `wiki/{entities,concepts,sources}/index.md` を各々作成
  - ドメイン別への再編 → `wiki/<domain>/` 単位に再配置（破壊的変更）

## 類似アイデアとの比較

- [[rohitg00-v2-gist]] の [[knowledge-graph]] — 構造化された型付きノード / エッジ層を重ねる。**検索精度**のアプローチ
- 本パターン — index のファイル分割。**コンテキスト効率**のアプローチ
- 両者は直交しており、併用可能

## 関連ページ

- [[llm-wiki]] — 基底パターン
- [[clip-taroumegane-llm-wiki]] — 提唱元
- [[taroumegane]] — 提唱者
- [[karpathy-gist]] — 原典（単一 index/log）
- [[page-decision-criteria]] — 同じ記事で提唱される別の補完ルール
