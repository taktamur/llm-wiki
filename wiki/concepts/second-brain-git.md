---
type: concept
updated: 2026-04-22
sources: [../sources/blog-yamapiiii.md]
aliases: [第二の脳 Git, Git Second Brain, Obsidian 非使用, brain=Why/What project=How]
---

# second-brain-git（Git リポジトリ第二の脳）

[[yamapiiii]]（株式会社ナハト）が [[blog-yamapiiii]]（2026-03-01）で提示した **Obsidian を意図的に不採用**し、Git リポジトリ + Claude Code のみで構築する Second Brain パターン。[[llm-wiki]] の**姉妹パターン**（Karpathy 非参照だが構造的には類似）。

## [[llm-wiki]] との差分

| 観点 | llm-wiki（Obsidian 系） | second-brain-git |
|------|----------------------|-----------------|
| 閲覧 UI | Obsidian 前提 | UI なし（エディタ or git log で閲覧） |
| バージョン管理 | Obsidian Git プラグイン | git 直接 |
| 自動化 | ユーザー起動 | GitHub Actions 構想 |
| リポジトリ数 | 1 つ（wiki） | **2 つ分離**（brain + project） |
| PJ 管理統合 | なし | **GitHub Issues 一元化** |

## 基本設計原則

### 1. brain = Why/What、project = How

**思考と実装を別リポジトリに分離**：

```
brain（思考リポジトリ）             project（実装リポジトリ）
├── なぜ・何をやるか                 ├── ソースコード
├── 意思決定ログ                    ├── テストコード
├── アイデアメモ                    ├── 設定ファイル
├── GitHub Issues（全て）           └── README
└── Milestones（全て）
```

- **Issues は brain に一元化**: project からは `Closes y-ryuki/brain#123` で参照
- AI の判断が明確になる: Claude Code が「brain で設計を書くか、project でコードを書くか」を迷わない
- 思考と実装のスピード差（アイデア 10 個/日 vs コード 1 個/日）を分離

### 2. 番号付きフォルダで「流れ」を作る

```
brain/
├── 00_Inbox/              # 何でも放り込む
├── 10_Journal/            # 日記・振り返り
├── 20_Projects/           # ゴール付きプロジェクト
├── 30_Tech_Notes/         # 永続的な技術知識
├── 50_Business_Context/   # ビジネス・ドメイン知識
├── 90_System/             # 運用ルール
└── 99_Archives/           # 完了プロジェクト
```

**流れ**: `00_Inbox` → `10_Journal / 20_Projects` → `30_Tech_Notes / 50_Business_Context` → `99_Archives`

PARA method (Projects/Areas/Resources/Archives) の変形。[[llm-wiki]] の `raw/` + `wiki/` とは異なる**時間軸中心の分類**。

## Obsidian 不採用の根拠

著者は以下で Git 選択を正当化：

| 観点 | Obsidian | Git |
|------|----------|-----|
| AI 連携 | プラグイン経由・制約多い | ファイル直接読み書き |
| バージョン管理 | プラグイン依存 | git log |
| 自動化 | API / Zapier | GitHub Actions + CLI |
| データ所有権 | ローカルファイル（同じ） | ローカルファイル（同じ） |

**決定打は Claude Code との相性**: ファイルシステム直接性を優先。[[obsidian]] の閲覧 UI（グラフビュー等）を捨てる代わりに、CI/CD 連携と Issues 一元化を得る。

## 自律化の 3 層アーキテクチャ

```
Layer 3: Always-On    ← GitHub Actions / cron（構想）
Layer 2: Judgment     ← project-orchestrator が優先度・ブロッカー判定
Layer 1: Execution    ← 14 コマンド × 13 エージェント
```

**段階的委譲**: 読み取り自律 → brain 内自律 → 全操作自律。ガードレール YAML で安全装置：

```yaml
guardrails:
  max_issues_per_chain: 3
  max_time_per_issue: 10min
  forbidden_actions:
    - ファイル削除
    - force push
```

## 14 コマンド + 13 エージェント

本パターンは Claude Code の拡張を大規模に使う：

- **14 コマンド**: `/decompose` `/work` `/orchestrate` `/kickoff` `/deep-research` `/weekly-review` `/brain-status` `/stale-check` `/idea` `/new-project` `/sync` `/voice-note` `/meeting` `/issue`
- **13 エージェント**: note-writer / knowledge-explorer / project-orchestrator / deep-researcher / socrates / marketer / writer / pm-brief-generator / roadmap-analyzer / blocker-detector / priority-scorer / idea-generator / synthesis-agent

[[taroumegane]] の 6 コマンドと比べて**粒度が大幅に細かい**。PJ 管理まで統合しているのが要因。

## 優先度スコアリング（独自式）

```
Score = (依存関係 × 3) + (ラベル優先度 × 2) +
        (タイプ適合度 × 2) + (工数 × 1) + (鮮度 × 1)
```

`/orchestrate` がこの式で未完了 Issue をランク付けし、上位から自動実行。ガードレールの「max_issues_per_chain: 3」で暴走を防ぐ。

## 本 Vault での位置づけ

- **llm-wiki パターンではない**（Karpathy 非参照）が、構造的には**姉妹パターン**
- 本 Vault は [[obsidian]] 採用方針のため、second-brain-git の設計哲学は採用しない
- ただし以下は援用価値あり：
  - **段階的自律化**: ingest の自動化ロードマップ検討時の参照
  - **ガードレール YAML**: LLM 委譲時の安全装置として普遍性あり
  - **ペルソナエージェント**: query 時の多角的視点獲得手法

## 関連ページ

- [[llm-wiki]] — 姉妹パターン（本パターンは非参照）
- [[blog-yamapiiii]] — 原典
- [[yamapiiii]] — 提唱者
- [[obsidian]] — **意図的に不採用**
- [[rag]] — 対比概念
- [[self-os]] — 段階的自律化の思想を共有（別実装）
