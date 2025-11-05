# Claude Code: エージェント・スキル・MCP完全ガイド

> **公式ドキュメント参照**: 2025-11-05調査
> **更新日**: 2025-11-05

## 📋 目次

1. [概要](#概要)
2. [エージェント（Subagents）](#エージェントsubagents)
3. [スキル（Skills）](#スキルskills)
4. [MCP（Model Context Protocol）](#mcpmodel-context-protocol)
5. [プラグイン（Plugins）](#プラグインplugins)
6. [使い分けガイド](#使い分けガイド)
7. [実践例](#実践例)
8. [まとめ](#まとめ)

---

## 概要

Claude Codeには、機能を拡張するための4つの主要な仕組みがあります：

| 仕組み | 目的 | 起動方法 | 主な用途 |
|--------|------|----------|----------|
| **エージェント** | タスクの委譲 | 自動/明示的 | 専門的な調査・分析 |
| **スキル** | 機能拡張 | 自動（Claudeが判断） | ワークフローの自動化 |
| **MCP** | 外部統合 | 設定後、@や/で利用 | API・DB・ツール連携 |
| **プラグイン** | パッケージ管理 | マーケットプレイスから | チーム共有 |

---

## エージェント（Subagents）

### 📚 エージェントとは

**定義**: タスクを委譲できる専門的なAIアシスタント

**特徴**:
- 独立したコンテキストウィンドウで動作（メイン会話を汚染しない）
- ドメイン特化のシステムプロンプト
- 選択的なツールアクセス権限
- プロジェクト間で再利用可能

### 🎯 エージェントの主な利点

1. **コンテキスト保護**: メイン会話のコンテキストを節約
2. **専門性**: 特定領域に特化した知識
3. **再利用性**: ワークフローを標準化
4. **セキュリティ**: ツールアクセスを細かく制御

### 📁 エージェントの保存場所

優先度の高い順：

1. **プロジェクトレベル**: `.claude/agents/` （最優先、プロジェクト固有）
2. **ユーザーレベル**: `~/.claude/agents/` （全プロジェクトで利用可能）
3. **プラグインエージェント**: プラグイン経由で配布
4. **CLI定義**: `--agents` フラグで動的作成（セッション限定）

**重要**: 名前が衝突した場合、プロジェクトレベルが優先されます。

### ⚙️ エージェント設定ファイル

**ファイル形式**: YAMLフロントマター付きMarkdown

```yaml
---
name: code-reviewer              # 一意の識別子（小文字、ハイフン区切り）
description: コード品質、セキュリティ、保守性をレビュー
tools: Read,Grep,Bash            # オプション: 利用可能ツール（省略時は全て継承）
model: sonnet                    # オプション: sonnet, opus, haiku, inherit
---

# システムプロンプト

あなたは経験豊富なコードレビュアーです。以下の観点で分析してください：

1. セキュリティ脆弱性（SQLインジェクション、XSSなど）
2. パフォーマンス問題
3. コードの可読性
4. ベストプラクティス違反

## 出力形式

- 🔴 重大な問題
- 🟡 改善推奨
- 🟢 良い点

具体的なコード例と修正案を提示してください。
```

### 🔧 組み込みエージェント

Claude Codeには以下のエージェントが標準で含まれています：

1. **Plan**: プランモード時のコードベース調査（Sonnetモデル使用）
2. **Code Reviewer**: コード品質、セキュリティ、保守性のレビュー
3. **Debugger**: エラー診断と根本原因分析
4. **Data Scientist**: SQLクエリとBigQuery操作

### 🚀 エージェントの使い方

#### 自動起動

Claudeが自動的にタスクに適したエージェントを選択：

```bash
claude "最近のPRをレビューして"
# → Code Reviewerエージェントが自動起動
```

#### 明示的起動

特定のエージェントを明示的に指定：

```bash
claude "code-reviewerエージェントを使って変更をチェックして"
```

#### エージェントの連鎖

複数のエージェントを順次使用：

```bash
claude "debuggerエージェントでエラーを特定してから、code-reviewerで修正案をレビューして"
```

### 🔄 再開可能なエージェント

エージェントは一意のIDで以前の会話を継続できます：

**メリット**:
- セッション間で長期的な調査を継続
- コンテキストを維持しながら反復的な改善
- 複数ステップのワークフローを中断・再開可能

**実装**: エージェントのトランスクリプトは `.jsonl` ファイルとして保存され、完全な履歴コンテキストを持って再開できます。

### 📊 エージェント管理

```bash
# エージェント管理インターフェイス
/agents

# 実行できる操作：
# - エージェント一覧表示
# - 新規作成
# - 編集
# - 削除
# - ツール権限管理
```

### ✨ ベストプラクティス

1. **生成からカスタマイズ**: まずClaudeにエージェントを生成させ、その後調整
2. **単一責任の原則**: 1つのエージェントは1つの責任に集中
3. **詳細なプロンプト**: 具体的な例を含む明確なシステムプロンプト
4. **最小権限の原則**: 必要なツールのみアクセスを許可
5. **バージョン管理**: プロジェクトエージェントはgitにコミット
6. **説明文の最適化**: `description` フィールドを使って自動起動を促進

### ⚡ パフォーマンスの考慮事項

- **利点**: メイン会話のコンテキストを保護し、長いセッションが可能
- **欠点**: エージェントが独立して情報収集するため、レイテンシが増加

---

## スキル（Skills）

### 📚 スキルとは

**定義**: Claudeの機能を拡張するモジュラーで発見可能なパッケージ

**特徴**:
- `SKILL.md` ファイルに指示を記述
- Claudeが「関連する時」に自動的に読み込む
- サポートファイル（スクリプト、テンプレート）は必要時にのみロード
- **モデル起動**: Claudeが自律的に使用を判断（スラッシュコマンドは明示的起動）

### 🎯 スキルの種類

| 種類 | 保存場所 | スコープ | 用途 |
|------|---------|---------|------|
| **個人スキル** | `~/.claude/skills/` | 全プロジェクト | 個人ワークフロー |
| **プロジェクトスキル** | `.claude/skills/` | チーム共有 | チーム標準化 |
| **プラグインスキル** | プラグイン内 | プラグイン利用者 | 配布・共有 |

### 📝 スキル作成

#### 基本構造

全てのスキルには `SKILL.md` ファイルが必要：

```markdown
---
name: code-review-checklist
description: コードレビュー時にセキュリティとパフォーマンスのチェックリストを適用
allowed-tools: Read,Grep
---

# コードレビューチェックリストスキル

このスキルは、コードレビュー時に以下の項目を自動的にチェックします：

## セキュリティチェック

1. **SQLインジェクション**: パラメータ化されたクエリを使用しているか
2. **XSS**: ユーザー入力のサニタイズ
3. **認証**: 適切な認証・認可チェック
4. **シークレット**: ハードコードされたAPIキーやパスワードがないか

## パフォーマンスチェック

1. **N+1クエリ**: データベースアクセスパターン
2. **メモリリーク**: リソース解放の確認
3. **無限ループ**: ループの終了条件

## 出力形式

各チェック項目について：
- ✅ 問題なし
- ⚠️ 要注意
- ❌ 問題あり

具体的なファイル名と行番号を示してください。
```

#### YAMLフロントマター

```yaml
---
name: スキル名（小文字、ハイフン区切り、最大64文字）
description: 何をするか、いつ使うか（最大1024文字）
allowed-tools: Read,Grep,Edit  # オプション: 利用可能ツール
---
```

#### サポートファイル

スキルには追加ファイルを含めることができます：

```
.claude/skills/api-docs/
├── SKILL.md              # メインスキルファイル
├── examples/
│   ├── rest_api.md       # REST APIの例
│   └── graphql.md        # GraphQLの例
└── templates/
    └── endpoint.md       # エンドポイント定義テンプレート
```

**重要**: サポートファイルは必要時にのみロード（プログレッシブディスクロージャー）

### ⚙️ ツール制限

`allowed-tools` フィールドでツールアクセスを制限：

**用途**:
- 読み取り専用操作（Read, Grep のみ）
- セキュリティが重要なワークフロー
- 限定的な権限での実行

**省略時**: 標準の権限プロトコルに従う

### 🔄 共有と配布

#### 推奨方法: プラグイン経由

チーム配布には Claude Code プラグインを使用：

```bash
# プラグインマーケットプレイスからインストール
/plugin install team-skills@company
```

#### 直接共有: Git経由

プロジェクトスキルをリポジトリにコミット：

```bash
# .claude/skills/ をgitにコミット
git add .claude/skills/
git commit -m "Add code review skill"
git push

# チームメンバーがpull後、自動的にスキルが利用可能
```

### 🔍 発見とデバッグ

#### スキルが起動しない場合

**一般的な問題**:

1. **曖昧な説明**: 具体的なトリガー用語が不足
   ```yaml
   # ❌ 悪い例
   description: コードを改善する

   # ✅ 良い例
   description: TypeScriptのコードレビュー時にセキュリティ脆弱性とパフォーマンス問題をチェック
   ```

2. **無効なYAML構文**: フロントマターのエラー
   ```yaml
   # ❌ エラー（コロンの後にスペースがない）
   name:code-review

   # ✅ 正しい
   name: code-review
   ```

3. **間違ったファイルパス**: 正しいディレクトリにない
   ```bash
   # ✅ 正しい場所
   ~/.claude/skills/my-skill/SKILL.md
   .claude/skills/team-skill/SKILL.md

   # ❌ 間違った場所
   ~/my-skill/SKILL.md
   ```

#### デバッグ方法

説明に一致する質問をして、スキルが起動するかテスト：

```bash
claude "TypeScriptのセキュリティ問題をチェックして"
# → code-review-checklist スキルが起動するはず
```

### ✨ ベストプラクティス

1. **単一機能**: 広範な機能ではなく、単一の機能に集中
2. **具体的な説明**: 動作内容とアクティベーションのコンテキストを両方記述
3. **バージョン管理**: スキルのバージョンをドキュメント化
4. **直接更新**: 機能を変更する際は `SKILL.md` を直接編集

---

## MCP（Model Context Protocol）

### 📚 MCPとは

**定義**: AI-ツール統合のためのオープンソース標準

**目的**: Claude Codeを外部ツール、データベース、APIに接続し、数百のサービスやカスタムアプリケーションに機能を拡張

### 🔌 MCPサーバーのタイプ

| タイプ | 用途 | 推奨度 | 例 |
|--------|------|--------|-----|
| **HTTP** | クラウドベースサービス | ⭐⭐⭐ | Notion, GitHub |
| **SSE** | Server-Sent Events | ⚠️ 非推奨 | Asana（廃止予定） |
| **Stdio** | ローカルプロセス | ⭐⭐ | Airtable, PostgreSQL |

### 📦 インストール方法

#### 1. HTTP サーバー（推奨）

```bash
# クラウドベースサービス
claude mcp add --transport http notion https://mcp.notion.com/mcp
claude mcp add --transport http github https://mcp.github.com/mcp
```

#### 2. SSE サーバー（非推奨）

```bash
# Server-Sent Events（段階的に廃止中）
claude mcp add --transport sse asana https://mcp.asana.com/sse
```

#### 3. Stdio サーバー

```bash
# ローカルプロセス実行
claude mcp add --transport stdio airtable \
  --env AIRTABLE_API_KEY=YOUR_KEY \
  -- npx -y airtable-mcp-server

# PostgreSQLの例
claude mcp add --transport stdio postgres \
  --env DATABASE_URL=postgresql://user:pass@localhost/db \
  -- npx -y @modelcontextprotocol/server-postgres
```

### 🔐 スコープと設定

#### 3つのスコープレベル

1. **ローカル**: 現在のプロジェクトのみ（デフォルト）
2. **プロジェクト**: `.mcp.json` でチーム共有（バージョン管理）
3. **ユーザー**: マシン上の全プロジェクトで利用可能

#### スコープ指定

```bash
# プロジェクトスコープ（チーム共有）
claude mcp add --scope project --transport http notion https://mcp.notion.com/mcp

# ユーザースコープ（個人利用）
claude mcp add --scope user --transport stdio postgres --env DATABASE_URL=... -- npx -y @modelcontextprotocol/server-postgres
```

### 🔒 認証

#### OAuth 2.0 認証フロー

多くのクラウドベースMCPサーバーはOAuth 2.0が必要：

```bash
# 1. サーバーを追加
claude mcp add --transport http github https://mcp.github.com/mcp

# 2. Claude Code内で認証
/mcp
# → ブラウザでログインプロセス

# 3. トークンは自動的に安全に保存され、リフレッシュされる
```

### 🛠️ サーバー管理コマンド

```bash
# 全サーバー一覧
claude mcp list

# 特定サーバーの詳細
claude mcp get notion

# サーバー削除
claude mcp remove notion

# Claude Code内でステータス確認
/mcp
```

### 🌟 人気のMCP統合

#### 開発ツール
- **GitHub**: リポジトリ、Issue、PR管理
- **Sentry**: エラー監視とトラッキング
- **Serena**: セマンティックコード検索と編集（Python、Java、TypeScript対応）
- **Hugging Face**: AI モデルとデータセット
- **Jam**: バグレポートとスクリーンレコーディング

#### ドキュメント・学習
- **Context7**: バージョン対応の最新ドキュメントを動的に取得

#### プロジェクト管理
- **Jira**: タスクとスプリント管理
- **Asana**: プロジェクト計画
- **Linear**: Issue トラッキング
- **Monday.com**: ワークフロー管理
- **Notion**: ドキュメント管理

#### 決済サービス
- **Stripe**: 支払い処理
- **PayPal**: オンライン決済
- **Square**: POS システム
- **Plaid**: 銀行口座連携

#### デザインツール
- **Figma**: デザインファイル
- **Canva**: グラフィック作成
- **Cloudinary**: メディア管理

#### インフラストラクチャ
- **Vercel**: デプロイメント
- **Netlify**: ホスティング
- **Cloudflare**: CDN・DNS

#### データベース
- **Supabase**: PostgreSQLベースのBaaS、テーブル管理、クエリ実行、マイグレーション
- **HubSpot**: CRM
- **Airtable**: データベース
- **Daloopa**: 財務データ

#### 自動化
- **Zapier**: ワークフロー自動化
- **Workato**: エンタープライズ統合

### 📚 MCPリソースとプロンプト

#### リソース

@ メンションで外部コンテンツを参照：

```bash
claude "@github:issue://123 を分析して"
claude "@notion:page://project-spec を要約して"
```

#### プロンプトをコマンドとして実行

MCPサーバーのプロンプトをスラッシュコマンドとして実行：

```bash
/mcp__github__list_prs
/mcp__notion__search_pages
/mcp__stripe__list_customers
```

### 🏢 エンタープライズ設定

#### 管理MCPファイル: `managed-mcp.json`

組織は集中管理されたMCP制御を展開可能：

```json
{
  "approved_servers": [
    {
      "name": "github",
      "transport": "http",
      "url": "https://mcp.github.com/mcp"
    }
  ],
  "deny_unapproved": true,
  "disable_mcp": false
}
```

**機能**:
- 全従業員向けに承認されたサーバーを定義
- 未承認サーバーの追加を制限
- 必要に応じてMCPを完全に無効化
- 許可リスト/拒否リストでアクセス制御

### 💡 実践例

#### 1. エラー監視

```bash
# Sentryサーバーを追加
claude mcp add --transport http sentry https://mcp.sentry.io/mcp

# 認証
/mcp
# → ブラウザでSentryにログイン

# エラーパターンを分析
claude "@sentry:errors://project/myapp の直近24時間のエラーパターンを分析して"
```

#### 2. データベースクエリ

```bash
# PostgreSQLに接続
claude mcp add --transport stdio postgres \
  --env DATABASE_URL=postgresql://user:pass@localhost/db \
  -- npx -y @modelcontextprotocol/server-postgres

# 自然言語でクエリ
claude "過去7日間のアクティブユーザー数を教えて"
claude "売上トップ10の商品をリストして"
```

#### 3. コードレビュー

```bash
# GitHubサーバーをリンク
claude mcp add --transport http github https://mcp.github.com/mcp

# PRをレビュー
claude "@github:pr://123 をレビューしてセキュリティ問題をチェックして"

# Issueを作成
claude "見つかった問題をIssueとして作成して"
```

#### 4. Context7 - 最新ドキュメントの取得

```bash
# Context7をインストール
claude mcp add context7 -- npx -y @upstash/context7-mcp

# または、HTTPトランスポートでリモートサーバーとして
claude mcp add --transport http context7 https://mcp.context7.com/mcp

# 使用例: 最新のドキュメントを参照しながらコード作成
claude "use context7 で最新のReact 19のドキュメントを参照してコンポーネントを作成して"
claude "use context7 でNext.js 15の新機能を使った実装例を示して"
```

**特徴**:
- ライブラリのバージョンに対応した最新ドキュメントを自動取得
- プロンプトに「use context7」を含めるだけで動作
- 古い情報による実装ミスを防止

#### 5. Serena - セマンティックコード検索

```bash
# Serenaをインストール
claude mcp add serena -- npx -y @oraios/serena-mcp

# 使用例: コードベースの意味的な検索
claude "認証ロジックを実装している箇所を全て見つけて"
claude "データベーススキーマの変更履歴を追跡して"
```

**特徴**:
- IDEのような意味的コード理解
- Python、Java、TypeScript完全対応
- テキスト検索ではなくセマンティック検索
- 無料・オープンソース

**対応言語**:
- **直接サポート**: Python、Java、TypeScript
- **間接サポート**: Ruby、Go、C#

#### 6. Supabase - データベース管理

```bash
# Supabase MCPをインストール（HTTPトランスポート）
claude mcp add --transport http supabase https://mcp.supabase.com/mcp

# OAuth認証
/mcp
# → ブラウザでSupabaseにログイン

# 使用例
claude "新しいusersテーブルを作成して、email、name、created_atカラムを追加"
claude "過去30日間にサインアップしたユーザー数を教えて"
claude "マイグレーションファイルを生成して"
```

**主な機能**:
- テーブル設計とスキーマ管理
- SQLクエリの実行
- マイグレーション生成
- ブランチ管理
- TypeScript型定義の生成

**セキュリティ注意**:
- ⚠️ **開発環境のみ**: 本番環境には接続しない
- ⚠️ **プロジェクトスコープ**: 特定プロジェクトに限定
- ⚠️ **読み取り専用モード**: 実データを扱う場合は読み取り専用に設定

**ローカル開発**:
```bash
# Supabase CLI でローカル起動している場合
# http://localhost:54321/mcp でアクセス可能
```

### ⚠️ セキュリティと注意事項

**公式警告**:
> "Use third party MCP servers at your own risk - Anthropic has not verified the correctness or security of all these servers."

#### ベストプラクティス

1. **信頼**: インストール前にサーバーを信頼できるか確認
2. **プロンプトインジェクション**: 信頼できないコンテンツを取得するサーバーに注意
3. **環境変数**: 機密情報は環境変数で管理
4. **タイムアウト**: `MCP_TIMEOUT` 環境変数で起動遅延を設定
5. **出力制限**: `MAX_MCP_OUTPUT_TOKENS` で大規模データセットを監視

```bash
# タイムアウト設定（秒）
export MCP_TIMEOUT=30

# 出力トークン制限
export MAX_MCP_OUTPUT_TOKENS=10000
```

### 📖 参考リンク

#### 公式リソース
- **MCP公式ドキュメント**: https://docs.claude.com/en/docs/claude-code/mcp
- **MCPサーバーマーケットプレイス**: https://github.com/modelcontextprotocol/servers
- **MCP仕様**: https://spec.modelcontextprotocol.io/

#### 注目のMCPサーバー
- **Context7**: https://apidog.com/blog/context7-mcp-server/
- **Serena**: https://github.com/oraios/serena
- **Supabase MCP**: https://supabase.com/docs/guides/getting-started/mcp

---

## プラグイン（Plugins）

### 📚 プラグインとは

**定義**: カスタム機能でClaude Codeを拡張し、プロジェクトやチーム間で共有できるパッケージ

**特徴**:
- コマンド、エージェント、スキル、フック、MCPサーバーをバンドル
- マーケットプレイス経由で配布
- 有効化・無効化・アンインストールが簡単

### 🧩 プラグインの構成要素

プラグインは独立または組み合わせで動作する以下の要素で構成：

| 要素 | 説明 | 呼び出し方 |
|------|------|-----------|
| **コマンド** | カスタムスラッシュコマンド | `/command-name` |
| **エージェント** | 専門タスク用AIアシスタント | 自動/明示的 |
| **スキル** | Claudeが自律的に適用する機能 | 自動（モデル判断） |
| **フック** | イベントハンドラー | 自動（イベント発生時） |
| **MCPサーバー** | 外部ツール統合 | @メンション、/mcp__ |

### 📁 プラグイン構造

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json              # メタデータ
├── commands/                     # スラッシュコマンド
│   ├── review.md
│   └── deploy.md
├── agents/                       # カスタムエージェント
│   ├── security-checker.md
│   └── performance-analyzer.md
├── skills/                       # 拡張機能
│   └── api-docs/
│       └── SKILL.md
└── hooks/                        # イベントハンドラー
    └── pre-commit.sh
```

#### plugin.json 例

```json
{
  "name": "team-dev-tools",
  "version": "1.0.0",
  "description": "チーム開発用の標準ツールセット",
  "author": "DevOps Team",
  "commands": ["review", "deploy"],
  "agents": ["security-checker", "performance-analyzer"],
  "skills": ["api-docs"],
  "hooks": ["pre-commit"]
}
```

### 📦 マーケットプレイスシステム

**マーケットプレイス**: プラグインカタログ

#### プラグインのライフサイクル

```bash
# 1. マーケットプレイス追加
/plugin marketplace add company https://plugins.company.com

# 2. プラグイン閲覧
/plugin

# 3. インストール
/plugin install team-dev-tools@company

# 4. 有効化（通常は自動）
/plugin enable team-dev-tools

# 5. 無効化（削除せず無効に）
/plugin disable team-dev-tools

# 6. アンインストール
/plugin uninstall team-dev-tools
```

### 👥 チーム設定

#### リポジトリレベルの設定

`.claude/settings.json` でプラグインを自動設定：

```json
{
  "plugins": {
    "required": [
      {
        "name": "team-dev-tools",
        "marketplace": "company",
        "version": "^1.0.0"
      }
    ]
  }
}
```

**動作**:
- チームメンバーがリポジトリフォルダを信頼
- プラグインが自動的にインストール
- チーム全体で統一された環境

### 🔄 コンポーネント間の関係

```
プラグイン
├── スキル → エージェントの機能を拡張
├── コマンド → プラグイン機能へのインターフェイス
├── MCP → 外部ツールとの統合
└── フック → ユーザーアクションに基づくワークフロー
```

**例**: コードレビュープラグイン

```
code-review-plugin/
├── commands/review.md          # /review コマンド
├── agents/reviewer.md          # レビューエージェント
├── skills/security-check/      # セキュリティチェックスキル
│   └── SKILL.md
└── hooks/pre-push.sh           # プッシュ前の自動レビュー
```

**ワークフロー**:
1. ユーザーが `/review` コマンドを実行
2. `reviewer` エージェントが起動
3. `security-check` スキルが自動適用
4. `pre-push` フックが git push 前に自動レビュー

### 📖 参考リンク

- **プラグイン公式ドキュメント**: https://docs.claude.com/en/docs/claude-code/plugins
- **プラグインリファレンス**: https://docs.claude.com/en/docs/claude-code/plugins-reference

---

## 使い分けガイド

### 🤔 どれを使うべき？

#### 状況別の選択フローチャート

```
質問: 何を実現したいですか？

├─ 外部サービスと連携したい
│  └─→ MCP を使用
│     例: GitHub、Notion、Stripe、PostgreSQL
│
├─ タスクを専門的なAIに委譲したい
│  └─→ エージェント を使用
│     例: コードレビュー、デバッグ、データ分析
│
├─ Claudeに新しい能力を追加したい
│  └─→ スキル を使用
│     例: チェックリスト適用、テンプレート生成
│
└─ 複数の機能をパッケージ化してチーム配布したい
   └─→ プラグイン を使用
      例: チーム標準ツールセット、社内ワークフロー
```

### 📊 詳細比較表

| 特性 | エージェント | スキル | MCP | プラグイン |
|------|-------------|--------|-----|-----------|
| **目的** | タスク委譲 | 機能拡張 | 外部統合 | パッケージ化 |
| **起動** | 自動/明示的 | 自動 | 設定後利用 | インストール後 |
| **コンテキスト** | 独立 | メイン | メイン | N/A（コンテナ） |
| **設定場所** | `.claude/agents/` | `.claude/skills/` | `.mcp.json` | マーケットプレイス |
| **共有方法** | Git/プラグイン | Git/プラグイン | Git | マーケットプレイス |
| **権限制御** | `tools` フィールド | `allowed-tools` | サーバー側 | 各コンポーネント |
| **ユースケース** | 専門的調査・分析 | ワークフロー自動化 | API・DB連携 | チーム標準化 |

### 🎯 ユースケース別推奨

#### 1. コードレビューを自動化したい

**推奨の組み合わせ**:
```
エージェント + スキル + MCP + プラグイン
```

**実装例**:
```
code-review-plugin/
├── agents/reviewer.md           # レビューエージェント
├── skills/security-check/       # セキュリティチェックスキル
│   └── SKILL.md
├── .mcp.json                    # GitHub MCP設定
└── commands/review.md           # /review コマンド
```

**ワークフロー**:
1. `/review` コマンド実行
2. `reviewer` エージェントが起動
3. `security-check` スキルでチェックリスト適用
4. GitHub MCP で PR にコメント投稿

#### 2. データベースを自然言語でクエリしたい

**推奨**: MCP

```bash
# PostgreSQL MCPサーバー追加
claude mcp add --transport stdio postgres \
  --env DATABASE_URL=postgresql://... \
  -- npx -y @modelcontextprotocol/server-postgres

# 自然言語でクエリ
claude "先月のアクティブユーザー数は？"
claude "売上トップ10の商品を教えて"
```

#### 3. 特定のコーディングスタイルを強制したい

**推奨**: スキル

```markdown
<!-- .claude/skills/style-guide/SKILL.md -->
---
name: coding-style-guide
description: TypeScriptコード作成時に会社のスタイルガイドを適用
---

# コーディングスタイルガイド

TypeScriptコードを作成する際、以下のルールに従ってください：

1. **命名規則**:
   - 変数/関数: camelCase
   - クラス: PascalCase
   - 定数: UPPER_SNAKE_CASE

2. **インポート順序**:
   1. 外部ライブラリ
   2. 内部モジュール
   3. 型定義

3. **コメント**: JSDocスタイルで記述
```

#### 4. 複雑なリサーチタスクを委譲したい

**推奨**: エージェント

```markdown
<!-- .claude/agents/researcher.md -->
---
name: codebase-researcher
description: コードベースの深い調査と分析を実施
tools: Read,Grep,Bash
model: sonnet
---

# コードベースリサーチャー

あなたは経験豊富なソフトウェアアーキテクトです。コードベースを徹底的に調査し、以下を提供してください：

1. アーキテクチャの概要
2. 主要なコンポーネントとその責務
3. データフロー
4. 改善の機会

## 調査手順

1. ディレクトリ構造を把握
2. 主要ファイルを特定
3. 依存関係を分析
4. パターンと慣習を抽出

## 出力形式

- マークダウン形式の詳細レポート
- 図解（Mermaid記法）
- 具体的な改善提案
```

**使用例**:
```bash
claude "codebase-researcherエージェントを使って認証システムを調査して"
```

#### 5. チーム全体で標準ツールを共有したい

**推奨**: プラグイン

```
team-standards-plugin/
├── .claude-plugin/plugin.json
├── commands/
│   ├── test.md                 # /test - テスト実行
│   ├── lint.md                 # /lint - Lint実行
│   └── deploy.md               # /deploy - デプロイ
├── agents/
│   ├── code-reviewer.md        # コードレビュー
│   └── security-scanner.md     # セキュリティスキャン
├── skills/
│   ├── api-docs/              # API ドキュメント生成
│   └── error-handling/        # エラーハンドリング標準
└── hooks/
    ├── pre-commit.sh          # コミット前チェック
    └── pre-push.sh            # プッシュ前チェック
```

**チーム設定** (`.claude/settings.json`):
```json
{
  "plugins": {
    "required": [
      {
        "name": "team-standards",
        "marketplace": "company",
        "version": "^2.0.0"
      }
    ]
  }
}
```

### 🔀 組み合わせパターン

#### パターン1: MCP + エージェント

外部データを専門エージェントで分析：

```bash
# Sentryエラーを監視エージェントで分析
claude "error-analysisエージェントを使って @sentry:errors://myapp の最新エラーを調査して"
```

#### パターン2: スキル + エージェント

スキルでエージェントの機能を拡張：

```markdown
<!-- .claude/skills/test-patterns/SKILL.md -->
---
name: test-patterns
description: テストコード作成時にベストプラクティスを適用
---
# テストパターン集
...
```

```markdown
<!-- .claude/agents/test-generator.md -->
---
name: test-generator
description: 包括的なテストコードを生成
---
# テストジェネレーター
test-patterns スキルのパターンを使用してテストを作成します。
...
```

#### パターン3: MCP + スキル + プラグイン

完全な統合ワークフロー：

```
deployment-plugin/
├── .mcp.json                   # Vercel, GitHub MCP設定
├── skills/deployment-checklist/
│   └── SKILL.md               # デプロイ前チェックリスト
└── commands/deploy.md         # /deploy コマンド
```

**ワークフロー**:
1. `/deploy` コマンド実行
2. `deployment-checklist` スキルが自動適用
3. GitHub MCP でPRステータス確認
4. Vercel MCP でデプロイ実行

---

## 実践例

### 例1: セキュリティ監査ワークフロー

#### 構成

```
.claude/
├── agents/
│   └── security-auditor.md
├── skills/
│   └── security-checklist/
│       └── SKILL.md
└── .mcp.json                   # Sentry, GitHub
```

#### security-auditor.md

```markdown
---
name: security-auditor
description: コードベースのセキュリティ脆弱性を包括的に監査
tools: Read,Grep,Bash
model: sonnet
---

# セキュリティ監査エージェント

OWASP Top 10の脆弱性を中心に、コードベースを体系的に監査します。

## 監査項目

1. **インジェクション攻撃**
   - SQLインジェクション
   - コマンドインジェクション
   - XSS

2. **認証・認可**
   - 脆弱なパスワードポリシー
   - セッション管理の問題
   - 不適切なアクセス制御

3. **データ保護**
   - 暗号化されていない機密データ
   - ハードコードされたシークレット
   - 不適切なログ出力

## 出力形式

### 🔴 重大（即座に修正が必要）
- ファイル名:行番号
- 問題の説明
- 修正案

### 🟡 警告（計画的に修正）
...

### 🟢 良好な実装
...
```

#### security-checklist/SKILL.md

```markdown
---
name: security-checklist
description: コード作成・レビュー時にセキュリティチェックリストを自動適用
allowed-tools: Read,Grep
---

# セキュリティチェックリスト

## 入力検証

- [ ] ユーザー入力のサニタイズ
- [ ] 型チェック
- [ ] 境界値チェック

## 認証

- [ ] パスワードのハッシュ化（bcrypt、Argon2）
- [ ] JWTトークンの署名検証
- [ ] セッションタイムアウト

## データベース

- [ ] パラメータ化クエリ
- [ ] ORMの適切な使用
- [ ] 最小権限の原則

## API

- [ ] CORS設定
- [ ] レート制限
- [ ] APIキーの環境変数管理
```

#### 使用方法

```bash
# 1. Sentry MCPでエラー監視
claude mcp add --transport http sentry https://mcp.sentry.io/mcp

# 2. セキュリティ監査実行
claude "security-auditorエージェントを使って認証モジュールを監査して"

# 3. 見つかった問題をGitHub Issueに
claude "@sentry:errors://auth-module の問題と合わせてGitHub Issueを作成して"
```

### 例2: データ分析ワークフロー

#### 構成

```
.claude/
├── agents/
│   └── data-analyst.md
├── skills/
│   └── sql-patterns/
│       ├── SKILL.md
│       └── examples/
│           ├── aggregation.sql
│           └── window-functions.sql
└── .mcp.json                   # PostgreSQL, BigQuery
```

#### data-analyst.md

```markdown
---
name: data-analyst
description: データベースを分析し、ビジネスインサイトを提供
tools: Bash
model: sonnet
---

# データアナリストエージェント

sql-patterns スキルを使用して効率的なクエリを作成し、データを分析します。

## 分析アプローチ

1. **要件理解**: ビジネス質問を明確化
2. **データ調査**: テーブル構造とリレーションシップを把握
3. **クエリ作成**: 最適化されたSQLクエリを生成
4. **結果解釈**: ビジネスコンテキストで結果を説明
5. **可視化提案**: 適切なチャート形式を推奨

## 出力形式

### 📊 分析結果
- 主要な発見
- 統計サマリー

### 💡 インサイト
- ビジネスへの影響
- 推奨アクション

### 📈 可視化案
- チャートタイプ
- 実装コード（Matplotlib、Chart.js等）
```

#### sql-patterns/SKILL.md

```markdown
---
name: sql-patterns
description: SQL クエリ作成時にパフォーマンス最適化パターンを適用
---

# SQL パターン集

## 集約クエリ

### GROUP BY の最適化
```sql
-- ❌ 非効率
SELECT user_id, COUNT(*)
FROM orders
WHERE created_at > '2025-01-01'
GROUP BY user_id;

-- ✅ 効率的（インデックス活用）
SELECT user_id, COUNT(*)
FROM orders
WHERE created_at > '2025-01-01'
GROUP BY user_id
HAVING COUNT(*) > 5;
```

## ウィンドウ関数

### ランキング
```sql
-- 部門ごとの給与ランキング
SELECT
  employee_id,
  department_id,
  salary,
  RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) as rank
FROM employees;
```

## N+1問題の回避

```sql
-- ❌ N+1問題
SELECT * FROM users;
-- 各ユーザーごとにクエリ
SELECT * FROM orders WHERE user_id = ?;

-- ✅ JOINで一度に取得
SELECT u.*, o.*
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```
```

#### 使用方法

```bash
# 1. データベース接続
claude mcp add --transport stdio postgres \
  --env DATABASE_URL=postgresql://user:pass@localhost/analytics \
  -- npx -y @modelcontextprotocol/server-postgres

# 2. データ分析実行
claude "data-analystエージェントを使って先月の売上トレンドを分析して"

# 3. 結果をNotionに保存
claude "分析結果を@notion:page://monthly-report に追加して"
```

### 例3: チームプラグイン

#### フルスタックチーム向けプラグイン

```
fullstack-team-plugin/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── api.md                  # /api - API エンドポイント生成
│   ├── test.md                 # /test - テスト実行
│   ├── migrate.md              # /migrate - DB マイグレーション
│   └── deploy.md               # /deploy - デプロイ
├── agents/
│   ├── api-designer.md         # API 設計
│   ├── frontend-reviewer.md    # フロントエンドレビュー
│   └── backend-reviewer.md     # バックエンドレビュー
├── skills/
│   ├── rest-api/              # REST API 設計パターン
│   ├── react-patterns/        # React ベストプラクティス
│   └── sql-patterns/          # SQL 最適化
├── hooks/
│   ├── pre-commit.sh          # Lint + フォーマット
│   └── pre-push.sh            # テスト実行
└── .mcp.json                  # GitHub, Vercel, PostgreSQL
```

#### plugin.json

```json
{
  "name": "fullstack-team-tools",
  "version": "2.1.0",
  "description": "フルスタック開発チーム向け標準ツールセット",
  "author": "Engineering Team",
  "homepage": "https://github.com/company/claude-plugins",
  "commands": ["api", "test", "migrate", "deploy"],
  "agents": ["api-designer", "frontend-reviewer", "backend-reviewer"],
  "skills": ["rest-api", "react-patterns", "sql-patterns"],
  "hooks": ["pre-commit", "pre-push"],
  "mcp_servers": [
    {
      "name": "github",
      "transport": "http",
      "url": "https://mcp.github.com/mcp"
    },
    {
      "name": "vercel",
      "transport": "http",
      "url": "https://mcp.vercel.com/mcp"
    }
  ]
}
```

#### チーム設定 (.claude/settings.json)

```json
{
  "plugins": {
    "required": [
      {
        "name": "fullstack-team-tools",
        "marketplace": "company-internal",
        "version": "^2.0.0"
      }
    ]
  },
  "mcp": {
    "servers": {
      "github": {
        "scope": "project"
      },
      "vercel": {
        "scope": "project"
      },
      "postgres": {
        "scope": "local",
        "env": {
          "DATABASE_URL": "${DATABASE_URL}"
        }
      }
    }
  }
}
```

#### ワークフロー例

```bash
# 新機能開発
claude "/api で新しい認証エンドポイントを作成して"
# → api-designer エージェントが起動
# → rest-api スキルでパターン適用

# フロントエンド実装
claude "React でログインフォームを作成して"
# → react-patterns スキルが自動適用

# コードレビュー
claude "frontend-reviewer を使って変更をレビューして"
# → GitHub MCP でPRにコメント

# テスト
claude "/test"
# → pre-push フックが自動実行

# デプロイ
claude "/deploy production"
# → Vercel MCP でデプロイ
# → GitHub MCP でリリースタグ作成
```

---

## まとめ

### 🎯 重要なポイント

1. **エージェント**: タスクを専門的なAIに委譲（独立コンテキスト）
2. **スキル**: Claudeの機能を拡張（自動適用）
3. **MCP**: 外部サービスとの統合（数百のツール）
4. **プラグイン**: 全てをパッケージ化してチーム共有

### 📊 選択基準

| やりたいこと | 使うもの |
|-------------|---------|
| 外部API/DBに接続 | MCP |
| 複雑なタスクを委譲 | エージェント |
| ワークフローを自動化 | スキル |
| チームで標準化 | プラグイン |

### ✨ ベストプラクティス

#### 全般
1. **小さく始める**: 1つの機能から開始
2. **段階的に拡張**: 必要に応じて機能追加
3. **ドキュメント化**: 使い方を明確に記述
4. **バージョン管理**: 設定ファイルをgitにコミット

#### エージェント
- 単一責任の原則
- 詳細なシステムプロンプト
- 最小権限のツールアクセス

#### スキル
- 具体的な説明文
- プログレッシブディスクロージャー
- 単一機能に集中

#### MCP
- 信頼できるサーバーのみインストール
- 環境変数で機密情報を管理
- エンタープライズは allowlist/denylist 使用

#### プラグイン
- マーケットプレイス経由で配布
- セマンティックバージョニング
- チーム設定はリポジトリで管理

### 🚀 次のステップ

1. **実験**: 小さなプロジェクトで試す
2. **カスタマイズ**: チームのニーズに合わせて調整
3. **共有**: 効果的なパターンをチームに展開
4. **改善**: フィードバックを元に継続的に改善

### 📖 参考リソース

- **Subagents**: https://docs.claude.com/en/docs/claude-code/sub-agents
- **Skills**: https://docs.claude.com/en/docs/claude-code/skills
- **MCP**: https://docs.claude.com/en/docs/claude-code/mcp
- **Plugins**: https://docs.claude.com/en/docs/claude-code/plugins
- **Claude Code 公式ドキュメント**: https://docs.claude.com/en/docs/claude-code/overview

---

**作成日**: 2025-11-05
**最終更新**: 2025-11-05
**公式ドキュメントバージョン**: 2025年11月版
