# Claude Code 調査ノート

**調査日**: 2025-11-05

## 📋 Claude Codeとは

Claude Codeは、Anthropicが開発した**ターミナルで動作するAIコーディングツール**です。

### 基本コンセプト

- **ターミナルベース**: IDE内のチャットウィンドウではなく、ターミナルで既存のツール体系と統合
- **実行能力**: ファイル編集、コマンド実行、Gitコミット作成を直接実行可能
- **AI駆動開発**: 自然言語で指示を出すだけで、計画・実装・検証を自動化
- **コンテキスト理解**: プロジェクト構造全体を認識し、適切な提案を実施

### 他のツールとの違い

従来のAIコーディングアシスタント（Copilot、Cursorなど）と異なり、Claude Codeは：

- ターミナルで動作するため、既存の開発ワークフローに自然に統合
- チャットではなく、実際にコードを書き、実行し、検証まで行う
- 複数ファイルにまたがる複雑な変更に対応
- Git操作やCI/CDとの連携が標準で可能

---

## 🎯 主要機能（4つの中核機能）

### 1. 機能開発

- 自然言語で「何を作りたいか」を伝えると、計画を立て、コードを書き、動作確認まで実施
- 複数ファイルにまたがる実装も対応
- テストコードの自動生成

**例**:
```bash
claude "add a user authentication feature with JWT tokens"
```

### 2. デバッグ支援

- エラーメッセージやスタックトレースから問題を特定
- 修正案を提案し、実装まで実施
- 再現手順を提供することでより正確な診断

**例**:
```bash
claude "fix the error: TypeError: Cannot read property 'map' of undefined"
```

### 3. コードベース探索

- プロジェクト構造全体を認識
- アーキテクチャパターンやデータモデルの理解
- Web情報やMCP連携でコンテキストを充実

**例**:
```bash
claude "explain the authentication flow in this codebase"
```

### 4. 自動化

- リント修正
- マージ競合解決
- リリースノート作成
- 定型業務の自動化

**例**:
```bash
claude "fix all linting errors and commit the changes"
```

---

## 🚀 インストールと初期設定

### インストール方法

#### macOS/Linux
```bash
# ネイティブインストール（推奨）
curl -fsSL https://claude.ai/install.sh | bash

# または Homebrew
brew install --cask claude-code
```

#### Windows
```powershell
# PowerShell
irm https://claude.ai/install.ps1 | iex
```

#### NPM
```bash
npm install -g @anthropic-ai/claude-code
```

### 動作環境要件

- **OS**: macOS 10.15+、Ubuntu 20.04+/Debian 10+、Windows 10+
- **メモリ**: 4GB 以上
- **Node.js**: バージョン 18 以上（NPM インストール時のみ）
- **インターネット接続**: 認証と AI 処理に必須

### 認証オプション

1. **Claude Console**（デフォルト）- console.anthropic.com でアクティブな課金が必要
2. **Claude アプリ** - Pro または Max プランで統一管理可能
3. **エンタープライズプラットフォーム** - Amazon Bedrock や Google Vertex AI

### 初回起動

```bash
# プロジェクトディレクトリに移動
cd your-awesome-project

# Claude Codeを起動
claude
```

初回起動時に認証プロンプトが表示されます。

---

## 💡 基本的な使い方

### クイックスタート5ステップ

#### 1. セッション開始
```bash
cd /path/to/your/project
claude
```

#### 2. 質問を試す
```
what does this project do?
what technologies does this project use?
```

#### 3. コード修正実行
```
add a hello world function to the main file
```

#### 4. Git操作
```
what files have I changed?
commit my changes with a descriptive message
```

#### 5. 本格的な開発
```
fix the bug where users can't log in
add unit tests for the authentication module
refactor the database connection code
```

### 便利なコマンド

```bash
# 対話モード
claude

# 単発実行
claude "タスクの内容"

# ヘルプ
claude --help
/help

# 更新確認
claude update

# 環境診断
claude doctor
```

---

## 🔧 高度な機能

### 1. MCP (Model Context Protocol) 統合

MCPは「AIのUSB-C」と呼ばれる、AIがさまざまなツールやサービスと連携するための標準プロトコルです。

#### MCPでできること

- **Google Drive**: ドキュメントの読み取り・編集
- **Slack**: メッセージの送受信、チャンネル情報の取得
- **GitHub**: Issue/PRの作成・管理
- **Postgres**: データベースクエリの実行
- **Figma**: デザイン情報の取得

#### MCP設定方法

```bash
# Context7の追加
claude mcp add context7 -- npx --yes @upstash/context7-mcp

# 一般的な形式
claude mcp add my-server -e API_KEY=123 -- /path/to/server arg1 arg2

# 設定ファイルの切り替え
claude --mcp-config=./custom-mcp.json
```

