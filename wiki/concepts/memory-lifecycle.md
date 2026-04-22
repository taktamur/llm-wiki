---
type: concept
updated: 2026-04-22
sources: [../sources/rohitg00-v2-gist.md, ../sources/blog-hayatetakeda.md]
aliases: [memory lifecycle, メモリライフサイクル]
---

# memory-lifecycle

[[rohitg00-v2-gist]] が [[llm-wiki]] v1 に追加した最大のレイヤー。**知識にはライフサイクルがある**という前提で、信頼度・世代交代・忘却・段階的昇格を導入する。

## 4 要素

### Confidence scoring

全 fact に信頼度をスコア化:

- ソース数（多いほど高い）
- 最終確認日（新しいほど高い）
- 矛盾の有無（あると減少）

> "Project X uses Redis for caching" → sources=2, last confirmed 3 weeks ago, confidence=0.85

### Supersession

新情報が古い claim を明示的に supersede。旧版は timestamp 付きで**保持**（削除しない）。

> knowledge version control

### Forgetting

Ebbinghaus の忘却曲線に従う減衰。

- アクセス・補強でリセット
- 未使用なら徐々にフェード（削除ではなく deprioritize）
- アーキテクチャ判断 = ゆっくり減衰、一過性バグ = 速く減衰

### Consolidation tiers

| 層 | 内容 | 寿命 | 圧縮度 |
|----|------|------|--------|
| Working memory | 直近の観察、未処理 | 短 | 低 |
| Episodic memory | セッション要約 | 中 | 中 |
| Semantic memory | クロスセッションの fact | 長 | 高 |
| Procedural memory | ワークフロー・パターン | 最長 | 最高 |

証拠が蓄積すると LLM が上位層に昇格させる。「一度見た」→「こう動くのが常」への距離を埋める仕組み。

## 本 wiki への適用可能性

現状は frontmatter に `updated:` があるのみ。以下は段階的導入候補:

- `confidence:` フィールド（0.0–1.0）追加
- `supersedes:` フィールドで旧ページを指す
- 各 claim に `{src:..., confirmed:...}` 脚注
- log.md ベースで「最後に言及されたのはいつ」を抽出して retention 判定

ただし**個人・メタ wiki 規模**では over-engineering になる可能性が高い。本ページはレベル 2 以降のスキーマ拡張用の reference として保持。

## 具体的な閾値提案（[[self-os]] 由来）

[[hayatetakeda]] の [[self-os]] では Self 領域のパターンページに以下の具体的な昇格条件を与える：

```yaml
---
type: pattern
first_observed: 2024-06-01
last_observed: 2026-04-10
observation_count: 12
confidence: medium       # low | medium | high
---
```

- `confidence: high` の条件: **観測 10 回以上かつ異なる状況で一貫**
- 3 週間分蓄積してからパターン抽出を開始
- 「自分についての確信はゆっくり育てるべき、LLM に急かされてはいけない」という設計原則

[[rohitg00-v2-gist]] の confidence scoring は 0.0–1.0 の連続値だったが、[[self-os]] は **low / medium / high の 3 段階 + observation_count 整数**という離散化された運用形態。小規模個人運用ではこちらの方が実用的。

## 関連ページ

- [[rohitg00-v2-gist]] — 出典
- [[llm-wiki]] — 適用先
- [[model-collapse]] — 緩和策としての効果
- [[knowledge-graph]] — 併用を推奨される構造層
- [[self-os]] — 具体的な閾値提案の源
- [[decision-archive]] — supersession 概念の類似適用
- [[blog-hayatetakeda]] — 追加ソース
