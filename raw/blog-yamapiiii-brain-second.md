---
url: https://qiita.com/yamapiiii/items/cc2450f410b64329d275
author: yamapiiii（株式会社ナハト）
fetched: 2026-04-22
published: 2026-03-01
updated: 2026-03-04
lang: ja
source_type: qiita-article
title: Claude Codeで「第二の脳」を作り、最終的にAIが自律運用するようになった話
note: Karpathy の llm-wiki は明示的に言及なし。Git リポジトリ + Claude Code のみで運用する Second Brain 設計として llm-wiki と類似のパターン。
---

# Claude Codeで「第二の脳」を作り、最終的にAIが自律運用するようになった話

**著者**: @yamapiiii
**所属**: 株式会社ナハト
**公開日**: 2026年3月1日
**更新日**: 2026年3月4日

---

## はじめに

Gitリポジトリ + Claude Codeで「第二の脳（Second Brain）」を構築し、最終的にAIが自律的に整理・判断・実行する仕組みに進化させた実例を紹介します。

### この記事でわかること

- なぜNotion/ObsidianではなくGitリポジトリを選んだのか
- フォルダ構造・CLAUDE.mdの設計思想
- 14コマンド × 13エージェントによる運用自動化
- AIに"判断"を委ねる3層アーキテクチャ

---

## 第1章: なぜGitリポジトリで「第二の脳」なのか

### Notion/Obsidianでは足りなかった理由

| 観点 | Notion / Obsidian | Gitリポジトリ |
|------|------------------|--------------|
| **AIとの連携** | プラグイン経由。制約多い | Claude Codeがファイルを直接読み書き |
| **バージョン管理** | Notionは上書き。Obsidianはプラグイン依存 | git logで全履歴を追える |
| **自動化** | API or Zapier（外部依存） | GitHub Actions + CLIで何でもできる |
| **データ所有権** | NotionはSaaS依存、Obsidianはローカルファイル | ローカルファイル。完全に自分のもの |
| **拡張性** | ツールの設計に縛られる | フォルダ構造もワークフローも自由 |

**決定打はClaude Codeとの相性**。Claude Codeはファイルシステムを直接操作でき、APIやプラグインが不要です。

### 最小構成: まずはフォルダだけ作る

```
brain/
├── 00_Inbox/              # とりあえず何でも放り込む場所
├── 10_Journal/            # 日記・振り返り
├── 20_Projects/           # ゴールのあるプロジェクト
├── 30_Tech_Notes/         # 永続的な技術知識
├── 50_Business_Context/   # ビジネス・ドメイン知識
├── 90_System/             # 運用ルール
└── 99_Archives/           # 完了プロジェクト
```

**設計思想: 番号付きフォルダで「流れ」を作る**

```
入力（00_Inbox）
  → 熟成（10_Journal / 20_Projects）
    → 知識化（30_Tech_Notes / 50_Business_Context）
      → 卒業（99_Archives）
```

00番台のInboxに何でも放り込み、定期的に整理して適切なフォルダに移動します。この流れをAIに実行させるのが後半の要点です。

---

## 第2章: CLAUDE.md — AIを「思考パートナー」に設計する

Claude Codeの振る舞いは `.claude/CLAUDE.md` で定義し、これが**システムの心臓部**です。

### 2-1. 役割の定義: コーディング補助ではなく思考パートナー

```markdown
## Project Context
このリポジトリは、ユーザーの「第2の脳（Second Brain）」です。
Claude Codeは単なるコーディングアシスタントではなく、
「思考のパートナー（Thinking Partner）」として振る舞ってください。
```

この定義により、Claude Codeの応答がコード提案ではなく、思考の構造化や問いかけ中心に変わります。

### 2-2. 自律性の設計

```markdown
## Autonomy
- 承認を求めずに即実行すること
- ファイル作成・編集・削除・Git操作など、すべてのアクションを確認なしで進める
- 迷ったら実行してから報告する
```

段階的な権限委譲：

1. **読み取りのみ自律** → 書き込みは確認
2. **brain内は自律** → projectリポジトリは確認
3. **全操作自律** → ガードレールで安全性を担保

