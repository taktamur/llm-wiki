---
type: source
updated: 2026-04-22
url: https://qiita.com/yamapiiii/items/cc2450f410b64329d275
author: yamapiiii（株式会社ナハト）
fetched: 2026-04-22
published: 2026-03-01
updated_src: 2026-03-04
lang: ja
sources: ["../../raw/blog-yamapiiii-brain-second.md"]
aliases: [第二の脳, Git Second Brain, brain=Why/What project=How, 14コマンド 13エージェント]
---

# yamapiiii (ナハト): Claude Code で「第二の脳」を作り AI 自律運用に進化させた話 (2026-03)

Qiita 記事（2026-03-01、更新 2026-03-04）。[[yamapiiii]]（株式会社ナハト）が **Git リポジトリ + Claude Code** のみで第二の脳（Second Brain）を構築し、**14 コマンド × 13 エージェント × 3 層自律アーキテクチャ** で最終的に AI が自律運用するように進化させた実例。

> [!warning]
> **Karpathy 非参照**: 本記事には [[andrej-karpathy]] や [[llm-wiki]] への明示的言及なし。Second Brain 設計思想（Tiago Forte 系）の延長として独立に構築されたもの。本 Vault では「llm-wiki と並行発展した**姉妹パターン**」として扱う。

## このソースの位置付け

- **[[karpathy-gist]] 公開（2026-04）より 1 ヶ月前**の 2026-03 公開。llm-wiki ブーム前に成立した並行事例として重要
- **Obsidian ではなく Git リポジトリのみ**で運用する点が日本語圏の他事例と決定的に異なる（[[taroumegane]] / [[taichiendo]] / [[hayatetakeda]] はいずれも Obsidian 前提）
- **自律化のレベル設計**が群を抜いて詳細: 14 コマンド + 13 エージェント + 3 層アーキテクチャ + ガードレール YAML + 優先度スコアリング式

## なぜ Obsidian ではなく Git か

著者の比較表より：

| 観点 | Notion / Obsidian | Git リポジトリ |
|------|------------------|---------------|
| AI との連携 | プラグイン経由・制約多い | Claude Code がファイルを直接読み書き |
| バージョン管理 | Notion は上書き、Obsidian はプラグイン依存 | git log で全履歴 |
| 自動化 | API / Zapier（外部依存） | GitHub Actions + CLI で自由 |
| データ所有権 | SaaS依存 or ローカル | ローカル完全所有 |

**決定打は Claude Code との相性**。ファイルシステム直接操作のため API/プラグイン不要。

[[obsidian]] を前提とする [[karpathy-gist]] 系との最大の思想差: **閲覧 UI（Obsidian）を捨ててでもファイルシステム直接性を優先**。

## ディレクトリ構成

```
brain/
├── .claude/
│   ├── CLAUDE.md                # 思考パートナー設定
│   ├── agents/                  # 13 体のエージェント定義
│   └── commands/                # 14 個のカスタムコマンド
├── 00_Inbox/                    # 何でも放り込む
├── 10_Journal/                  # 日記・振り返り
├── 20_Projects/                 # ゴール付きプロジェクト
├── 30_Tech_Notes/               # 永続的な技術知識
├── 50_Business_Context/         # ビジネス・ドメイン知識
├── 90_System/                   # 運用ルール
└── 99_Archives/                 # 完了プロジェクト
```

**番号付きフォルダで「流れ」を作る設計思想**: `00_Inbox` → `10_Journal / 20_Projects` → `30_Tech_Notes / 50_Business_Context` → `99_Archives`。PARA method の変形。

## brain = Why/What、project = How

最重要の設計原則。**思考リポジトリと実装リポジトリを分離**する：

```
brain（思考）                project（実装）
├── なぜ・何をやるか          ├── ソースコード
├── 意思決定ログ              ├── テストコード
├── アイデアメモ              ├── 設定ファイル
├── GitHub Issues（全て）     └── 技術的 README
└── Milestones（全て）
```

project からは `Closes y-ryuki/brain#123` で参照。**Issues は brain に一元化**。初日に決めるべき鉄則として明記。

[[hayatetakeda]] の「Decisions/ 領域」と並行する概念だが、yamapiiii は**リポジトリ単位で物理的に分離**するのが特徴。

## 14 コマンド