#### スコープ

- **local**: ユーザー全体で共有（デフォルト）
- **project**: プロジェクト固有（`.mcp.json`に保存）

#### 2025年の最新動向

2025年6月から「Desktop Extensions」により、Node.jsの個別インストールなしでワンクリック導入が可能になりました。

### 2. サブエージェント（2025年7月追加）

サブエージェントは、特定のタスクを処理するために呼び出すことができる専用のAIアシスタントです。

#### サブエージェントの特徴

- **独立したコンテキストウィンドウ**: メインの会話とは別の専用メモリ空間
- **特定の目的と専門分野**: タスク専用の知識と指示
- **許可された特定のツール**: Read、Writeなどのpermissionを設定可能
- **カスタムシステムプロンプト**: サブエージェント固有の指示
- **並列処理**: 最大10並列で処理を実行

#### サブエージェントの活用例

- **コードレビューエージェント**: コード品質の評価
- **テスト専門エージェント**: テストケースの作成と実行
- **依存関係チェックエージェント**: セキュリティ脆弱性のチェック
- **APIデザイナー**: REST API設計の最適化
- **データベース専門家**: クエリ最適化とスキーマ設計
- **セキュリティ監査**: セキュリティベストプラクティスの検証
- **パフォーマンス専門家**: ボトルネックの特定と最適化

#### サブエージェントの設定

```yaml
# .claude/agents/my-agent.yml
name: Code Reviewer
description: Reviews code for quality and best practices
system_prompt: |
  You are a senior code reviewer...
tools:
  - Read
  - Write
permissions:
  - read: "**/*.{js,ts,py}"
  - write: "**/*.md"
```

### 3. スキル機能（Agent Skills）

2025年10月17日リリースのClaude Code 2.0.20以降で追加された機能です。

#### スキルとは

スキルは、Claudeが必要に応じて読み込むための指示を含むファイルとスクリプトのセットです。

#### スキルの構成

- **SKILL.md**: スキルの説明と使い方
- **スクリプトやテンプレート**: オプションのサポートファイル

#### スキルの3つの定義方法

1. **個人スキル**: `~/.claude/skills/` - ユーザー全体で共有
2. **プロジェクトスキル**: `.claude/skills/` - プロジェクト固有
3. **プラグインスキル**: プラグインが提供

#### スキルの呼び出し

Claudeはユーザーが入力したプロンプトとスキルの名前、説明に基づいて、いつどのスキルを実行するかを自律的に決定します。

### 4. プラグイン機能

プラグインは、Claude Codeとシームレスに統合するカスタム機能を提供します。

#### プラグインのコンポーネント

- カスタムスラッシュコマンド
- サブエージェント
- MCP統合
- フック（イベントハンドラー）
- スキル

#### プラグインの配布

チーム内でプラグインを共有することで、標準化されたワークフローを構築できます。

### 5. 計画モード

複雑な変更前に`--permission-mode plan`フラグで読み取り専用分析を実施できます。

```bash
claude --permission-mode plan "refactor the authentication system"
```

変更前の徹底的な計画が実装ミスを防ぎます。

### 6. Extended Thinking

深い推論が必要なタスクで、Extended Thinkingを有効化すると、建築的な決定や複雑なバグ調査で詳細な分析が得られます。

```bash
claude --thinking "design a scalable microservices architecture"
```

---

## 🔍 一般的なワークフロー

### 1. コードベース理解

```bash
# プロジェクト全体の概要
claude "give me an overview of this codebase"

# アーキテクチャの理解
claude "explain the architecture patterns used in this project"

# データモデルの確認
claude "show me the data models and their relationships"
```

### 2. バグ修正

```bash
# エラーメッセージを共有
claude "I'm getting this error: [エラーメッセージ]"

# 修正の実施
claude "fix the authentication bug that prevents login"

# テストの追加
claude "add tests to prevent this bug from happening again"
```

### 3. コード品質向上

```bash
# リファクタリング
claude "refactor the user service to improve readability"

# テスト追加
claude "add unit tests for the payment processing module"

# ドキュメント作成
claude "generate API documentation for all endpoints"
```

### 4. ファイル参照

"@"記号を使用して特定のファイルを参照できます。

```bash
# 特定のファイルをレビュー
claude "review @src/auth/login.js for security issues"

# 複数ファイルの同時処理
claude "update @src/models/*.js to use TypeScript"
```

### 5. カスタムスラッシュコマンド

`.claude/commands/`に定型作業を登録し、チーム全体で再利用できます。

```bash
# カスタムコマンドの作成
# .claude/commands/review.md
# /review - コードレビューを実施

# カスタムコマンドの実行
/review
```

