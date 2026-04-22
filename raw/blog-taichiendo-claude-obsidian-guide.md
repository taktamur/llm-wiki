---
url: https://qiita.com/TaichiEndoh/items/eddc358e42d6c575fded
author: 遠藤 太一 (@TaichiEndoh)
fetched: 2026-04-22
published: 2026-04-11
lang: ja
source_type: qiita-article
title: Claude Code × Obsidian で作る「自己増殖型ナレッジベース」——LLM Wiki の実装ガイド
---

# Claude Code × Obsidian で作る「自己増殖型ナレッジベース」——LLM Wiki の実装ガイド

**著者**: @TaichiEndoh(遠藤 太一)
**公開日**: 2026-04-11
**タグ**: Obsidian / ナレッジマネジメント / n8n / LLM / ClaudeCode

---

## はじめに

NotebookLM を使っていると、ある限界に気づきます。

「資料を読み込ませて質問できる。要約もできる。便利なのは間違いない。でも **情報がそこで止まる**。一度出力したら終わり。知識が自己成長していかない。他のツールとの連携も難しい。」

この「閉じた箱」問題を解決するアーキテクチャが、OpenAI共同創業者 Andrej Karpathy 氏が提唱した「LLM Wiki」です。

本記事では、LLM Wiki の概念と、**Claude Code × Obsidian** を使った実装ワークフローを解説します。

---

## LLM Wiki とは

Wikipedia のように「ページ同士がリンクで繋がった知識グラフ」を、AIが自動生成・自動拡張し続ける仕組みです。

従来の RAG（Retrieval-Augmented Generation）が「検索して回答する」受動的なシステムであるのに対し、LLM Wiki は「知識構造そのものを継続的に構築・改善する」能動的なアーキテクチャです。

```
従来: ユーザーが質問 → RAGが検索 → LLMが回答（そこで終わり）

LLM Wiki: 情報を投入 → AIが構造化・リンク付け → 知識ベースが成長
               ↑_____________________________________↓（ループ）
```

---

## なぜ Obsidian × Claude Code なのか

この組み合わせが機能する技術的な理由は明確です。

### Obsidian の特性

- すべてのデータがローカルの `.md` ファイルとして保存される
- `[[WikiLink]]` によるノード間リンク構造を標準でサポート
- プレーンテキストのため、LLM との相性が抜群

### Claude Code の特性

- ローカルファイルシステムへの直接アクセス・読み書きが可能
- 自律的なタスク実行（ファイル作成・編集・フォルダ操作）
- 長いコンテキストウィンドウで、大量のノートを一度に処理できる

| 役割 | ツール | 担当 |
|------|--------|------|
| 知識の倉庫 | Obsidian | データ永続化・リンクグラフ管理 |
| 実行エンジン | Claude Code | 構造化・リンク付け・生成 |

**「思考は Obsidian、実行は Claude Code」** という役割分担がこのシステムの核心です。

---

## 3フェーズのワークフロー

### Phase 1: Ingest（情報の投入）

生のテキストデータを `inbox/` フォルダに投入します。完璧に整理する必要はありません。

```
vault/
├── inbox/           # 投入するだけ
│   ├── transcript_youtube.md
│   ├── meeting_notes.md
│   └── idea_fragments.md
├── wiki/            # Phase 2 で AI が自動生成
└── outputs/         # Phase 3 で AI が生成したコンテンツ
```

対象ファイルの例：

- YouTube動画の書き起こし
- 読んだ記事・論文のメモ
- 会議・打ち合わせメモ
- アイデアの断片

### Phase 2: Compile（自動整理とリンク付け）

Claude Code が `inbox/` を読み込み、以下を自動実行します。

1. コンテンツを理解し、Wikipedia 型の構造化ページを生成
2. 関連キーワード同士を `[[WikiLink]]` で自動接続
3. 生成したノートを `wiki/` 配下に配置

#### Claude Code への指示例

```
Read all files in /vault/inbox/,
create structured wiki pages in /vault/wiki/ with [[WikiLinks]],
then move processed files to /vault/inbox/processed/
```

#### 生成されるノートのイメージ

```markdown
# Claude Code

Claude Code は Anthropic が開発した AI エージェントツール。
[[Obsidian]] との連携により [[LLM Wiki]] の構築が可能。

## 関連
- [[Anthropic]]
- [[MCP（Model Context Protocol）]]
- [[AIエージェント]]
```

### Phase 3: Lint（品質チェックと新規コンテンツ生成）

プログラミングにおける linter（静的解析ツール）と同じ考え方で、知識ベース全体の品質を自動チェックします。

#### Claude Code への指示例（品質チェック）

