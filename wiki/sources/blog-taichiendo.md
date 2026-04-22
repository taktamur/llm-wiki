---
type: source
updated: 2026-04-22
url: https://qiita.com/TaichiEndoh/items/eddc358e42d6c575fded
author: 遠藤 太一 (@TaichiEndoh)
fetched: 2026-04-22
published: 2026-04-11
lang: ja
sources: ["../../raw/blog-taichiendo-claude-obsidian-guide.md"]
aliases: [Claude Code x Obsidian 実装ガイド, Ingest Compile Lint, n8n Bonsai-8B Gemma4 統合]
---

# 遠藤太一: Claude Code × Obsidian で作る「自己増殖型ナレッジベース」 (2026-04)

Qiita 記事（2026-04-11）。[[andrej-karpathy]] の [[karpathy-gist]] を受け、[[taichiendo|遠藤]] が **3 フェーズワークフロー（Ingest → Compile → Lint）** として定式化。さらに n8n / Bonsai-8B / Gemma 4 ADK を組み合わせた **完全自律型統合アーキテクチャ** を構想提示した実装ガイド。

## このソースの位置付け

- 日本語圏で [[karpathy-gist]] の **ワークフローを命名・定式化**した記事（公開は 2026-04-11、[[taroumegane]] より 3 日早い）
- NotebookLM の「閉じた箱」問題（情報がそこで止まる）を批判の出発点に置く点で独特
- [[hayatetakeda]] が「設計原理の拡張」、[[taroumegane]] が「運用判断基準の明文化」なのに対し、本記事は **外部システムとの統合設計**で差別化

## 3 フェーズワークフロー（[[ingest-compile-lint]]）

```
Phase 1: Ingest  → 生のテキストを inbox/ に投入（整理不要）
Phase 2: Compile → Claude Code が wiki/ に自動生成・[[WikiLink]]付け
Phase 3: Lint    → 矛盾・孤立・鮮度・リンク抜けチェック
```

本 Vault の `CLAUDE.md` にある「Ingest / Query / Lint」と並列する別系統のワークフロー命名。Query（質問応答）よりも Compile（構造化）を中核に据える。

### ディレクトリ構成（シンプル 3 フォルダ）

```
vault/
├── inbox/           # 投入するだけ（Phase 1）
├── wiki/            # AI が自動生成（Phase 2）
└── outputs/         # AI が生成したコンテンツ（Phase 3）
```

[[karpathy-gist]] の原典と比べて、**Phase 3 で `outputs/` という「生成物置き場」を分離**している点が特徴。wiki/ は知識ベース、outputs/ は派生コンテンツ（YouTube 台本、記事、提案書等）という役割分離。

## Claude Code への指示プロンプト例

### Phase 2（Compile）

```
Read all files in /vault/inbox/,
create structured wiki pages in /vault/wiki/ with [[WikiLinks]],
then move processed files to /vault/inbox/processed/
```

### Phase 3（Lint）

```
Analyze all wiki pages and:
1. Find contradictions between pages
2. Identify orphan pages (no incoming links)
3. Find pages that should be linked but aren't
4. Flag potentially outdated information
Output a lint report to /vault/lint_report.md
```

**lint 観点の 4 分類**（矛盾 / 孤立 / 未リンク / 鮮度）は本 Vault の [[llm-wiki]] Lint 節と完全に一致。実質的に Karpathy gist の標準 lint プロンプトを日本語実装者が再定式化したもの。

## 統合アーキテクチャ（完全自律型）

```
【自動収集層】 n8n（毎朝9時） → Google News RSS → GPT-4o要約 → GitHub → vault/inbox/
【知識構築層】 Phase 2: Claude Code（公開情報）or Bonsai-8B（機密情報・ローカル）
【品質維持層】 Phase 3: Claude Code + Gemma 4 ADK Skill Factory
【同期層】    Obsidian Git（自動Pull/Push）→ GitHub バックアップ
```

### 用途別モデル使い分け（独自の分類）

| フェーズ | 推奨モデル | 理由 |
|---------|-----------|------|
| Phase 1（Ingest） | n8n + GPT-4o | 公開情報要約、スピード優先 |
| Phase 2 — 公開情報 | Claude Code | 構造化品質が高い |
| Phase 2 — 機密情報 | Bonsai-8B（ローカル、1.15GB、151 tokens/秒） | データを外部に出さない |
| Phase 3 | Claude Code + Gemma 4 ADK | 長文コンテキスト、256K ウィンドウ |

**設計ポイント**: プライバシー境界で LLM を切り替える。医療・研究などのローカル完結ニーズを [[blog-kenhuang]] とは別角度で提示（kenhuang は単一ローカル LLM 提案、遠藤はクラウド/ローカルのハイブリッド）。

## NotebookLM との対比（出発点）

| 観点 | NotebookLM | LLM Wiki |
|------|-----------|---------|
| データの場所 | Google サーバー | ローカル |
| 知識の成長 | なし（静的） | 自動増殖 |
| アウトプット | 質問への回答 | 新規コンテンツ自動生成 |
| 向いている用途 | 資料の質問応答 | 知識ベース構築・育成・活用 |

「閉じた箱」問題を提起する導入は [[blog-epsilla]] の「RAG killer」論調と違って **NotebookLM を競合ではなく補完** と位置づけている点が温和。

## 生成されるノート例（WikiLink 実例）

```markdown
# Claude Code

Claude Code は Anthropic が開発した AI エージェントツール。
[[Obsidian]] との連携により [[LLM Wiki]] の構築が可能。

## 関連
- [[Anthropic]]
- [[MCP（Model Context Protocol）]]
- [[AIエージェント]]
```

## 注意点（本記事が明示）

- **データプライバシー**: Claude Code 使用時は Anthropic の API サーバーに送信される。機密情報は含めない
- **自律実行リスク**: `--dangerously-skip-permissions` の使用は慎重に
- **出力の検証**: 生成物は叩き台として扱い、専門性の高い領域は人間レビュー必須

## 本 Vault との差分メモ

| 観点 | 本 Vault 現状 | 遠藤実装 |
|------|-------------|---------|
| ワークフロー命名 | Ingest / Query / Lint | Ingest / Compile / Lint |
| ディレクトリ | raw + wiki + Clippings + sources | inbox + wiki + outputs |
| 自動化範囲 | Ingest 手動 | Phase 1 も n8n で自動化 |
| LLM 選択 | Claude Code 専用 | クラウド/ローカルハイブリッド |
| outputs 層 | なし | あり（派生コンテンツ置き場） |

「Compile」という命名は wiki への統合作業の本質（情報の事前コンパイル）を直接表現していて興味深い。本 Vault の「Ingest」は取り込み〜統合までを含む広義の用法だが、遠藤実装は Ingest（投入のみ）と Compile（構造化）を分離している。

## 関連ページ

- [[llm-wiki]] — パターン本体
- [[karpathy-gist]] — 原典
- [[taichiendo]] — 著者
- [[ingest-compile-lint]] — 本記事が定式化したワークフロー
- [[obsidian]] — フロントエンド
- [[taroumegane]] — 日本語の別実装（運用判断基準方向）
- [[hayatetakeda]] — 日本語の別実装（設計原理拡張方向）
- [[blog-kenhuang]] — ローカル LLM 案（本記事とは別方向）
