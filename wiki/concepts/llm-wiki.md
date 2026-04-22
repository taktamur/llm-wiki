---
type: concept
updated: 2026-04-22
sources: [../sources/karpathy-gist.md, ../sources/rohitg00-v2-gist.md, ../sources/hn-karpathy-llm-wiki.md, ../sources/blog-epsilla.md, ../sources/blog-kenhuang.md, ../sources/clip-taroumegane-llm-wiki.md, ../sources/blog-hayatetakeda.md, ../sources/blog-taichiendo.md, ../sources/blog-yamapiiii.md, ../sources/blog-ashidayo.md, ../sources/blog-yuimaru.md]
aliases: [LLM Wiki, llm wiki, エルエルエムウィキ]
---

# llm-wiki

[[andrej-karpathy]] が 2026 年 4 月に gist で提唱した、LLM を活用した**個人ナレッジベース構築パターン**。本 wiki の中核ページ。新ソースが ingest されるたびにここに差分追記していく。

## 一行定義

**「LLM が読むたびに、恒久的な wiki を段階的に構築・維持する」ことで、[[rag]] のように毎回ゼロから知識を再発見する方式を克服するパターン。**

## [[rag]] との違い

| 観点 | 従来の RAG | llm-wiki |
|------|-----------|----------|
| 知識の蓄積 | なし（毎回再発見） | あり（複利で増える） |
| cross-reference | クエリ時に探索 | 事前に wiki 内で解決済み |
| 矛盾の扱い | 都度露呈 | ingest 時に flag される |
| synthesis | 毎回合成 | 既に合成済み |
| 人間の役割 | 質問する | ソース選定・探索・問いを立てる |
| LLM の役割 | 検索 + 回答 | 検索 + 要約 + 相互参照 + ブックキーピング |

## 構成要素

### 3 層アーキテクチャ

1. **Raw sources** — 不変の原典。LLM は読むのみ
2. **The wiki** — LLM 生成の markdown 群。LLM が全管理
3. **The schema** — `CLAUDE.md` / `AGENTS.md` 等。LLM がどう働くかを規定

### 3 つの操作

- **Ingest**: 新ソースを取り込む。1 ソースで 10〜15 ページに波及するのが典型
- **Query**: wiki に質問する。良い回答は wiki に書き戻す
- **Lint**: 健康診断（矛盾・孤立・古い記述・不足 cross-reference の検出）

### 2 つの特殊ファイル

- **index.md** — コンテンツ志向のカタログ。query 時に最初に読む
- **log.md** — 時系列の append-only ログ。`grep "^## \["` で履歴取得

## 規模感

- ~100 ソース / 数百ページ規模までは **embedding RAG なしで index.md だけで機能する**
- それ以上になったら [[qmd]] のようなローカル検索エンジンを導入する選択肢

## 推奨 UI と道具立て

- [[obsidian]] — wikilink、graph view、Dataview、Marp、Web Clipper との親和性
- git — 単なる markdown リポジトリなので version 管理・branch・collaboration が無料
- Obsidian Web Clipper — 記事を markdown 化して raw に投入
- 画像のローカル化（attachment folder に固定）

## 思想的源流

[[memex]] — Vannevar Bush が 1945 年に As We May Think で提唱した個人知識装置。Bush の未解決問題「誰が維持するか」に対し、LLM を maintainer として据えたのが llm-wiki の本質的貢献。

## 適用ドメイン

Karpathy が gist で挙げた例:

- 個人の記録（目標・健康・自己理解）
- リサーチ（論文・レポートの継続読み）
- 読書（Tolkien Gateway のような fan wiki を個人規模で）
- 業務（Slack・議事録・顧客通話を源にした社内 wiki）
- 競合分析・デューデリ・旅行計画・講義ノート・趣味の深掘り

## なぜ機能するか（Karpathy の主張）

ナレッジベースを放棄する最大の理由は「維持コスト > 価値増加」。LLM は:

- 飽きない
- cross-reference の更新を忘れない
- 1 パスで 15 ファイル触れる

結果、維持コストが near-zero に落ち、wiki が成長し続ける。