```
Analyze all wiki pages and:
1. Find contradictions between pages
2. Identify orphan pages (no incoming links)
3. Find pages that should be linked but aren't
4. Flag potentially outdated information
Output a lint report to /vault/lint_report.md
```

#### チェック観点

- 情報の矛盾検出
- 孤立ページ（どこからもリンクされていないノード）の検出
- 関連しているのに未リンクなページの検出
- 情報の鮮度チェック

さらに、整理された知識ベースを元に **新規コンテンツを自動生成** できます。

```
Based on the wiki pages about [topic],
generate a YouTube script in my writing style.
Reference: /vault/style_guide.md
```

---

## NotebookLM との比較

| 観点 | NotebookLM | LLM Wiki（Claude Code × Obsidian） |
|------|-----------|----------------------------------|
| データの場所 | Google サーバー | ローカル（自分のPC） |
| 知識の成長 | なし（静的） | 自動増殖（動的） |
| 他ツール連携 | 難しい | ファイルベースで柔軟 |
| アウトプット | 質問への回答 | 新規コンテンツ・資料の自動生成 |
| 向いている用途 | 資料の質問応答・要約 | 知識ベース構築・育成・活用 |

NotebookLM はリサーチや資料理解に優れており、両者は競合しません。**用途によって使い分ける** のが正解です。

---

## ハルシネーション対策としての観点

このワークフローには、ハルシネーション（AIの事実誤認）を抑制する副次的な効果があります。

自分が投入した情報を **グラウンドトゥルース（根拠データ）** として Claude Code に渡すため、モデルは自身の学習データではなく、あなたの知識ベースを参照して動作します。RAG 的なアプローチで、事実的根拠のある出力を得やすくなります。

ただし完全ではありません。Claude Code が生成したコンテンツは必ずレビューしてください。

---

## 注意点

### データのプライバシー

Claude Code 使用時、ファイル内容は API 経由で Anthropic のサーバーに送信されます。機密情報・個人情報・患者データを含むファイルは知識ベースに含めないよう注意してください。

### 自律実行のリスク

Claude Code はファイルの作成・編集・削除を自律的に行います。重要なファイルは事前にバックアップを取り、`--dangerously-skip-permissions` フラグの使用は慎重に検討してください。

### 出力の検証

自動生成されたノートやコンテンツは叩き台として扱い、専門性の高い領域では必ず人間がレビューする工程を設けてください。

---

## まとめ

- **LLM Wiki** は Andrej Karpathy が提唱した「AIが自己成長する知識グラフ」の概念
- **Ingest → Compile → Lint** の3フェーズで、生のメモが整理・連結・コンテンツ化される
- **Obsidian**（`.md` ローカル保存 + WikiLink）と **Claude Code**（ファイル操作 + 自律実行）の相性が技術的な根拠
- NotebookLM の「閉じた箱」問題を解決する、継続的に育つ知識ベースが実現する
- ハルシネーション抑制・データプライバシー・自律実行リスクへの注意は必須

AIをその場しのぎで使うのではなく、**日々の思考と作業の流れにAIを組み込む設計** をする。その視点がこのワークフローの本質だと思います。

---

## 既存システムとの組み合わせ——「完全自律型」ナレッジエンジンへ

ここまで説明したワークフローは、単体でも十分に機能します。しかし、以下の既存システムと組み合わせることで、**手動操作をほぼゼロにした完全自律型の知識基盤** へと進化させることができます。

---

### Phase 1 を自動化：n8n × Obsidian Git との連携

関連記事：「【完全自動化】n8n×Obsidianで『寝ている間に最新ニュースが届く』最強の情報収集システムを作った話」では、n8n が毎朝 Google News RSS を自動取得し、GPT-4o で要約後、GitHub 経由で Obsidian の `inbox/` に自動配信するシステムを構築しています。

これは LLM Wiki の **Phase 1（Ingest）を完全自動化** するものです。

```
[Google News RSS]
      ↓ n8n（毎朝9時）
[GPT-4o で要約・Markdown化]
      ↓
[GitHub プライベートリポジトリ]
      ↓ Obsidian Git（自動 Pull）
[vault/inbox/ に自動配信]
      ↓ Claude Code（Phase 2 / 3）
[wiki/ に自動構造化・リンク付け]
```

毎朝 Obsidian を開くだけで、最新ニュースがすでに `inbox/` に入っており、Claude Code がそれを `wiki/` に整理するループが回ります。**完全に手を動かさずに知識ベースが育ち続ける** 状態が実現します。

#### 実装上のポイント