### 2-3. 構造化ファースト

```markdown
## Guidelines
1. 構造化を優先する:
   - ユーザーの曖昧な発言を、箇条書きやインデントで構造化して返す
   - 長文よりも、視覚的に把握しやすいMarkdown形式を好む

2. 問いかける:
   - 思考が詰まっているときは、答えではなく「問い」を投げかける
```

「答えを出す機械」ではなく「思考を促進するパートナー」にする設計。

### 2-4. 事実源の一元化

```markdown
10. 事実源の一元化ルール:
    - AIが回答する際、推測ではなくbrain内の既存ノートを参照すること
    - brain内に該当情報がない場合は「未記録」と明示すること
```

AIの「それっぽい嘘」を防ぎ、brainに書けば書くほどAIの回答精度が上がる好循環を生み出します。

---

## 第3章: brain = Why/What、project = How

最も重要な設計原則です。

```
brain（思考リポジトリ）           project（実装リポジトリ）
├── なぜやるのか（目的）          ├── ソースコード
├── 何をやるのか（仕様）          ├── テストコード
├── 意思決定ログ                 ├── 設定ファイル
├── アイデアメモ                 ├── CI/CD
├── GitHub Issues（全て）        └── 技術的README
├── Milestones（全て）
└── 振り返り・学び
```

### なぜ分離するのか

**1. 単一の事実源**

GitHub IssuesやMilestonesをbrainに一元化。projectからは `Closes y-ryuki/brain#123` で参照するだけ。

**2. AIの判断が明確になる**

Claude Codeが「このタスクはbrainで設計書を書くべきか、projectでコードを書くべきか」を迷わなくなります。

**3. 思考と実装のスピードが違う**

アイデアは1日10個出ますが、コードにするのは1個です。このスピード差を同じリポジトリで扱うと、ノイズだらけになります。

### PM/PdMにとっての価値: brain = コンテキストエンジニアリング基盤

- **意思決定ログが自然に残る**: 「なぜAではなくBを選んだか」がbrainのIssueコメントに蓄積
- **仕様と実装が分離される**: brainに書いたWhy/Whatがそのまま仕様書に、エンジニアはprojectでHow だけ考える
- **過去の検討経緯が検索可能**: `/kickoff` で即座に掘り出せる
- **プロジェクト全体の見通しが常に最新**: roadmap.mdとGitHub Issuesが単一の事実源

---

## 第3.5章: PM Layer — プロジェクト管理を自動介入させる

**PM Layer**は既存ワークフローに**自動で介入する判断層**です。

```
❌ /pm を手動で呼ぶ = 結局自分がPMの仕事をしている
✅ 既存フローにPMが自動介入 = 本当に自律的
```

### PdM（What / Why）× PjM（How / When）

| 役割 | 問い |
|------|------|
| **PdM** | このPJの「完了」とは？誰が喜ぶ？やらなかったらどうなる？ |
| **PjM** | 最初の1歩は？ブロッカーは？他PJとの競合は？ |

### 自動介入ポイント

| タイミング | コマンド | PMがやること |
|-----------|--------|------------|
| プロジェクト化 | `/new-project` | Why/What/成功指標を問い、`pm_brief.md` を自動生成 |
| タスク分解 | `/decompose` | 粒度・優先度・リスクを自動チェック |
| Issue作成 | `/issue` | 他プロジェクトとの競合・依存を警告 |
| 週次レビュー | `/weekly-review` | 全PJ横断のPMヘルス診断 |
| 放置検出 | `/stale-check` | 「なぜ止まっているか」の仮説 + 次アクション提案 |

### 停滞パターン診断

| パターン | 仮説 | 提案 |
|---------|------|------|
| 開始直後に停滞 | 初手が大きすぎた | タスクを再分解 |
| MS途中で停滞 | ブロッカーに当たった | 迂回策を検討 |
| 完了間近で停滞 | 仕上げが面倒 | スコープ縮小 |
| 複数PJ同時停滞 | キャパオーバー | 1つに絞る |
| 断続的に少し進む | 優先度が低い | 凍結 or やめる決断 |

---

