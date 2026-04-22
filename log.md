# log

ingest / query / lint の append-only ログ。

**フォーマット:**
```
## [YYYY-MM-DD] <ingest | query | lint> | <タイトル・要旨>
- 詳細...
```

`grep "^## \[" log.md | tail -N` で最新 N 件の履歴を確認できる。

---

## [2026-04-22] bootstrap | llm-wiki Vault 初期化

- 骨格作成: `CLAUDE.md`, `index.md`, `log.md`, `.gitignore`
- ディレクトリ作成: `raw/`, `raw/assets/`, `wiki/{entities,concepts,sources}/`
- Obsidian Vault 化はユーザー手動で実施（`.obsidian/` は触らない）

## [2026-04-22] ingest | Karpathy - LLM Wiki gist

- raw: [[karpathy-llm-wiki-gist]]
- summary: [[karpathy-gist]]
- touched: [[llm-wiki]], [[rag]], [[memex]], [[andrej-karpathy]], [[obsidian]], [[qmd]] (6 ページ新規作成)
- notes: 初回 ingest のため全ページ新規。以降の周辺議論 ingest で [[llm-wiki]] を中心に成長させる

## [2026-04-22] ingest | rohitg00 - LLM Wiki v2 gist

- raw: [[rohitg00-llm-wiki-v2-gist]]
- summary: [[rohitg00-v2-gist]]
- new: [[memory-lifecycle]], [[knowledge-graph]] (2 ページ新規)
- touched: [[llm-wiki]] (v2 拡張節追加、関連ページ大幅追加)
- notes: agentmemory 運用知見ベース。schema is the real product、lifecycle、knowledge-graph が主要貢献

## [2026-04-22] ingest | HN thread 47640875 - LLM Wiki 議論

- raw: [[hn-47640875-llm-wiki]]
- summary: [[hn-karpathy-llm-wiki]]
- new: [[model-collapse]], [[pkm]] (2 ページ新規)
- touched: [[llm-wiki]] (主要批判節追加)
- notes: 「RAG か否か」論争、model-collapse 懸念、qaadika の PKM 派反論、vbarsoum1 の実装規模（155K ワード / 210 ページ / 4597 xref）が主要な収穫

## [2026-04-22] ingest | Epsilla blog - LLM Wiki kills RAG

- raw: [[blog-epsilla-semantic-graph]]
- summary: [[blog-epsilla]]
- new: [[semantic-graph]], [[epsilla]] (2 ページ新規)
- touched: [[llm-wiki]] (エンタープライズ批判・代替節追加)
- notes: 自社プロダクト誘導バイアスあり。RBAC / 監査 / スケール観点は v2 の Privacy-governance と重複

## [2026-04-22] ingest | Ken Huang blog - Local LLM Wiki

- raw: [[blog-kenhuang-local-llm-wiki]]
- summary: [[blog-kenhuang]]
- touched: [[llm-wiki]] (実装バリエーション節に追加)
- notes: ローカル LLM (Gemma 4) 実装案。検証データ未提示、参照点として記録

## [2026-04-22] ingest | たろう眼鏡 - 【LLM Wiki】Obsidian x Claude Code で知識を構造化（Zenn）

- raw: `Clippings/【LLM Wiki】Obsidian x Claude Codeで学んだ知識を構造化し記憶媒体を脳からAIに移行する.md`（Obsidian Web Clipper 経由）
- summary: [[clip-taroumegane-llm-wiki]]
- new: [[taroumegane]], [[two-layer-index]], [[page-decision-criteria]]（3 ページ新規）
- touched: [[llm-wiki]]（実装バリエーション / 運用判断基準 / オープンな論点 節追加）, [[obsidian]]（Web Clipper 運用サイクル節追加）
- notes: 日本語圏初期の Claude Code 実装レポート。Karpathy gist にない運用判断基準 3 点（2 層 index/log, ページ判断基準, query 保存基準）を明文化。本 Vault の `Clippings/` スキーマ追加後の最初の ingest で、新ワークフロー（ステップ 1 スキップ、`wiki/sources/clip-<slug>.md` 命名）を実践

## [2026-04-22] lint | 初回 Vault 健全性チェック

- 総ページ数: 20（wiki 17 + meta 3: CLAUDE/index/log）
- raw ソース: 5 ファイル
- 総 wikilink: 126
- **孤立ページ: 0**（全ページが他から参照されている）
- **赤リンク: 0 実質**（検出された `[[page1]]` 等は CLAUDE.md の code block 内のテンプレート例のみ。Obsidian は code fence 内の wikilink を解決しない）
- 中心ハブ（inbound 順）:
  - [[llm-wiki]] (18) — 想定通り中核
  - [[karpathy-gist]] (11), [[rohitg00-v2-gist]] (9), [[rag]] (9)
  - [[knowledge-graph]] (7), [[obsidian]] (6), [[semantic-graph]] (6), [[epsilla]] (6)