- n8n の GitHub ノードで `guid` をファイル名にすることで重複保存を防止
- `On Error: Continue` 設定で既存記事をスキップし、新着のみ投入される設計
- GitHub をバッファとして挟むことで、PCが起動していない間のデータも消えない

---

### ローカルLLMとの組み合わせ：Bonsai-8B でプライバシーを守る

関連記事：「【実装公開】医療現場のニーズを匿名で集める掲示板を作った——1-bit AI『Bonsai-8B』でアイデア発想もサポート」

Claude Code を使う場合、ファイル内容は Anthropic の API サーバーに送信されます。医療・研究・機密情報を扱う場合、これがボトルネックになります。

**Bonsai-8B**（1-bit 量子化、1.15GB、RTX A5000 で 151 tokens/秒）を Claude Code の代替として Compile フェーズに組み込むことで、**データを一切外部に出さないローカル完結型の LLM Wiki** を構築できます。

```
[vault/inbox/]
      ↓
[Bonsai-8B（ローカルGPU）がノード生成・リンク付け]
      ↓
[vault/wiki/]  ← データは自分のPCから出ない
```

#### 用途別の使い分け

| フェーズ | 推奨モデル | 理由 |
|----------|-----------|------|
| Phase 1（Ingest） | n8n + GPT-4o | 公開情報の要約、スピード優先 |
| Phase 2（Compile）— 公開情報 | Claude Code | 構造化品質が高い |
| Phase 2（Compile）— 機密情報 | Bonsai-8B（ローカル） | データを外部に出さない |
| Phase 3（Lint / 生成） | Claude Code | 長文コンテキスト・品質優先 |

Cloudflare Tunnel を使えば、Bonsai サーバーをローカルで起動しながら外部のサービスから安全に呼び出すことも可能です。

---

### エージェント進化：Gemma 4 ADK との統合

関連記事：「Gemma 4 徹底解説：Googleのオープンモデル最新版で何ができるのか」

Gemma 4 と同時公開された **Agent Development Kit（ADK）** の「Skill Factory」は、LLM Wiki の Lint フェーズを大幅に強化する可能性があります。

通常の Lint フェーズでは、Claude Code に毎回「矛盾を探して、孤立ページを探して……」と指示を書く必要があります。ADK の Progressive Disclosure（段階的開示）を使うと、このチェックロジック自体をスキルとして定義・再利用できます。

```
[ADK Skill: lint_wiki]
  L1（メタデータ）: "Obsidianナレッジベースの品質チェック"
  L2（指示）: 矛盾検出 / 孤立ページ探索 / リンク提案 / 鮮度チェック
  L3（リソース）: /vault/wiki/ 全ファイル
```

さらに ADK の **Skill Factory**（エージェントが新しいスキルを自己生成する機能）と組み合わせると、知識ベースの成長に合わせてチェックルール自体が自動で追加・更新される **自己進化型の Lint エンジン** を実現できます。

また、Gemma 4 の **256K コンテキストウィンドウ**（Apache 2.0 ライセンスでオンプレ運用可）により、大規模なナレッジベース全体を一度に読み込んだ横断的な分析も可能になります。

---

### 統合アーキテクチャ全体像

3つの既存システムを統合すると、以下のフルスタック構成になります。

```
【自動収集層】
  n8n（毎朝定時起動）
    └─ Google News RSS → GPT-4o 要約 → GitHub → vault/inbox/

【知識構築層】
  Phase 2: Claude Code（公開情報）or Bonsai-8B（機密情報）
    └─ inbox/ → wiki/（WikiLink グラフ自動生成）

【品質維持・生成層】
  Phase 3: Claude Code + Gemma 4 ADK Skill
    └─ wiki/ の矛盾・孤立・鮮度チェック → コンテンツ自動生成

【同期層】
  Obsidian Git（自動 Pull / Push）
    └─ GitHub バックアップ → マルチデバイス同期
```

#### 実現できること

- 毎日の情報収集〜整理〜品質維持が完全自動
- 公開情報はクラウドLLM、機密情報はローカルLLM と使い分けた安全設計
- Obsidian のグラフビューで成長する知識ネットワークを視覚化
- 整理済みの wiki をベースに、Qiita 記事・スライド・提案書を自動生成

---

## 参考

- Andrej Karpathy - LLM OS concept
- Obsidian 公式ドキュメント
- Claude Code 公式ドキュメント
- 【完全自動化】n8n×Obsidianで最強の情報収集システムを作った話
- 【実装公開】医療現場のニーズを匿名で集める掲示板を作った——Bonsai-8B でAI機能をサポート
- Gemma 4 徹底解説：Googleのオープンモデル最新版で何ができるのか