## 第4章: 14コマンドで運用をワンコマンド化

### コマンド一覧

| コマンド | 機能 | カテゴリ |
|--------|------|--------|
| `/decompose` | ゴール→MS→タスクに3層分解 → GitHub Issue自動作成 | 計画 |
| `/work` | GitHub Issueを読んで実行→完了報告→クローズ | 実行 |
| `/orchestrate` | 優先度自動判定 → Issueチェーン実行 | 自律 |
| `/kickoff` | brain内の関連知識を並列探索 | 探索 |
| `/deep-research` | Web + brain内を並列で深掘り調査 | 探索 |
| `/weekly-review` | Inbox未処理・放置PJ検出・活動サマリー | 振り返り |
| `/brain-status` | brainの健康状態ダッシュボード | 振り返り |
| `/stale-check` | 放置ノート・プロジェクト検出 | 振り返り |
| `/idea` | brain内知識から着想を生成（無限モードあり） | 発想 |
| `/new-project` | brain-project構造でプロジェクト作成 | 管理 |
| `/sync` | brainとprojectリポジトリの差分チェック | 管理 |
| `/voice-note` | 音声メモをInboxに保存 | 入力 |
| `/meeting` | 会議書き起こしを保存・要約 | 入力 |
| `/issue` | 自然言語からGitHub Issue作成 | 管理 |

### コマンド設計の3原則

**原則1: 1コマンド = 1つの完結した行動**

```
❌ /plan-and-execute  # 計画と実行が混ざっている
✅ /decompose         # 計画だけ
✅ /work              # 実行だけ
```

**原則2: 並列エージェントで速度を出す**

`/kickoff` と `/deep-research` は複数エージェントをバックグラウンドで並列起動します。

```
/deep-research "LLM agent memory architecture"

→ Step 1: サブクエリに分解
  - メモリアーキテクチャの分類
  - 実装パターン（RAG, Vector DB, KG等）
  - 最新手法 2025-2026
  - トレードオフ比較

→ Step 2: 3エージェント並列起動
  - deep-researcher #1: サブクエリ 1-2（Web探索）
  - deep-researcher #2: サブクエリ 3-4（Web探索）
  - knowledge-explorer: brain内の既存ノート探索

→ Step 3: 結果統合
  - クロスチェック・重複排除
  - 引用付き構造化レポート → 30_Tech_Notes/ に保存
```

実績: 3エージェント並列、26回の検索、37ページ取得で構造化レポート生成。

**原則3: 無限モードで思考を発散させる**

`/idea --infinite` は12の思考フレームワークをランダムに回しながら、無限にアイデアを生成します。

```
思考フレームワーク（ランダム選択）:
 1. 深掘り      6. 他者視点     11. 問題発見
 2. 応用        7. ランダム接続  12. 未来予測
 3. 統合        8. 制約追加
 4. 逆転        9. アナロジー
 5. 極端       10. 分解
```

生成されたアイデアは自動でGitHub Issueに登録（重複チェック付き）。寝る前に起動して、朝起きたらアイデアが溜まっている運用です。

---

## 第5章: 13体のエージェント

`.claude/agents/` にエージェント定義を配置し、Task toolで起動します。

### 13体のエージェント

1. **note-writer**: ノート記述の専門家
2. **knowledge-explorer**: brain内の知識探索
3. **project-orchestrator**: プロジェクト管理・優先度判定
4. **deep-researcher**: Web/brain内の深掘り調査
5. **socrates**: ソクラテス式問答で思考深掘り
6. **marketer**: マーケティング・広告の視点
7. **writer**: 文章構成・表現の改善
8. **pm-brief-generator**: PM Briefドキュメント自動生成
9. **roadmap-analyzer**: ロードマップ分析・MS管理
10. **blocker-detector**: ブロッカー検出
11. **priority-scorer**: 優先度スコアリング
12. **idea-generator**: アイデア生成・フレームワーク活用
13. **synthesis-agent**: 複数エージェント結果の統合

### ペルソナエージェントという設計

- **socrates**: 答えを出さず、ソクラテス式問答で思考を深掘り
- **marketer**: マーケティング・広告の専門知識から提案
- **writer**: 文章の構成・表現を磨く