- 所感: 初回 ingest 5 本で健全な相互参照構造が成立。次の段階は、Obsidian での graph view 確認（ユーザー作業）と query スモークテスト

## [2026-04-22] query | Obsidian CLI を wiki 管理に使えるか検討

- 背景: Obsidian 1.12.7 で標準搭載された Obsidian CLI（https://obsidian.md/ja/help/cli）を、本 Vault の維持管理に取り込めるか評価
- 調査: `obsidian vaults` で `llm-wiki` が登録済み（open 状態）確認 / `orphans` / `unresolved counts` / `tags counts` / `backlinks file=llm-wiki counts` を実地テストしすべて期待どおり動作
- 結論: Phase A（read-only の構造チェック補助 + 必要時の `log.md` append）を採用。ページ本体の書き込みは従来どおり `Write`/`Edit` ベース
  - Lint の初手として `orphans` / `unresolved` / `tags` / `properties` / `backlinks` を活用
  - Ingest 直後の新規赤リンク・孤立の混入確認に使う
  - Query 時の関連ページ探索補助に `search` / `backlinks` / `links` が使える
- CLI 実測値:
  - orphans: `Clippings/...` と `README.md` の 2 件（設計上想定内。wiki 配下の孤立ゼロを確認）
  - unresolved: `./Clippings/`, `./raw/`, `./wiki/`, `ページ名` の 4 件（いずれも CLAUDE.md の code fence 内テンプレート由来）
  - tags: `#clippings` のみ（Web Clipper 由来、wiki 本文では tags プロパティ未使用）
  - backlinks file=llm-wiki: 合計 50+ 件（ハブ性再確認）
- 反映: CLAUDE.md の Lint セクションに CLI 手順を追記 / 新規セクション「Obsidian CLI の利用方針」を追加（使う場面・使わない場面・フォールバック明記）
- new page: なし（検討結果は CLAUDE.md に組み込み、計画は `/Users/tak/.claude/plans/wiki-obsidian-cli-snuggly-kernighan.md`）
- 保留: Phase B（lint スクリプト化、ingest 前後 `unresolved` diff、`updated` 古い順レビューキュー）は 50〜100 ページ規模で再検討

## [2026-04-22] ingest | 日本語圏の llm-wiki 実装・関連 blog 5 本

- raw: [[blog-hayatetakeda-self-os]], [[blog-taichiendo-claude-obsidian-guide]], [[blog-yamapiiii-brain-second]], [[blog-ashidayo-llm-wiki-week1]], [[blog-yuimaru-llm-wiki-intro]]
- summary: [[blog-hayatetakeda]], [[blog-taichiendo]], [[blog-yamapiiii]], [[blog-ashidayo]], [[blog-yuimaru]]
- new concepts: [[self-os]], [[decision-archive]], [[ingest-compile-lint]], [[second-brain-git]]（4 ページ）
- new entities: [[hayatetakeda]], [[taichiendo]], [[yamapiiii]], [[ahshi-dayo]], [[yuimaru]], [[nvk-llm-wiki]]（6 ページ）
- touched: [[llm-wiki]]（実装バリエーション表に 5 著者追記 / 設計原理の批判的拡張節追加 / ワークフロー命名の並行定式化節追加 / 姉妹パターン節追加 / 日本語ユーザー特有の運用ノウハウ節追加 / 既成スキル採用ルート節追加 / オープン論点 4 件追加）, [[obsidian]]（日本語圏の採用パターン節 + 意図的不採用節追加）, [[taroumegane]]（日本語圏の並行実装者クロス参照表追加）, [[memory-lifecycle]]（self-os 由来の離散閾値提案節追加、sources に blog-hayatetakeda 追加）
- ユーザー原依頼: 「日本の blog などから、llm-wiki を実施した結果、どんなディレクトリ構成になったとかどんな skill 構成になったのか、などを調べてきて ingest」
- 対象著者の差別化軸（要約表は [[taroumegane]] に記録）:
  - [[taichiendo]]（2026-04-11）: ingest-compile-lint ワークフロー命名 / n8n・Bonsai-8B・Gemma 4 ADK 統合アーキテクチャ構想
  - [[yuimaru]]（2026-04-13）: 一般向け解説 + 中規模運用実測（約 100 資料）/ Dataview 活用 / 英語化 ingest ノウハウ
  - [[taroumegane]]（2026-04-14）: 運用判断基準の明文化（既 ingest 済み）
  - [[ahshi-dayo]]（2026-04-16）: nvk-llm-wiki エンドユーザー体験 / 「合成錬金釜」感覚
  - [[hayatetakeda]]（2026-04-19）: 設計原理の批判的拡張（self-os 3 領域×3 層、decision-archive の superseded_by 鎖）
  - [[yamapiiii]]（2026-03-01、Karpathy 非参照の姉妹）: second-brain-git / brain=Why・What / project=How / 14 コマンド × 13 エージェント