---

## 📊 実用的な活用パターン

### パターン1: 新機能開発

```bash
# 1. 仕様の確認
claude "what would be the best approach to add OAuth2 authentication?"

# 2. 計画の作成
claude --permission-mode plan "implement OAuth2 authentication"

# 3. 実装
claude "implement OAuth2 authentication with Google and GitHub providers"

# 4. テストの追加
claude "add integration tests for OAuth2 flows"

# 5. ドキュメント作成
claude "update the README with OAuth2 setup instructions"

# 6. コミット
claude "commit all changes with a descriptive message"
```

### パターン2: レガシーコードのリファクタリング

```bash
# 1. 現状分析
claude "analyze @legacy/payment-service.js and identify improvement areas"

# 2. リファクタリング計画
claude --permission-mode plan "refactor payment service to modern standards"

# 3. 段階的リファクタリング
claude "extract payment validation logic into separate functions"
claude "add TypeScript types to payment service"
claude "add error handling and logging"

# 4. テストの追加
claude "add comprehensive tests for refactored payment service"
```

### パターン3: バグ調査と修正

```bash
# 1. 問題の特定
claude "users report 500 errors when updating profile. Check @logs/error.log"

# 2. 詳細調査
claude "trace the profile update flow and identify the root cause"

# 3. 修正実装
claude "fix the profile update bug and add validation"

# 4. 回帰防止
claude "add tests to prevent this bug from recurring"
```

### パターン4: CI/CDとの連携

```bash
# GitHub Actionsでの自動化
# .github/workflows/claude-review.yml
claude --headless "review all changed files and create a summary"

# リリースノート自動生成
claude "generate release notes from commits since last tag"

# 自動リファクタリング
claude "fix all ESLint warnings in changed files"
```

---

## 🎯 社内導入のメリット

### 1. 生産性の向上

- **開発時間の短縮**: 定型的なコーディング作業の自動化
- **バグ修正の効率化**: 問題の特定から修正までの時間を削減
- **ドキュメント作成の簡素化**: 自動生成による時間節約

### 2. コード品質の改善

- **一貫性のあるコードスタイル**: AIによる統一的な実装パターン
- **テストカバレッジの向上**: 自動テスト生成
- **セキュリティの強化**: ベストプラクティスの適用

### 3. 学習コストの削減

- **新規参入の容易さ**: コードベース理解の支援
- **技術的な質問への回答**: 即座にアーキテクチャや実装の説明
- **ベストプラクティスの学習**: AIによる推奨実装の提示

### 4. チーム協働の促進

- **標準化されたワークフロー**: スキルやプラグインの共有
- **知識の共有**: カスタムコマンドによる組織知の蓄積
- **レビュープロセスの効率化**: サブエージェントによる自動レビュー

---

## ⚠️ 注意点と制約

### セキュリティ

- **機密情報の扱い**: APIキーやパスワードを直接コードに含めない
- **コードレビュー**: AIが生成したコードも必ず人間がレビュー
- **サードパーティMCPサーバー**: 自己責任で使用（Anthropicは検証していない）

### コスト

- **API利用料金**: Claude Consoleでの利用には課金が必要
- **プランの選択**: Pro/Maxプラン、またはエンタープライズプラットフォーム

### 技術的制約

- **インターネット接続**: オフラインでは使用不可
- **コンテキストウィンドウ**: 大規模なコードベースでは制限がある
- **言語サポート**: 一部のマイナー言語では精度が低い可能性

---

## 📚 参考リソース

### 公式ドキュメント

- **概要**: https://docs.claude.com/en/docs/claude-code/overview
- **クイックスタート**: https://docs.claude.com/en/docs/claude-code/quickstart
- **ワークフロー**: https://docs.claude.com/en/docs/claude-code/common-workflows
- **MCP統合**: https://docs.claude.com/ja/docs/claude-code/mcp
- **サブエージェント**: https://docs.claude.com/ja/docs/claude-code/sub-agents

### コミュニティリソース

- **Zenn記事**: Claude CodeのMCP設定、サブエージェント活用など
- **Qiita記事**: 実践的な使用例、トラブルシューティング
- **技術ブログ**: エンタープライズ導入事例、チーム活用法

---

## 🚀 次のステップ

1. **ユースケースの収集**: 具体的な活用シーンを整理
2. **プレゼンテーション構成**: 社内向け資料の構成を設計
3. **デモの準備**: 実演用のサンプルコードを作成
4. **FAQ作成**: よくある質問への回答を準備

---

**作成日**: 2025-11-05
**調査範囲**: 公式ドキュメント、Web検索、コミュニティ記事
**情報の正確性**: 2025年11月時点