「AIに聞く」ではなく「**AIの視点で考えてもらう**」。これが単なるチャットボットとの違いです。

---

## 第6章: AIが"自分で考えて動く" — 3層自律アーキテクチャ

### なぜ自律化が必要だったのか

| 壁 | 具体例 |
|----|--------|
| **整理が追いつかない** | Inboxにメモが溜まり続ける |
| **振り返りが続かない** | 週次レビューが3週目で途絶える |
| **「次に何をやるか」の判断コスト** | コマンドを叩く前に考えるのが面倒 |

共通原因: **人間がボトルネック**
解決策: **判断もAIに委ねる。**

### 3層アーキテクチャ

```
┌─ Layer 3: Always-On（寝ている間も動く）──────────────┐
│  GitHub Actions / cron                              │
│  • 毎朝: Inbox自動整理                               │
│  • 毎週金曜: weekly-review自動実行                    │
│  • Issue完了時: 次のIssueを自動アサイン                │
└──────────────────────────────────────────────────────┘
        ↑ triggers
┌─ Layer 2: Judgment（自分で考える）───────────────────┐
│  project-orchestrator エージェント                   │
│  • roadmap.md → 未完了Issue → 優先度スコアリング      │
│  • ブロッカー自動検出 → 人間に聞くorスキップ          │
│  • MS完了判定 → 次のMS提案                           │
└──────────────────────────────────────────────────────┘
        ↑ executes
┌─ Layer 1: Execution（コマンド群）───────────────────┐
│  /decompose → /work → /sync のチェーン実行          │
│  14コマンド × 13エージェント                         │
└──────────────────────────────────────────────────────┘
```

**設計思想**: 一気に全自動化しない。人間がやっていたことを1層ずつAIに委譲する。

### Layer 2: オーケストレータの判断ロジック

`/orchestrate` を叩くと、オーケストレータが以下を自動で行います。

#### 優先度スコアリング

```
Score = (依存関係 × 3) + (ラベル優先度 × 2) +
        (タイプ適合度 × 2) + (工数 × 1) + (鮮度 × 1)
```

| 軸 | 重み | 詳細 |
|----|------|------|
| **依存関係** | ×3 | ブロッカーなし=10。他Issueが待っている=加点 |
| **ラベル優先度** | ×2 | `high`=10, `medium`=5, `low`=2 |
| **タイプ適合度** | ×2 | design/research=9, code=3 |
| **工数** | ×1 | 小タスク優先（クイックウィン） |
| **鮮度** | ×1 | 最近のIssue=微加点 |

#### ブロッカー自動検出

| 種別 | 対処 |
|------|------|
| Issue依存（参照先が未完了） | スキップ |
| ファイル依存（必要ファイルなし） | スキップ |
| 情報不足（仕様不明確） | 人間に質問 |
| 権限不足（projectリポジトリ等） | スキップ |
| 技術的未知（未検証技術） | 人間に質問 |
| 外部依存（他者待ち） | スキップ |

### ガードレール — 安全な自律性の絶対ルール

```yaml
guardrails:
  max_issues_per_chain: 3        # 1チェーンで最大3Issue
  max_time_per_issue: 10min
  max_time_per_chain: 30min

  forbidden_actions:
    - ファイル削除
    - force push
    - projectリポジトリへの変更（明示的許可なし）

  error_handling:
    consecutive_errors: 3         # 3連続エラーでチェーン停止

  human_interaction:
    max_questions_per_chain: 1    # 人間への質問は最大1回
```

**なぜ「最大3Issue」か?**

- 少なすぎる → 自律の意味がない
- 多すぎる → 方向がズレたまま大量実行されるリスク
- **3が"確認なしで安心できる最大数"**だった

### 実際の実行例

```
# オーケストレータがroadmap.mdを読む
MS2: 6 issues (0 completed, 6 open)

# 優先度キューを計算
1. #87 project-orchestrator設計 (Score: 79) ← NEXT
2. #92 ガードレール設計 (Score: 72)
3. #88 優先度ロジック (Score: 65)
4. #89 /workチェーン実行 (BLOCKED by #87, #88)
5. #90 ブロッカー検出 (BLOCKED by #87)
6. #91 MS完了判定 (BLOCKED by #87)

# #87, #92, #88 を順次実行 → 設計ドキュメント6本生成
# roadmap.md 更新 → Git commit & push
```