- notes:
  - yamapiiii は Karpathy gist 公開（2026-04）より前（2026-03-01）に類似パターンを発表。**非参照の姉妹パターン**として `> [!warning]` で明記し、混同を避けた
  - hayatetakeda の observation_count / low-medium-high の離散閾値を [[memory-lifecycle]] に具体例として組み込み（既存の 0.0–1.0 連続値との対比）
  - taichiendo の「ingest → compile → lint」命名は本 Vault の CLAUDE.md ワークフローと強い整合。[[ingest-compile-lint]] として独立ページ化
  - 英語化 ingest / 合成錬金釜感覚などの小さな観察は [[llm-wiki]] 本文に節として吸収し、新規ページは作成しない方針で過剰分化を回避
  - 今回 1 ソース → 最大 10〜15 ページ波及の想定どおり、5 ソース × ~3 ページ/ソース = 約 15 ページ差分が生じた

## [2026-04-22] lint | 日本語圏 5 本 ingest 後の健全性チェック

- Obsidian CLI 4 種（orphans / unresolved counts / tags counts / properties=updated）を実行
- 総ページ数（`updated:` 保持）: 37 ページ（前回 20 → +17）
- **孤立ページ**: 3 件
  - `Clippings/...`, `README.md`: **設計上想定内**（CLAUDE.md で除外宣言）
  - `2026-04-22.md`（0 バイト、vault ルート直下、作成時刻 14:52）: **新規発生の想定外**。ユーザーが Obsidian で意図せず daily note を作成した可能性。**LLM は削除せず、ユーザー判断に委ねる**
- **赤リンク**: 4 件（前回と同じ。いずれも CLAUDE.md の code fence 内テンプレート由来で実害なし）
  - `./Clippings/`, `./raw/`, `./wiki/`, `ページ名`
  - 今回 ingest で追加された 10 新規 concept/entity ページ由来の赤リンクは **ゼロ**（新規 wikilink がすべて解決）
- **タグ**: `#clippings` のみ（Web Clipper 由来。wiki 本文は tags プロパティ未使用方針）
- **新規ページのハブ性**:
  - [[llm-wiki]]: log.md からだけで 10 inbound（累計 50+、中核ハブ維持）
  - [[self-os]]: 9 inbound（llm-wiki 4, memory-lifecycle 4, hayatetakeda 2 等）— 十分接続
  - [[hayatetakeda]]: 15 inbound（llm-wiki 5, decision-archive 2, yamapiiii 1 等）— ハブ性高
  - [[second-brain-git]]: 6 inbound（llm-wiki 3, obsidian 2, yamapiiii 2 等）— 姉妹パターンとして妥当
  - [[taichiendo]], [[yuimaru]], [[ahshi-dayo]]: いずれも 2+ inbound（arr table + source + cross-ref 等）
- **所感**:
  - ingest による相互参照は健全。孤立・大量赤リンク・重複のいずれも検出されず
  - 要対応は `2026-04-22.md` の扱いのみ（ユーザー側の判断待ち）
  - 設計原理バリエーション（self-os / second-brain-git / ingest-compile-lint）を独立 concept として保持しつつ、本 Vault の採用方針との関係を明示できている
- フォロー: `2026-04-22.md`（空の daily note）はユーザー承認のうえ削除。孤立は設計上想定内の 2 件のみに復旧

## [2026-04-22] query | llm-wiki の構造パターン類型

- 質問: llm-wiki の構造のパターンとしてどのようなものがあるか
- answered via: [[llm-wiki]], [[self-os]], [[second-brain-git]], [[ingest-compile-lint]], [[two-layer-index]], [[memory-lifecycle]], [[decision-archive]], [[taroumegane]], [[hayatetakeda]], [[yamapiiii]], [[taichiendo]], [[ahshi-dayo]], [[yuimaru]]
- 3 軸で類型化して回答: (1) ストレージ・UI 構造（2 層/3 層/brain+project/番号付きフォルダ）(2) ワークフロー命名（Ingest-Query-Lint vs Ingest-Compile-Lint など）(3) メタデータ・ライフサイクル層の深さ（minimal → 2 層 index/log → v2 拡張 → 離散閾値 → decision-archive）+ 直交軸（UI 採否、実行基盤、自作 vs 既成、自動化レベル）
- new page: なし。`llm-wiki.md` の既存の実装バリエーション / 運用判断補完 / 設計原理拡張 / ワークフロー命名 / 姉妹パターンの各節を横断参照して回答。分類軸自体はメタ視点だが、新規の観察ではなく既存情報の整理のためページ化せず


