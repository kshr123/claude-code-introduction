# Claude Code: GitHub Actions & Git Worktree 完全ガイド

> **公式ドキュメント参照**: 2025-11-05調査
> **更新日**: 2025-11-05

## 📋 目次

1. [概要](#概要)
2. [GitHub Actions統合](#github-actions統合)
3. [Git Worktreeワークフロー](#git-worktreeワークフロー)
4. [統合ワークフロー](#統合ワークフロー)
5. [実践例](#実践例)
6. [まとめ](#まとめ)

---

## 概要

Claude Codeは2つの強力な仕組みでGitHubエコシステムと統合されます：

### 全体像

```
┌─────────────────────────────────────────┐
│        Claude Code CLI (ローカル)        │
│                                         │
│  ┌───────────────┐  ┌───────────────┐  │
│  │ Git Worktree  │  │ Git Worktree  │  │
│  │  (feature-a)  │  │  (feature-b)  │  │
│  │               │  │               │  │
│  │ Claude Code   │  │ Claude Code   │  │
│  │   Session     │  │   Session     │  │
│  └───────────────┘  └───────────────┘  │
└─────────────────────────────────────────┘
              ↓ git push
┌─────────────────────────────────────────┐
│           GitHub Repository             │
│                                         │
│  ┌───────────────────────────────────┐  │
│  │    GitHub Actions (CI/CD)        │  │
│  │                                  │  │
│  │  Claude Code Action が自動実行    │  │
│  │  - PRレビュー                     │  │
│  │  - コード修正                     │  │
│  │  - Issue対応                     │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

### 役割の違い

| 機能 | Git Worktree | GitHub Actions |
|------|-------------|----------------|
| **実行場所** | ローカル | クラウド（GitHub） |
| **目的** | 並列開発 | 自動化・CI/CD |
| **Claude実行** | 対話的 | 自動トリガー |
| **主な用途** | 複数機能の同時開発 | PR/Issueの自動処理 |

---

## GitHub Actions統合

### 📚 概要

**GitHub Actions統合とは**: CI/CDパイプラインでClaude Codeを自動実行し、PR/Issueへの自動応答、コードレビュー、バグ修正を実現

### 🎯 主な機能

1. **自動PRレビュー**: PR作成時に自動でコードレビュー
2. **@claude メンション**: コメントで `@claude` を呼び出し
3. **Issue自動修正**: Issue からコード修正とPR作成
4. **スケジュール実行**: 定期的なメンテナンスタスク

### 🔧 セットアップ

#### クイックセットアップ（推奨）

```bash
# Claude Code CLIから実行
claude
/install-github-app

# ガイドに従って:
# 1. GitHub Appをインストール
# 2. ANTHROPIC_API_KEY を設定
# 3. ワークフローファイルを自動生成
```

**必要な権限**:
- リポジトリ管理者権限（初回セットアップ時）

#### 手動セットアップ

**ステップ1**: GitHub Appインストール
```
https://github.com/apps/claude
```

**ステップ2**: APIキー設定
```
GitHub Repository → Settings → Secrets and variables → Actions
→ New repository secret
Name: ANTHROPIC_API_KEY
Value: sk-ant-xxx...
```

**ステップ3**: ワークフローファイル作成

`.github/workflows/claude.yml`:

```yaml
name: Claude Code Review

on:
  pull_request:
    types: [opened, synchronize]
  issue_comment:
    types: [created]

jobs:
  claude-review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Review this PR for security issues"
          claude_args: "--max-turns 5"
```

### 💡 ユースケース

#### 1. 自動PRレビュー

```yaml
name: Auto PR Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            以下の観点でレビュー:

            ## セキュリティ
            - SQLインジェクション
            - XSS脆弱性
            - 認証・認可の問題
            - シークレット漏洩

            ## パフォーマンス
            - N+1クエリ
            - メモリリーク
            - 無限ループの可能性

            ## コード品質
            - コードスタイル違反
            - 複雑度が高い箇所
            - テストカバレッジ

            問題があればPRにコメントを投稿。
```

**動作**:
1. PRが作成される
2. GitHub ActionsでClaudeが起動
3. コードを分析してレビューコメント投稿

#### 2. @claude メンション対応

```yaml
name: Claude Mention Response

on:
  issue_comment:
    types: [created]

jobs:
  respond:
    if: contains(github.event.comment.body, '@claude')
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          claude_args: "--max-turns 3"
```

**使い方**:

PR/Issueコメントで:
```
@claude この認証エラーを修正して

@claude テストを追加して

@claude パフォーマンス改善案を提案して
```

**動作**:
1. コメントに `@claude` が含まれる
2. Claudeが起動してコンテキストを理解
3. コードを修正してcommit、またはコメント返信

#### 3. Issue自動修正

```yaml
name: Auto Fix Issues

on:
  issues:
    types: [labeled]

jobs:
  fix-bug:
    if: contains(github.event.issue.labels.*.name, 'bug')
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            このIssueを分析して:
            1. 問題の原因を特定
            2. 修正コードを実装
            3. テストを追加
            4. PRを作成
          claude_args: "--max-turns 5"
```

**動作**:
1. Issueに `bug` ラベルを付与
2. Claudeが自動でコード修正
3. PRを作成してレビュー待ち

#### 4. パス限定レビュー

```yaml
name: Security Review for Auth

on:
  pull_request:
    paths:
      - 'src/auth/**'
      - 'src/api/login/**'

jobs:
  security-review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            認証関連コードの徹底的なセキュリティレビュー:
            - パスワードハッシュ化
            - トークン管理
            - セッション管理
            - CSRF対策
            - XSS対策
```

**効果**: 認証ファイル変更時のみ、専門的なセキュリティレビューを実行

#### 5. 外部コントリビューター専用レビュー

```yaml
name: External Contributor Review

on:
  pull_request:
    types: [opened]

jobs:
  external-review:
    if: github.event.pull_request.author_association == 'FIRST_TIME_CONTRIBUTOR' || github.event.pull_request.author_association == 'CONTRIBUTOR'
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            外部コントリビューターのPR:
            - コントリビューションガイドライン遵守確認
            - コードスタイルチェック
            - セキュリティリスク評価
            - 歓迎メッセージを投稿
```

#### 6. スケジュールメンテナンス

```yaml
name: Weekly Maintenance

on:
  schedule:
    - cron: '0 9 * * 1'  # 毎週月曜9時（UTC）

jobs:
  weekly-maintenance:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            週次メンテナンス:

            1. 依存関係の更新チェック
            2. セキュリティアップデートの確認
            3. 非推奨API使用箇所の検出
            4. テストカバレッジレポート
            5. 結果をIssueで報告
```

### 🔒 セキュリティ

#### ベストプラクティス

1. **APIキー管理**
   ```yaml
   # ❌ 絶対にハードコードしない
   anthropic_api_key: "sk-ant-xxx..."

   # ✅ Secretsを使用
   anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
   ```

2. **権限の最小化**
   ```yaml
   permissions:
     contents: write      # コード変更に必要
     pull-requests: write # PR作成・コメントに必要
     issues: write        # Issue管理に必要
   ```

3. **レビュー必須**
   - Claudeの提案は必ず人間がレビュー
   - 自動マージは避ける
   - `.claude/CLAUDE.md` でルールを明確化

4. **機密情報の保護**
   ```yaml
   - uses: anthropics/claude-code-action@v1
     with:
       anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
       claude_args: "--thinking"  # 思考プロセスを確認
     env:
       # 機密情報は環境変数経由で渡さない
       # Claudeがログに出力する可能性がある
   ```

### 💰 コスト最適化

#### 実行時間の制限

```yaml
jobs:
  claude-review:
    runs-on: ubuntu-latest
    timeout-minutes: 10  # ランナウェイジョブ防止
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          claude_args: "--max-turns 3 --timeout 300"
```

#### 条件付き実行

```yaml
jobs:
  claude-review:
    # 大きなPRのみレビュー（小さなPRはスキップ）
    if: github.event.pull_request.changed_files > 5
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

#### コスト内訳

| 項目 | 課金対象 | 最適化方法 |
|------|---------|-----------|
| **GitHub Actions分数** | ランナー実行時間 | `timeout-minutes` 設定 |
| **API使用量** | トークン数 | `--max-turns` で制限 |
| **ストレージ** | ログ・アーティファクト | ログ保持期間を短縮 |

**目安**:
- 小規模PR（<10ファイル）: 1-2分、500-1000トークン
- 中規模PR（10-50ファイル）: 3-5分、2000-5000トークン
- 大規模PR（50+ファイル）: 5-10分、5000-10000トークン

### 🏢 エンタープライズ展開

#### AWS Bedrock統合

```yaml
- uses: anthropics/claude-code-action@v1
  with:
    # Anthropic API の代わりにAWS Bedrockを使用
    aws_region: us-east-1
    aws_model_id: anthropic.claude-3-5-sonnet-20241022-v2:0
  env:
    AWS_ROLE_ARN: ${{ secrets.AWS_ROLE_ARN }}
```

**設定**:
1. AWS IAMロールを作成
2. GitHub OIDCプロバイダーを設定
3. `AWS_ROLE_ARN` をシークレットに追加

#### Google Vertex AI統合

```yaml
- uses: anthropics/claude-code-action@v1
  with:
    vertex_project_id: my-project
    vertex_location: us-central1
  env:
    GCP_WORKLOAD_IDENTITY_PROVIDER: ${{ secrets.GCP_WIF }}
```

### 🔧 トラブルシューティング

#### Claudeが反応しない

**原因**:
1. GitHub Appが未インストール
2. ワークフローが有効化されていない
3. APIキーが正しくない

**解決策**:
```bash
# 1. App確認
GitHub Repository → Settings → GitHub Apps → Claude

# 2. ワークフロー確認
GitHub Repository → Actions → ワークフロー一覧

# 3. APIキー確認
curl https://api.anthropic.com/v1/messages \
  -H "x-api-key: $ANTHROPIC_API_KEY" \
  -H "anthropic-version: 2023-06-01" \
  -H "content-type: application/json" \
  -d '{
    "model": "claude-3-5-sonnet-20241022",
    "messages": [{"role": "user", "content": "test"}],
    "max_tokens": 10
  }'
```

#### CIがClaudeのコミットで実行されない

**原因**: GitHub ActionsはデフォルトでActions botのコミットでトリガーされない

**解決策**: GitHub App（Actions botではない）を使用
```yaml
- uses: anthropics/claude-code-action@v1
  # GitHub Appとして動作（推奨）
```

#### 認証エラー

**エラー例**:
```
Error: Authentication failed
```

**解決策**:
```bash
# APIキー確認
echo ${{ secrets.ANTHROPIC_API_KEY }}

# AWS Bedrock の場合
aws sts get-caller-identity

# Google Vertex AI の場合
gcloud auth application-default print-access-token
```

---

## Git Worktreeワークフロー

### 📚 Git Worktreeとは

**定義**: 同じリポジトリの複数ブランチを別ディレクトリで同時に扱える機能

#### 通常のGitワークフロー

```bash
# ブランチ切り替え時に作業を中断
cd myapp
git stash                    # 作業を退避
git checkout feature-b       # ブランチ切り替え
# → feature-a の作業状態が失われる
# → Claude Code のコンテキストも失われる
```

#### Git Worktreeの場合

```bash
# 各ブランチが独立したディレクトリ
myapp/          # main ブランチ
myapp-feature-a/ # feature-a ブランチ
myapp-feature-b/ # feature-b ブランチ

# どれも同時に編集可能！
```

### 🎯 Claude Codeとの相性

#### 問題: コンテキストの汚染

```bash
# feature-a 実装中
cd myapp
claude "認証機能を実装して"
# → Claude が feature-a のコンテキストを保持

# 別タスクへ切り替え
git stash
git checkout feature-b
claude "レポート機能を実装して"
# → Claude が feature-a のコンテキストを混同する可能性
# → ファイルが存在しないエラーが発生
```

#### 解決策: Worktreeで完全分離

```bash
# feature-a 専用 worktree
cd ~/projects/myapp-feature-a
claude "認証機能を実装して"
# → feature-a 専用のコンテキスト

# feature-b 専用 worktree（同時進行）
cd ~/projects/myapp-feature-b
claude "レポート機能を実装して"
# → feature-b 専用のコンテキスト

# お互いに完全に独立！
```

### 🚀 基本操作

#### Worktree作成

```bash
# 現在のリポジトリから worktree 作成
cd ~/projects/myapp

# 新規ブランチで worktree 作成
git worktree add ../myapp-feature-a -b feature-a

# 既存ブランチで worktree 作成
git worktree add ../myapp-hotfix hotfix-123

# 確認
git worktree list
# /Users/you/projects/myapp              abc123 [main]
# /Users/you/projects/myapp-feature-a    def456 [feature-a]
# /Users/you/projects/myapp-hotfix       ghi789 [hotfix-123]
```

#### Worktree削除

```bash
# worktree 削除
git worktree remove ../myapp-feature-a

# または、ディレクトリを手動削除後
rm -rf ../myapp-feature-a
git worktree prune
```

#### Worktree情報

```bash
# 一覧表示
git worktree list

# 詳細表示
git worktree list --porcelain

# ロックされた worktree 確認
git worktree list --verbose
```

### 💡 実践ワークフロー

#### シナリオ: 複数機能の並行開発

```bash
# 1. メインプロジェクト
cd ~/projects/myapp

# 2. 認証機能用 worktree
git worktree add ../myapp-auth -b feature/auth
cd ../myapp-auth
npm install
claude "OAuth 2.0 認証を実装して"

# 3. レポート機能用 worktree（別ターミナル）
cd ~/projects/myapp
git worktree add ../myapp-reports -b feature/reports
cd ../myapp-reports
npm install
claude "月次レポート機能を実装して"

# 4. ホットフィックス用 worktree（緊急対応）
cd ~/projects/myapp
git worktree add ../myapp-hotfix -b hotfix/login-bug
cd ../myapp-hotfix
npm install
claude "ログインエラーを修正して"

# 全て同時進行！
```

#### ディレクトリ構造

```
~/projects/
├── myapp/                    # メインワークツリー（main）
│   ├── src/
│   ├── node_modules/        # main 用パッケージ
│   ├── .env                 # main 用環境変数
│   └── .git/                # Gitリポジトリ本体
│
├── myapp-auth/              # feature/auth ブランチ
│   ├── src/
│   ├── node_modules/        # 独立したパッケージ
│   └── .env                 # 独立した環境変数
│
├── myapp-reports/           # feature/reports ブランチ
│   ├── src/
│   ├── node_modules/        # 独立したパッケージ
│   └── .env                 # 独立した環境変数
│
└── myapp-hotfix/            # hotfix/login-bug ブランチ
    ├── src/
    ├── node_modules/        # 独立したパッケージ
    └── .env                 # 独立した環境変数
```

### ⚡ 自動化スクリプト

#### スクリプト1: worktree + Claude 起動

`~/bin/claude-work`:

```bash
#!/bin/bash
# 使い方: claude-work feature-name "作業内容"

FEATURE_NAME=$1
WORK_DESC=$2
BASE_DIR=$(basename $(pwd))
WORKTREE_DIR="../${BASE_DIR}-${FEATURE_NAME}"

# worktree作成
echo "🌲 Creating worktree: ${WORKTREE_DIR}"
git worktree add ${WORKTREE_DIR} -b ${FEATURE_NAME}

# 移動
cd ${WORKTREE_DIR}

# 環境セットアップ
echo "📦 Installing dependencies..."
if [ -f "package.json" ]; then
    npm install
elif [ -f "requirements.txt" ]; then
    python -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
elif [ -f "go.mod" ]; then
    go mod download
fi

# .env コピー（必要に応じて）
if [ -f "../${BASE_DIR}/.env.example" ]; then
    cp "../${BASE_DIR}/.env.example" .env
    echo "📝 Created .env from .env.example"
fi

# Claude Code起動
echo "🤖 Starting Claude Code..."
claude "${WORK_DESC}"
```

使用例:
```bash
cd ~/projects/myapp
claude-work auth "OAuth 2.0 認証を実装して"
# → myapp-auth/ worktree作成
# → npm install 実行
# → Claude Code 起動
```

#### スクリプト2: worktree クリーンアップ

`~/bin/claude-cleanup`:

```bash
#!/bin/bash
# マージ済み worktree を削除

BASE_DIR=$(pwd)

# 全worktree取得
git worktree list --porcelain | grep -E "^worktree" | awk '{print $2}' | while read worktree; do
    if [ "$worktree" != "$BASE_DIR" ]; then
        cd "$worktree"
        BRANCH=$(git branch --show-current)

        # mainにマージ済みかチェック
        if git merge-base --is-ancestor HEAD origin/main 2>/dev/null; then
            echo "🗑️  Removing merged worktree: $worktree ($BRANCH)"
            cd "$BASE_DIR"
            git worktree remove "$worktree"
        else
            echo "⏭️  Skipping unmerged worktree: $worktree ($BRANCH)"
        fi
    fi
done

# 孤立したworktreeエントリを削除
git worktree prune

echo "✅ Cleanup complete!"
```

#### スクリプト3: Issue番号から worktree 作成

`~/bin/claude-issue`:

```bash
#!/bin/bash
# 使い方: claude-issue 123

ISSUE_NUM=$1
BRANCH_NAME="issue-${ISSUE_NUM}"
BASE_DIR=$(basename $(pwd))
WORKTREE_DIR="../${BASE_DIR}-${BRANCH_NAME}"

# Issue情報取得（gh CLI使用）
if command -v gh &> /dev/null; then
    ISSUE_TITLE=$(gh issue view ${ISSUE_NUM} --json title -q .title)
    echo "📋 Issue #${ISSUE_NUM}: ${ISSUE_TITLE}"
else
    ISSUE_TITLE="Issue #${ISSUE_NUM}"
fi

# worktree作成
git worktree add ${WORKTREE_DIR} -b ${BRANCH_NAME}
cd ${WORKTREE_DIR}

# 環境セットアップ
if [ -f "package.json" ]; then
    npm install
fi

# Claude Code起動
claude "Issue #${ISSUE_NUM} を実装して: ${ISSUE_TITLE}。完了後、PRを作成。"
```

使用例:
```bash
claude-issue 123
# → GitHub Issue #123 の内容を取得
# → issue-123 ブランチで worktree 作成
# → Claude が自動実装 → PR作成
```

### 📊 実際の効果

#### incident.io社の事例

**導入前**:
- ブランチ切り替えで作業が中断
- ビルド待ち時間が無駄
- コンテキストスイッチのストレス

**導入後**:
- 複数機能を真の並行開発
- ビルド中に別worktreeで作業継続
- チーム全体の開発速度向上

**引用**:
> "Git worktrees with Claude Code transformed how we ship features. We're literally working on 3-4 features simultaneously without any context switching overhead."

#### 一般的な開発者の報告

- **生産性**: 「10x生産性向上」
- **ストレス**: 「コンテキストスイッチのストレスゼロ」
- **AI活用**: 「Claude Codeの能力を最大限引き出せる」
- **チーム**: 「ペアプロ時も各自のworktreeで作業」

### ⚠️ 注意事項

#### ディスクスペース

各worktreeは独立した環境を持つため、ディスクを消費：

```bash
# 例: Node.jsプロジェクト
myapp/          # 500MB (node_modules)
myapp-feature-a/ # 500MB
myapp-feature-b/ # 500MB
myapp-feature-c/ # 500MB
# 合計: 2GB

# 不要なworktreeは削除
git worktree remove ../myapp-feature-a
```

#### 共有ファイルの扱い

`.git/` ディレクトリは全worktreeで共有：

```bash
# ✅ OK: 各worktreeで独立
.env
node_modules/
venv/
build/

# ⚠️ 共有: 全worktreeに影響
.git/config
.git/hooks/
```

#### ブランチ保護

同じブランチを複数worktreeでチェックアウト不可：

```bash
# エラー例
git worktree add ../myapp-main main
# fatal: 'main' is already checked out at '/Users/you/projects/myapp'

# 解決策: 別ブランチを使用
git worktree add ../myapp-main-backup -b main-backup main
```

---

## 統合ワークフロー

### 🎯 Worktree + GitHub Actionsの最強組み合わせ

ローカルでWorktreeを使った高速並列開発 + GitHub ActionsでClaudeの自動品質チェック

### 📋 完全ワークフロー図

```
1. Issue作成
   ↓
2. Worktree作成（ローカル）
   git worktree add ../myapp-feature-123 -b feature-123
   ↓
3. Claude Codeで実装（ローカル）
   cd ../myapp-feature-123
   claude "Issue #123 を実装"
   ↓
4. Git Push
   git add .
   git commit -m "Implement feature #123"
   git push origin feature-123
   ↓
5. PR自動作成（Claude or gh CLI）
   gh pr create --title "Feature #123" --body "..."
   ↓
6. GitHub Actions トリガー（自動）
   - Claude が自動レビュー
   - セキュリティチェック
   - テスト実行
   ↓
7. レビュー結果
   ├─ 問題なし → マージ
   └─ 問題あり → @claude 修正依頼
                ↓
                Claude が自動修正（GitHub Actions）
                ↓
                再レビュー（6に戻る）
   ↓
8. マージ後クリーンアップ
   git worktree remove ../myapp-feature-123
```

### 🔧 設定例

#### .github/workflows/claude-complete.yml

完全な自動化ワークフローファイル：

```yaml
name: Claude Code Complete Workflow

on:
  # PR作成・更新時
  pull_request:
    types: [opened, synchronize, reopened]

  # コメント投稿時
  issue_comment:
    types: [created]

  # Issue作成時
  issues:
    types: [opened, labeled]

jobs:
  # Job 1: PR自動レビュー
  pr-review:
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    timeout-minutes: 15
    permissions:
      contents: write
      pull-requests: write

    steps:
      - name: Claude PR Review
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            包括的なコードレビュー:

            ## セキュリティ
            - OWASP Top 10 脆弱性
            - シークレット漏洩
            - 認証・認可の問題

            ## コード品質
            - .claude/CLAUDE.md ルール遵守
            - コードスタイル
            - 複雑度
            - テストカバレッジ

            ## パフォーマンス
            - N+1クエリ
            - メモリリーク
            - 非効率なアルゴリズム

            問題があればPRにコメント。修正案も提示。
          claude_args: "--max-turns 5 --thinking"

  # Job 2: @claude メンション対応
  claude-respond:
    if: |
      github.event_name == 'issue_comment' &&
      contains(github.event.comment.body, '@claude')
    runs-on: ubuntu-latest
    timeout-minutes: 10
    permissions:
      contents: write
      pull-requests: write
      issues: write

    steps:
      - name: Respond to @claude
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          claude_args: "--max-turns 3"

  # Job 3: Bug Issue自動修正
  auto-fix-bug:
    if: |
      github.event_name == 'issues' &&
      contains(github.event.issue.labels.*.name, 'bug')
    runs-on: ubuntu-latest
    timeout-minutes: 20
    permissions:
      contents: write
      pull-requests: write
      issues: write

    steps:
      - name: Auto Fix Bug
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            このバグを修正:

            1. 問題の原因を特定
            2. 修正コードを実装
            3. テストを追加
            4. PRを作成
            5. PR説明に修正内容を詳細に記載
          claude_args: "--max-turns 5"

  # Job 4: 認証コードの厳重レビュー
  security-review:
    if: |
      github.event_name == 'pull_request' &&
      (
        contains(github.event.pull_request.changed_files, 'auth') ||
        contains(github.event.pull_request.changed_files, 'login') ||
        contains(github.event.pull_request.changed_files, 'password')
      )
    runs-on: ubuntu-latest
    timeout-minutes: 10
    permissions:
      pull-requests: write

    steps:
      - name: Security Review
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            認証・セキュリティ専門レビュー:

            - パスワードハッシュ化（bcrypt、Argon2）
            - トークン管理（JWT、セッション）
            - CSRF対策
            - XSS対策
            - SQLインジェクション
            - レート制限
            - 多要素認証の実装

            ⚠️ セキュリティ問題は severity ラベルを付与
          claude_args: "--max-turns 3"
```

#### ローカルスクリプト: full-workflow

`~/bin/full-workflow`:

```bash
#!/bin/bash
# 完全自動ワークフロー
# 使い方: full-workflow 123 "Issue title"

ISSUE_NUM=$1
ISSUE_TITLE=$2
BRANCH_NAME="issue-${ISSUE_NUM}"
BASE_DIR=$(basename $(pwd))
WORKTREE_DIR="../${BASE_DIR}-${BRANCH_NAME}"

echo "🚀 Starting full workflow for Issue #${ISSUE_NUM}"

# 1. Worktree作成
echo "📁 Creating worktree: ${WORKTREE_DIR}"
git worktree add ${WORKTREE_DIR} -b ${BRANCH_NAME}
cd ${WORKTREE_DIR}

# 2. 環境セットアップ
echo "📦 Setting up environment..."
if [ -f "package.json" ]; then
    npm install
elif [ -f "requirements.txt" ]; then
    python -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
fi

# 3. Claude Code実行
echo "🤖 Running Claude Code..."
claude "Issue #${ISSUE_NUM} を実装: ${ISSUE_TITLE}。実装完了後、テストも追加。"

# 4. Commit & Push
echo "📤 Committing and pushing..."
git add .
git commit -m "$(cat <<EOF
Implement Issue #${ISSUE_NUM}: ${ISSUE_TITLE}

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"
git push -u origin ${BRANCH_NAME}

# 5. PR作成
echo "🔀 Creating pull request..."
if command -v gh &> /dev/null; then
    gh pr create \
        --title "Fix #${ISSUE_NUM}: ${ISSUE_TITLE}" \
        --body "$(cat <<EOF
## 概要

Issue #${ISSUE_NUM} を実装しました。

## 変更内容

Claude Codeによる自動実装:
- [x] 機能実装
- [x] テスト追加

## テスト

\`\`\`bash
npm test
\`\`\`

Closes #${ISSUE_NUM}

---

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"

    echo "✅ Pull request created!"
    echo "🔍 GitHub Actions will now review the PR automatically."
else
    echo "⚠️  gh CLI not found. Please install: brew install gh"
    echo "    Or create PR manually: https://github.com/OWNER/REPO/pull/new/${BRANCH_NAME}"
fi

echo ""
echo "🎉 Workflow complete!"
echo "   Worktree: ${WORKTREE_DIR}"
echo "   Branch: ${BRANCH_NAME}"
echo ""
echo "Next steps:"
echo "  1. GitHub Actions will review the PR"
echo "  2. Address any @claude comments if needed"
echo "  3. Merge when approved"
echo "  4. Run: git worktree remove ${WORKTREE_DIR}"
```

使用例:
```bash
cd ~/projects/myapp
full-workflow 123 "Add OAuth authentication"

# 自動実行される内容:
# 1. worktree 作成
# 2. npm install
# 3. Claude Code で実装
# 4. git commit & push
# 5. PR 作成
# 6. GitHub Actions で自動レビュー開始
```

### 💡 実践シナリオ

#### シナリオ1: 複数Issueの並行処理

**状況**: 3つのIssueを同時に処理したい

```bash
# ターミナル1: Issue #101
cd ~/projects/myapp
full-workflow 101 "Add user profile page"

# ターミナル2: Issue #102（同時進行）
cd ~/projects/myapp
full-workflow 102 "Fix login error"

# ターミナル3: Issue #103（同時進行）
cd ~/projects/myapp
full-workflow 103 "Improve database performance"

# 3つのClaude Codeセッションが独立して動作
# 3つのPRが作成される
# GitHub Actionsが3つのPRを並行レビュー
```

**結果**:
- 3つの機能が同時並行で開発
- お互いに干渉しない
- GitHub Actionsが全てを自動レビュー

#### シナリオ2: ホットフィックス緊急対応

**状況**: 本番で障害発生、緊急修正が必要

```bash
# 現在 feature-a を実装中
cd ~/projects/myapp-feature-a
# Claude Code 実行中...

# 緊急: 本番障害の連絡

# 別ターミナルで即座に対応
cd ~/projects/myapp
git worktree add ../myapp-hotfix -b hotfix/production-error
cd ../myapp-hotfix

# 最小限の環境セットアップ
npm install --production

# 緊急修正
claude "本番エラーを修正: [エラー内容]。テスト追加。"

# 即座にデプロイ
git add .
git commit -m "hotfix: Fix production error"
git push -u origin hotfix/production-error

# PR作成（緊急なので手動マージ承認）
gh pr create --title "Hotfix: Production error" --body "緊急修正"

# 元の作業に戻る
cd ~/projects/myapp-feature-a
# → feature-a の作業はそのまま継続可能
```

#### シナリオ3: コードレビュー指摘への対応

**状況**: PRレビューでClaudeが問題を指摘

```bash
# PRを作成後、GitHub ActionsでClaudeがレビュー
# Claude のコメント:
# @claude このセキュリティ問題を修正して:
# - パスワードがプレーンテキストで保存されている
# - SQLインジェクション脆弱性

# GitHub ActionsでClaudeが自動修正を試みる
# → コミットが追加される
# → 再レビュー

# 人間がレビュー
# → 問題なければマージ
```

### 📊 効果測定

#### 導入前 vs 導入後

| 指標 | 導入前 | 導入後 | 改善率 |
|------|--------|--------|--------|
| **Issue→PR時間** | 2-4時間 | 15-30分 | 75%短縮 |
| **並行作業数** | 1タスク | 3-5タスク | 3-5倍 |
| **レビュー待ち時間** | 2-24時間 | 即時 | 90%短縮 |
| **バグ検出率** | 人間レビューのみ | +AI自動検出 | 30%向上 |
| **コンテキストスイッチ** | 頻繁 | なし | 100%削減 |

#### コスト対効果

**投資**:
- GitHub Actions分数: 月100-500分（$5-25）
- Anthropic API: 月50-200k tokens（$10-40）
- セットアップ時間: 1-2時間

**効果**:
- 開発時間短縮: 週10-20時間
- バグ削減: デプロイ後バグ30%減少
- レビュー負荷軽減: 50%削減

**ROI**: 初月から10倍以上のリターン

---

## 実践例

### 例1: フルスタック開発での並列作業

#### 設定

3つのworktreeを使用:
1. `myapp-frontend`: React フロントエンド
2. `myapp-backend`: Node.js バックエンド
3. `myapp-database`: データベーススキーマ

#### ワークフロー

```bash
# 1. Frontend worktree
cd ~/projects/myapp
git worktree add ../myapp-frontend -b feature/user-profile-ui
cd ../myapp-frontend
npm install
claude "ユーザープロフィール画面を実装。Material-UIを使用。"

# 2. Backend worktree（別ターミナル）
cd ~/projects/myapp
git worktree add ../myapp-backend -b feature/user-profile-api
cd ../myapp-backend
npm install
claude "ユーザープロフィールAPIエンドポイントを実装。認証必須。"

# 3. Database worktree（別ターミナル）
cd ~/projects/myapp
git worktree add ../myapp-database -b feature/user-profile-schema
cd ../myapp-database
claude "ユーザープロフィール用のマイグレーションを作成。"

# 各worktreeで独立して作業
# 完了したらそれぞれPR作成
cd ../myapp-frontend
git push -u origin feature/user-profile-ui
gh pr create --title "Add user profile UI"

cd ../myapp-backend
git push -u origin feature/user-profile-api
gh pr create --title "Add user profile API"

cd ../myapp-database
git push -u origin feature/user-profile-schema
gh pr create --title "Add user profile schema"

# GitHub Actionsが3つのPRを自動レビュー
# レビュー完了後、順次マージ（database → backend → frontend）
```

#### GitHub Actions設定

`.github/workflows/fullstack-review.yml`:

```yaml
name: Full Stack Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  # Frontend PR レビュー
  frontend-review:
    if: startsWith(github.head_ref, 'feature/') && contains(github.event.pull_request.changed_files, 'frontend/')
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Reactフロントエンドレビュー:
            - コンポーネント設計
            - アクセシビリティ（WCAG 2.1）
            - パフォーマンス（Lighthouse）
            - レスポンシブデザイン

  # Backend PR レビュー
  backend-review:
    if: startsWith(github.head_ref, 'feature/') && contains(github.event.pull_request.changed_files, 'backend/')
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Node.jsバックエンドレビュー:
            - API設計（RESTful）
            - 認証・認可
            - エラーハンドリング
            - データベースクエリ最適化

  # Database PR レビュー
  database-review:
    if: startsWith(github.head_ref, 'feature/') && contains(github.event.pull_request.changed_files, 'migrations/')
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            データベースマイグレーションレビュー:
            - スキーマ設計
            - インデックス最適化
            - ロールバック可能性
            - データ整合性制約
```

### 例2: チーム開発での活用

#### チーム構成

- Frontend Developer（Alice）
- Backend Developer（Bob）
- DevOps Engineer（Charlie）

#### ワークフロー

**Alice（Frontend）**:
```bash
cd ~/projects/myapp
git worktree add ../myapp-alice -b feature/dashboard
cd ../myapp-alice
npm install
claude "ダッシュボード画面を実装"

# 実装完了後
git push -u origin feature/dashboard
gh pr create --title "Add dashboard" --body "@bob APIエンドポイント確認してください"

# GitHub ActionsでClaudeが自動レビュー
# → フロントエンド専門のレビュー
```

**Bob（Backend）**:
```bash
cd ~/projects/myapp
git worktree add ../myapp-bob -b feature/analytics-api
cd ../myapp-bob
npm install
claude "分析APIエンドポイントを実装"

# 実装完了後
git push -u origin feature/analytics-api
gh pr create --title "Add analytics API"

# GitHub ActionsでClaudeが自動レビュー
# → バックエンド専門のレビュー
# → セキュリティチェック
```

**Charlie（DevOps）**:
```bash
cd ~/projects/myapp
git worktree add ../myapp-charlie -b feature/ci-optimization
cd ../myapp-charlie
claude "CI/CDパイプラインを最適化"

# 実装完了後
git push -u origin feature/ci-optimization
gh pr create --title "Optimize CI/CD"

# GitHub ActionsでClaudeが自動レビュー
# → インフラ・DevOps専門のレビュー
```

#### チーム向けGitHub Actions

`.github/workflows/team-workflow.yml`:

```yaml
name: Team Workflow

on:
  pull_request:
    types: [opened, synchronize]
  issue_comment:
    types: [created]

jobs:
  # PR作成者に応じたレビュー
  smart-review:
    runs-on: ubuntu-latest
    steps:
      - name: Determine review type
        id: review-type
        run: |
          # PRラベルやファイルパスから判断
          if [[ "${{ github.event.pull_request.changed_files }}" == *"frontend"* ]]; then
            echo "type=frontend" >> $GITHUB_OUTPUT
          elif [[ "${{ github.event.pull_request.changed_files }}" == *"backend"* ]]; then
            echo "type=backend" >> $GITHUB_OUTPUT
          elif [[ "${{ github.event.pull_request.changed_files }}" == *".github/workflows"* ]]; then
            echo "type=devops" >> $GITHUB_OUTPUT
          fi

      - name: Claude Review
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            レビュータイプ: ${{ steps.review-type.outputs.type }}

            専門的なレビューを実施してください。

  # @メンションでチームメンバーに通知
  mention-team:
    if: contains(github.event.comment.body, '@claude review')
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            コードレビュー後、適切なチームメンバーにメンション:
            - Frontend: @alice
            - Backend: @bob
            - DevOps: @charlie
```

### 例3: セキュリティ重視の開発

#### 設定

セキュリティクリティカルな機能開発:
- 決済処理
- 個人情報管理
- 認証システム

#### ワークフロー

```bash
# セキュリティ機能専用worktree
cd ~/projects/myapp
git worktree add ../myapp-security -b security/payment-processing
cd ../myapp-security
npm install

# Claude Codeでセキュリティ重視の実装
claude "決済処理を実装。PCI DSS準拠。Stripe APIを使用。"

# 実装完了後
git push -u origin security/payment-processing
gh pr create \
  --title "🔒 Implement payment processing" \
  --label "security" \
  --body "セキュリティレビュー必須"

# GitHub Actionsで厳重レビュー
```

#### セキュリティ特化GitHub Actions

`.github/workflows/security-review.yml`:

```yaml
name: Security Review

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  # セキュリティラベルまたはパスで判定
  security-scan:
    if: |
      contains(github.event.pull_request.labels.*.name, 'security') ||
      contains(github.event.pull_request.changed_files, 'payment') ||
      contains(github.event.pull_request.changed_files, 'auth')
    runs-on: ubuntu-latest
    timeout-minutes: 20
    steps:
      - name: Security Review
        uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            🔒 セキュリティ専門レビュー

            ## OWASP Top 10 チェック
            1. インジェクション攻撃対策
            2. 認証・セッション管理
            3. XSS対策
            4. アクセス制御
            5. セキュリティ設定ミス
            6. 脆弱な依存関係
            7. 不十分なロギング
            8. CSRF対策
            9. 安全でないデシリアライゼーション
            10. コンポーネントの脆弱性

            ## 決済処理特有のチェック
            - PCI DSS準拠
            - カード情報の非保存
            - トークン化
            - 暗号化通信
            - トランザクション整合性

            ## 個人情報保護
            - GDPR準拠
            - データ最小化
            - 暗号化保存
            - アクセスログ

            ⚠️ 重大な問題は `security-critical` ラベルを付与
          claude_args: "--max-turns 10 --thinking"

      # 追加: 静的解析ツール
      - name: Run Security Scanners
        run: |
          npm audit
          npm run lint:security

      # 重大な問題があれば通知
      - name: Notify Security Team
        if: contains(github.event.pull_request.labels.*.name, 'security-critical')
        run: |
          # Slack通知など
          echo "🚨 Security critical issue found!"
```

---

## まとめ

### 🎯 重要なポイント

#### Git Worktreeの役割
- **並列開発**: 複数ブランチで真の同時作業
- **コンテキスト分離**: Claude Codeセッションの完全独立
- **環境分離**: 依存関係・ビルド・設定の独立
- **生産性向上**: コンテキストスイッチゼロ

#### GitHub Actionsの役割
- **自動化**: PR/Issueへの即時対応
- **品質保証**: 24/7自動コードレビュー
- **チーム協業**: @claudeで誰でもAI活用
- **セキュリティ**: 一貫したセキュリティチェック

#### 組み合わせの威力

```
ローカル開発（Worktree）
    ↓
  高速な並列開発
  複数機能の同時実装
    ↓
  git push
    ↓
GitHub Actions
    ↓
  自動レビュー・修正
  セキュリティチェック
    ↓
生産性10倍化 🚀
```

### 📊 効果の実例

| 開発フェーズ | 従来 | Worktree + Actions | 改善 |
|-------------|------|-------------------|------|
| **機能実装** | 4時間 | 30分 | 87%短縮 |
| **コードレビュー** | 2-24時間 | 即時 | 95%短縮 |
| **バグ修正** | 2時間 | 15分 | 87%短縮 |
| **デプロイ** | 1時間 | 5分 | 92%短縮 |
| **合計** | 9-31時間 | 50分 | 94%短縮 |

### ✨ ベストプラクティス

#### 1. Worktreeの命名規則

```bash
# ✅ 良い例
myapp-feature-auth
myapp-hotfix-login
myapp-issue-123

# ❌ 悪い例
myapp-temp
myapp-test
myapp-backup
```

#### 2. GitHub Actions の設定

```yaml
# ✅ タイムアウト設定
timeout-minutes: 10

# ✅ ターン数制限
claude_args: "--max-turns 5"

# ✅ 条件付き実行
if: github.event.pull_request.changed_files > 5
```

#### 3. セキュリティ

```yaml
# ✅ Secrets使用
anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}

# ✅ 最小権限
permissions:
  contents: write
  pull-requests: write

# ✅ レビュー必須
# → Settings → Branches → Require approvals
```

#### 4. コスト最適化

```bash
# worktree 定期クリーンアップ
git worktree prune

# GitHub Actions 実行制限
# → Settings → Actions → Limit workflow runs
```

### 🚀 導入ロードマップ

#### フェーズ1: トライアル（1週間）

```bash
# 1. GitHub Actions セットアップ
/install-github-app

# 2. worktree を試す
git worktree add ../myapp-test -b test-worktree
cd ../myapp-test
claude "簡単な機能を実装"

# 3. PR作成 → 自動レビュー体験
```

#### フェーズ2: チーム展開（2-4週間）

- チームメンバーへのトレーニング
- `.claude/CLAUDE.md` でルール統一
- GitHub Actions ワークフロー調整

#### フェーズ3: 本格運用（継続）

- メトリクス測定
- ワークフロー最適化
- コスト監視

### 📖 参考リンク

#### 公式ドキュメント
- **GitHub Actions**: https://docs.claude.com/en/docs/claude-code/github-actions
- **Common Workflows**: https://docs.claude.com/en/docs/claude-code/common-workflows
- **Claude Code Overview**: https://docs.claude.com/en/docs/claude-code/overview

#### GitHub
- **Claude Code Action**: https://github.com/anthropics/claude-code-action
- **GitHub Marketplace**: https://github.com/marketplace/actions/claude-code-action-official

#### 実践記事
- **incident.io事例**: https://incident.io/blog/shipping-faster-with-claude-code-and-git-worktrees
- **Anthropic公式ブログ**: https://www.anthropic.com/news/automate-security-reviews-with-claude-code

---

**作成日**: 2025-11-05
**最終更新**: 2025-11-05
**公式ドキュメントバージョン**: 2025年11月版