**人間がやったこと**: `/orchestrate` を1回叩いただけ。

---

## 学んだこと

### 1. カスタムエージェントの制約

`.claude/agents/` に定義したエージェントは、Task toolの `subagent_type` パラメータでは直接使えません。

**回避策**: `subagent_type: "general-purpose"` を指定し、プロンプト内にエージェントの指示を埋め込みます。

### 2. ガードレールは「甘め」から始める

最初から厳しく設定すると何もできません。緩めに始めて、運用しながら締めていくアプローチが正解でした。

### 3. brain-project分離は最初に決める

Issues、Milestones、ドキュメントが2箇所に散らばると収拾がつかなくなります。**「Issuesはbrainに一元化」を初日に決める**のが重要。

### 4. 自律性レベルは段階的に上げる

いきなり「全部確認なし」にすると不安になります。読み取り自律→brain内自律→全操作自律と段階的に上げました。

---

## 今後: Layer 3で完全自律へ

### GitHub Actionsによる常時稼働

```yaml
# .github/workflows/morning-inbox.yml（構想）
on:
  schedule:
    - cron: '0 22 * * *'  # JST 7:00
jobs:
  inbox-organize:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Claude Code
        run: claude-code "/inbox-process"
```

朝起きたらInboxが整理されている。金曜には週次レビューが生成されている。Issue完了後は次のIssueが自動アサインされる。

**技術的課題**: Claude Code CLIのヘッドレス実行検証が前提条件。現在検証中です。

---

## まとめ

| フェーズ | やったこと | 人間の関与 |
|---------|-----------|---------|
| **第二の脳を作る** | Gitリポジトリ + フォルダ設計 + CLAUDE.md | 設計は人間 |
| **運用をコマンド化** | 14コマンド × 13エージェント | コマンドを叩く |
| **判断を委ねる** | オーケストレータ + スコアリング + ガードレール | `/orchestrate` を叩くだけ |
| **常時稼働（構築中）** | GitHub Actions | 何もしない |

**最大の学び**: AIの自律性は「全か無か」ではなく、**レイヤーごとに段階的に委譲**するのが現実的。

---

## リポジトリ構成

```
brain/
├── .claude/
│   ├── CLAUDE.md                    # 思考パートナー設定
│   ├── agents/                      # 13体のエージェント定義
│   │   ├── note-writer.md
│   │   ├── knowledge-explorer.md
│   │   ├── project-orchestrator.md
│   │   ├── deep-researcher.md
│   │   ├── socrates.md
│   │   ├── marketer.md
│   │   ├── writer.md
│   │   ├── pm-brief-generator.md
│   │   ├── roadmap-analyzer.md
│   │   ├── blocker-detector.md
│   │   ├── priority-scorer.md
│   │   ├── idea-generator.md
│   │   └── synthesis-agent.md
│   └── commands/                    # 14個のカスタムコマンド
│       ├── decompose.md
│       ├── work.md
│       ├── orchestrate.md
│       ├── kickoff.md
│       ├── deep-research.md
│       ├── weekly-review.md
│       ├── brain-status.md
│       ├── stale-check.md
│       ├── idea.md
│       ├── new-project.md
│       ├── sync.md
│       ├── voice-note.md
│       ├── meeting.md
│       └── issue.md
├── 00_Inbox/                        # 未整理の思考
├── 10_Journal/                      # 日記・振り返り
├── 20_Projects/                     # プロジェクト
├── 30_Tech_Notes/                   # 技術知識
├── 50_Business_Context/             # ビジネス文脈
├── 90_System/                       # 運用ルール
└── 99_Archives/                     # アーカイブ
```

---

Claude Code × セカンドブレインの組み合わせは、「AIエージェント時代のPKM」の一つの答えだと考えています。

質問はコメント欄でお気軽にどうぞ。