## 実装上のキー洞察

- 「知識は複利で蓄積すべき、毎回再発見すべきでない」
- 「良い回答は wiki に書き戻す」（探索も蓄積）
- 「1 ソースで 10〜15 ページに波及するのが普通」
- 「ドキュメント（この gist）は意図的に抽象的。実装は自分の LLM と共作せよ」

## 後続議論と拡張

### v2 拡張（[[rohitg00-v2-gist]]）

原典後に公開された **LLM Wiki v2** が、agentmemory での本番運用知見を元に以下を追加:

- [[memory-lifecycle]] — confidence scoring / supersession / forgetting / consolidation tiers
- [[knowledge-graph]] — 型付きノード + 型付きエッジをページに重ねる
- ハイブリッド検索（BM25 + ベクトル + graph traversal）
- イベント駆動オートメーション（on new source / session start / session end 等）
- 自己修復 lint（orphan 自動リンク、stale 自動マーク、contradiction 自動解決）
- Multi-agent mesh sync / private-shared scoping
- Privacy & governance（ingest 時の secret strip / audit trail）
- Crystallization（完了したワークを構造化ダイジェスト化）

v2 自身が強調するのは「**スキーマこそが真のプロダクト**」という点。`CLAUDE.md` の質がそのまま wiki の質になる。

### 主要な批判

- **「これは RAG ではないか」論争**（[[hn-karpathy-llm-wiki]] / [[rag]]）— vector DB を使わないだけで、LLM が動的に wiki を構築・更新する点で区別される
- **[[model-collapse]] リスク**（[[hn-karpathy-llm-wiki]]）— LLM が自身の出力を再書き換えするループによる情報劣化。`raw/` 不変性で部分緩和、v2 の supersession / confidence で追加緩和
- **[[pkm]] 派からの反論**（[[hn-karpathy-llm-wiki]] / qaadika）— 「書くこと自体が思考」。llm-wiki は「ブックキーピングの外注」と再定義すれば両立可能
- **エンタープライズ要件の不足**（[[blog-epsilla]] / [[epsilla]]）— RBAC / 監査 / スケール。[[semantic-graph]] が代替提示

### 実装バリエーション