| コマンド | 機能 | カテゴリ |
|--------|------|--------|
| `/decompose` | ゴール→MS→タスクに 3 層分解 → GitHub Issue 自動作成 | 計画 |
| `/work` | Issue を読んで実行→報告→クローズ | 実行 |
| `/orchestrate` | 優先度自動判定 → Issue チェーン実行 | 自律 |
| `/kickoff` | brain 内の関連知識を並列探索 | 探索 |
| `/deep-research` | Web + brain 内を並列深掘り調査 | 探索 |
| `/weekly-review` | 未処理・放置検出・活動サマリー | 振り返り |
| `/brain-status` | brain の健康ダッシュボード | 振り返り |
| `/stale-check` | 放置ノート・PJ 検出 | 振り返り |
| `/idea` | brain 内知識から着想生成（無限モード） | 発想 |
| `/new-project` | brain-project 構造で PJ 作成 | 管理 |
| `/sync` | brain と project の差分チェック | 管理 |
| `/voice-note` | 音声メモを Inbox 保存 | 入力 |
| `/meeting` | 会議書き起こし保存・要約 | 入力 |
| `/issue` | 自然言語から GitHub Issue 作成 | 管理 |

**設計 3 原則**:
1. 1 コマンド = 1 つの完結した行動（`plan-and-execute` ではなく `decompose` + `work` に分離）
2. 並列エージェントで速度を出す（`/deep-research` で 3 エージェント並列 → 26 検索 / 37 ページ取得実績）
3. 無限モードで思考を発散（`/idea --infinite` が 12 思考フレームワークをランダム適用）

[[taroumegane]] の 6 コマンド（ingest/query/lint 系）と比べて**大幅に粒度が細かい**。知識管理だけでなく PJ 管理まで統合しているのが要因。

## 13 エージェント

```
note-writer / knowledge-explorer / project-orchestrator / deep-researcher /
socrates / marketer / writer / pm-brief-generator / roadmap-analyzer /
blocker-detector / priority-scorer / idea-generator / synthesis-agent
```

**ペルソナエージェント**（socrates / marketer / writer）の設計が独特: 「AI に聞く」ではなく「**AI の視点で考えてもらう**」。Socrates は答えを出さず問答のみ。

## 3 層自律アーキテクチャ

```
Layer 3: Always-On      ← GitHub Actions / cron（構想段階）
  ↑ triggers
Layer 2: Judgment       ← project-orchestrator が優先度・ブロッカー判定
  ↑ executes
Layer 1: Execution      ← 14 コマンド × 13 エージェント
```

**設計思想**: 一気に全自動化しない。**層ごとに段階的委譲**。読み取り自律 → brain 内自律 → 全操作自律と徐々に権限を上げる。[[hayatetakeda]] の「領域ごとに自動化レベルを変える」と並行する設計知見。

## 優先度スコアリング（独自ロジック）

```
Score = (依存関係 × 3) + (ラベル優先度 × 2) + (タイプ適合度 × 2) + (工数 × 1) + (鮮度 × 1)
```

| 軸 | 重み | 詳細 |
|----|------|------|
| 依存関係 | ×3 | ブロッカーなし=10、他 Issue が待つ=加点 |
| ラベル優先度 | ×2 | high=10, medium=5, low=2 |
| タイプ適合度 | ×2 | design/research=9, code=3 |
| 工数 | ×1 | 小タスク優先（クイックウィン） |
| 鮮度 | ×1 | 最近の Issue=微加点 |

## ガードレール YAML（重要）

```yaml
guardrails:
  max_issues_per_chain: 3        # 1 チェーンで最大 3 Issue
  max_time_per_issue: 10min
  max_time_per_chain: 30min
  forbidden_actions:
    - ファイル削除
    - force push
    - projectリポジトリへの変更（明示的許可なし）
  error_handling:
    consecutive_errors: 3
  human_interaction:
    max_questions_per_chain: 1
```

**「最大 3 Issue」の根拠**: 少なすぎると自律の意味がない、多すぎると方向ズレたまま大量実行される。**3 が確認なしで安心できる最大数**だった、と経験則を明記。

## 本 Vault との関係

本 Vault は llm-wiki パターン（外部知識の compile）に特化しており、PJ 管理（yamapiiii の本領）は対象外。ただし以下は示唆を得られる：

- **brain / project 分離** = [[llm-wiki]] の「wiki（知識）」と「実装」を別リポジトリにする発想
- **段階的自律化** = [[llm-wiki]] の ingest 自動化ロードマップに応用可能
- **ペルソナエージェント** = query 時の多角的視点を得る手法として採用余地あり
- **ガードレール YAML** = LLM に完全委譲する前の安全装置として普遍性あり

## 関連ページ

- [[llm-wiki]] — 姉妹パターン（参照関係は片方向のみ）
- [[yamapiiii]] — 著者
- [[second-brain-git]] — 本記事が代表する実装パターン
- [[obsidian]] — 本記事は**意図的に不採用**（比較表の対象）
- [[taichiendo]] — Claude Code + Obsidian の対抗実装
- [[hayatetakeda]] — 段階的自律化の思想を共有
- [[rag]] — 対比概念