- [[blog-kenhuang]] — ローカル LLM（Gemma 4）で動かすプライバシー保護型
- [[clip-taroumegane-llm-wiki]] — **Claude Code skill としての日本語実装**（[[taroumegane]] / dely クラシル社）。`/skill-creator` に gist を投げて SKILL.md を生成し、実 ingest を通じて補完判断基準を逆フィード
- 実装例（HN より）: [Semiont](https://github.com/The-AI-Alliance/semiont), [MCPTube](https://github.com/0xchamin/mcptube), [CacheZero](https://github.com/swarajbachu/cachezero), [Memoriki](https://github.com/AyanbekDos/memoriki), [llmwiki.app](https://llmwiki.app), [grimoire-pt5](https://grimoire-pt5.sprites.app/)

#### 日本語圏の実装バリエーション（2026-04 集中期）

2026-04 の [[karpathy-gist]] 公開を契機に、日本語圏で**約 2 週間に集中して**多様な実装・解説が公開された：

| 著者 | 日付 | 差別化の軸 | 主要貢献 |
|------|------|-----------|---------|
| [[taichiendo]] | 2026-04-11 | **ワークフロー命名** | [[ingest-compile-lint]] として 3 フェーズ定式化。n8n + Bonsai-8B + Gemma 4 ADK の統合アーキテクチャ構想 |
| [[yuimaru]] | 2026-04-13 | **解説＋中規模実測** | 約 100 資料・数 100 ページの運用。**英語化 ingest** の実践 |
| [[taroumegane]] | 2026-04-14 | **運用判断基準の明文化** | [[two-layer-index]] / [[page-decision-criteria]] / query 保存基準 |
| [[ahshi-dayo]] | 2026-04-16 | **エンドユーザーレポート** | [[nvk-llm-wiki]] 採用、1 週間 30 記事、「合成錬金釜感覚」「長期記憶懸念」 |
| [[hayatetakeda]] | 2026-04-19 | **設計原理の批判的拡張** | [[self-os]]（3 領域×3 層）、[[decision-archive]]、領域別自動化レベル |

公開順に見ると、**命名 → 解説 → 運用判断 → ユーザー体験 → 設計原理拡張**と日本語圏の議論が階段状に発展している。

参考: [[blog-yamapiiii]]（[[yamapiiii]] / ナハト、2026-03-01）は [[karpathy-gist]] 公開**前**の **姉妹パターン**（[[second-brain-git]]）。Obsidian 不採用で Git のみ、14 コマンド × 13 エージェント × 3 層自律アーキテクチャを先行提示。

### 運用判断基準の補完（[[clip-taroumegane-llm-wiki]]）

Karpathy gist は実装詳細を意図的に抽象化しているため、実 ingest 時に**判断基準の欠如**が浮上する。[[taroumegane]] の実装報告が明文化した 3 点:

- [[two-layer-index]] — ページ数 100 超でのコンテキスト肥大化を防ぐ index/log の階層化
- [[page-decision-criteria]] — ingest 時の「新規 / 更新 / 分割 / 横断」の発火条件
- **query 結果の wiki 保存基準** — 「新しい切り口 / 新コンセプト / 汎用的な分析」なら保存、「既存知識の組み合わせ / 個別具体すぎ / 追記で済む」なら保存不要。最終判断はユーザー

これらは本 Vault のスキーマ（`CLAUDE.md`）が将来拡張する余地を示唆している。

### 設計原理の批判的拡張（[[blog-hayatetakeda]]）

[[hayatetakeda]] は Karpathy 設計を「**外向き**」（外部知識のみ）と診断し、「内向き＋外向き」の [[self-os]] 拡張を提示：

- **3 領域** (`Knowledge/` / `Decisions/` / `Self/`) + メタ層 `Synthesis/`
- **3 層** (`Raw/` → `Sources/` → `Wiki/`) — 中間層 `Sources/` を挟む
- **[[decision-archive]]** — 意思決定ログは書き換えない、`superseded_by` で連鎖
- **領域別自動化レベル** — Knowledge は自動、Decision は確認、Self は提案のみ
- **自己成就的予言の回避** — Self 領域のパターン抽出は必ず人間を挟む

`confidence: low | medium | high` の昇格条件（**観測 10 回以上かつ異なる状況で一貫**）は [[memory-lifecycle]] の confidence 概念に**具体的な閾値**を与える。

### ワークフロー命名の並行定式化（[[blog-taichiendo]]）

[[taichiendo]] は Karpathy 原典の workflow を **[[ingest-compile-lint]]** として命名：

- **Ingest**: 生テキストを `inbox/` に投入（整理不要）
- **Compile**: Claude Code が `wiki/` に構造化・WikiLink 付け
- **Lint**: 矛盾 / 孤立 / 未リンク / 鮮度の 4 観点チェック

本 Vault の `Ingest / Query / Lint` とは別系統。**Compile を独立フェーズとして可視化**する点が貢献。また `outputs/` 層（派生コンテンツ置き場）を wiki/ と分離する構成もユニーク。

統合アーキテクチャ構想（n8n + Bonsai-8B + Gemma 4 ADK + Obsidian Git）で、**Phase 1 を自動化**する道筋も提示。

### 姉妹パターン: [[second-brain-git]]（[[blog-yamapiiii]]）

Karpathy 非参照だが構造的に類似する姉妹パターン（2026-03 公開、gist 公開より前）：

- **Obsidian 不採用**: Git リポジトリ + Claude Code のみ。閲覧 UI よりファイルシステム直接性を優先
- **brain / project 分離**: 思考 (Why/What) と実装 (How) を別リポジトリに分離、Issues は brain に一元化
- **14 コマンド × 13 エージェント**: Claude Code の slash command と agents/ 拡張を大規模に使う
- **3 層自律アーキテクチャ**: Execution → Judgment → Always-On の段階的委譲
- **ガードレール YAML**: `max_issues_per_chain: 3` など安全装置を明示

[[hayatetakeda]] の「領域ごとに自動化レベルを変える」と [[yamapiiii]] の「レイヤーごとに段階的に委譲」は独立に到達した共通知見。

### 日本語ユーザー特有の運用ノウハウ

- **英語化 ingest**（[[blog-yuimaru]]）: 「英語がいいらしいので翻訳してから取り込む」。LLM 精度と翻訳コストのトレードオフ。本 Vault は「全て日本語」方針（[[CLAUDE]] 明記）だが、ingest 時の言語選択は論点として残る
- **ガベージイン・ガベージアウト**（[[blog-yuimaru]]）: ソース選定が wiki 品質を支配
- **合成錬金釜感覚**（[[blog-ashidayo]]）: ingest 順序・組み合わせが wiki 構造に**経路依存的に影響**
- **長期記憶定着率低下の懸念**（[[blog-ashidayo]]）: AI に作業委譲することで「気持ちよさ」だけが残り、ユーザーの実質的な理解が育たない可能性

### 既成スキル採用ルート

自作派だけでなく **[[nvk-llm-wiki]]（lobster-wiki）** のような既成スキルを採用する選択肢も実例あり（[[ahshi-dayo]]）。自作コストなしで始められるが、SKILL.md を自身で洗練する過程が得られない trade-off。

### 実運用報告（HN vbarsoum1）

155K ワード（Alex Hormozi 著作 3 冊）を処理 → **210 コンセプトページ / 4,597 相互参照**を生成、11 ファイル横断の矛盾検出成功。「VectorDB は機械用、wiki は人間 + 機械の双方用」。スケール感の現実的な参照点。

## オープンな論点（本 Vault 固有）

- 本 Vault は現状 **Minimal viable 段階**（原典相当）。v2 の各レイヤーをいつ・どこまで導入するか
- `CLAUDE.md` のスキーマ拡張（frontmatter の `confidence:` / `supersedes:` 等）
- [[page-decision-criteria]] を `CLAUDE.md` の Ingest ワークフローに組み込むべきか（現状は概念ページにのみ記載）
- ページ数 100 超到達時に [[two-layer-index]] を導入するかの発火条件
- ユーザー固有の意見・判断を [[pkm]] 方式で保護する領域を設けるか
- `wiki/` 内の繰り返し書き換えによる [[model-collapse]] を定期 lint で監視する基準
- ingest 時の言語選択（全日本語 vs 英語化 ingest）の trade-off をどう扱うか（[[blog-yuimaru]] 由来）
- `Sources/` 中間層（[[self-os]] / [[hayatetakeda]] 提唱）を挟む必要があるか（現状は raw → wiki の 2 層）
- 意思決定記録層（[[decision-archive]]）を導入するかどうか（現状 log.md に混在）
- ingest 順序の経路依存性（合成錬金釜感覚、[[blog-ashidayo]]）を lint で検出できるか

## 関連ページ

- [[karpathy-gist]] — 原典ソース
- [[rohitg00-v2-gist]] — 拡張 gist
- [[hn-karpathy-llm-wiki]] — HN 議論
- [[blog-epsilla]] — エンタープライズ視点の批判
- [[blog-kenhuang]] — ローカル LLM 実装案
- [[rag]] — 対比概念
- [[memex]] — 思想的源流
- [[andrej-karpathy]] — 提唱者
- [[obsidian]], [[qmd]] — 推奨ツール
- [[memory-lifecycle]], [[knowledge-graph]] — v2 追加レイヤー
- [[model-collapse]], [[pkm]] — 主要批判軸
- [[semantic-graph]], [[epsilla]] — 代替アプローチ
- [[two-layer-index]], [[page-decision-criteria]] — 実装時の運用判断補完
- [[taroumegane]], [[clip-taroumegane-llm-wiki]] — Claude Code skill 実装報告
- [[self-os]], [[decision-archive]], [[ingest-compile-lint]], [[second-brain-git]] — 2026-04 以降の拡張パターン
- [[hayatetakeda]], [[taichiendo]], [[yamapiiii]], [[ahshi-dayo]], [[yuimaru]] — 日本語圏実装者・解説者
- [[nvk-llm-wiki]] — 既成スキル採用ルート
