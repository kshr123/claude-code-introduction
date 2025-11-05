# Claude Code FAQ とトラブルシューティング

**作成日**: 2025-11-05

Claude Codeを使用する際のよくある質問とトラブルシューティングをまとめています。

---

## 📚 基本的な質問

### Q1: Claude Codeは無料ですか？

**A**: いいえ、有料です。

| プラン | 料金 | 特徴 |
|-------|------|------|
| Claude Pro | 約2万円/月 | 個人利用向け |
| Claude Team | 約3万円/月/人 | チーム向け |
| Enterprise | 要相談 | Bedrock/Vertex AI経由 |

---

### Q2: どのプログラミング言語に対応していますか？

**A**: 主要な言語はすべて対応しています。

**対応言語**:
- Python, JavaScript, TypeScript
- Go, Rust, Java, C++, C#
- Ruby, PHP, Swift, Kotlin
- HTML, CSS, SQL

**フレームワーク**:
- React, Vue, Angular
- Django, Flask, FastAPI
- Express, Next.js
- Rails, Laravel

---

### Q3: GitHub CopilotやCursorと何が違いますか？

**A**: 実行環境と機能範囲が異なります。

| 機能 | GitHub Copilot | Cursor | Claude Code |
|------|----------------|--------|-------------|
| 実行環境 | エディタ内 | エディタ内 | ターミナル |
| コード補完 | ✓ | ✓ | ✓ |
| ファイル編集 | △ | ✓ | ✓ |
| コマンド実行 | ✗ | △ | ✓ |
| Git操作 | ✗ | △ | ✓ |
| 複数ファイル編集 | △ | ✓ | ✓ |
| 外部連携(MCP) | ✗ | ✗ | ✓ |
| プロジェクトメモリ | ✗ | △ | ✓ |

**Claude Codeの強み**:
- ターミナルベースで既存ワークフローに統合しやすい
- プロジェクトメモリで独自ルールを設定可能
- Git操作やコマンド実行まで自動化

---

### Q4: 既存のプロジェクトでも使えますか？

**A**: はい、既存プロジェクトでも問題なく使えます。

**使い方**:
1. プロジェクトディレクトリに移動
2. `claude`コマンドを実行
3. （オプション）`.claude/CLAUDE.md`でプロジェクトルールを設定

**注意点**:
- 既存のコードを理解するのに数秒かかる
- 大規模プロジェクト（数千ファイル）では制限がある場合がある

---

### Q5: オフラインでも使えますか？

**A**: いいえ、インターネット接続が必須です。

Claude CodeはAnthropicのAPIを使用するため、常時インターネット接続が必要です。

---

## 🔧 インストールとセットアップ

### Q6: インストールがうまくいきません

**A**: 以下を確認してください。

#### macOS/Linux

```bash
# エラー: permission denied
# 解決: sudoを使用
sudo curl -fsSL https://claude.ai/install.sh | bash

# エラー: command not found
# 解決: PATHを確認
echo $PATH
export PATH="$HOME/.claude/bin:$PATH"
```

#### npm経由

```bash
# エラー: npm not found
# 解決: Node.jsをインストール
# https://nodejs.org/

# エラー: permission denied
# 解決: npxで実行
npx @anthropic-ai/claude-code
```

---

### Q7: 認証がうまくいきません

**A**: 以下を試してください。

#### 認証方法を確認

Claude Codeには3つの認証方法があります：

1. **Claude Console**（デフォルト）
   - https://console.anthropic.com でアカウント作成
   - API Keyを取得
   - 課金設定が必要

2. **Claude アプリ**
   - Claude ProまたはMaxプラン契約
   - 統一管理可能

3. **エンタープライズプラットフォーム**
   - Amazon Bedrock
   - Google Vertex AI

#### トラブルシューティング

```bash
# 認証状態を確認
claude auth status

# 再認証
claude auth login

# 環境診断
claude doctor
```

---

### Q8: アップデートはどうやりますか？

**A**: 以下のコマンドでアップデートできます。

```bash
# アップデート確認
claude update

# 強制アップデート
claude update --force

# バージョン確認
claude --version
```

---

## 💻 使い方の質問

### Q9: プロンプトをどう書けばいいですか？

**A**: 明確で具体的に書くのがコツです。

#### ❌ 悪い例

```
コードを直して
```

#### ✅ 良い例

```
src/auth.jsのログイン処理で、パスワードが間違っている時のエラーメッセージを
「パスワードが正しくありません」に変更して
```

**ポイント**:
1. **ファイルを指定** - `@src/auth.js`
2. **具体的に説明** - 何をどうしたいか
3. **期待する結果** - 最終的にどうなるべきか

---

### Q10: Claude Codeが理解してくれません

**A**: 以下を試してください。

#### 1. プロジェクトメモリを設定

`.claude/CLAUDE.md`を作成してプロジェクトのルールを記載：

```markdown
# プロジェクト名

## 技術スタック
- 言語: Python 3.13
- フレームワーク: Django
- データベース: PostgreSQL

## コーディング規約
- PEP 8に準拠
- テストは必ず書く
```

#### 2. ファイルを明示的に参照

```bash
# @記号でファイル指定
claude "review @src/models/user.py"

# 複数ファイル
claude "@src/models/user.py と @src/views/auth.py を確認して"
```

#### 3. コンテキストを与える

```bash
# 悪い例
claude "エラーを修正して"

# 良い例
claude "このエラーを修正して:
TypeError: Cannot read property 'map' of undefined
at UserList.render (src/components/UserList.js:15)
"
```

---

### Q11: 変更を元に戻したい

**A**: Gitで元に戻せます。

```bash
# 変更を確認
git status
git diff

# 特定ファイルを元に戻す
git restore <file>

# 全ての変更を元に戻す
git restore .

# コミット前なら
git reset --hard HEAD
```

**重要**: Claude Codeの変更も通常のGit操作で管理できます。

---

### Q12: Claude Codeが勝手にコミットしてしまいます

**A**: `.claude/CLAUDE.md`で制御できます。

```markdown
## Claude Codeへの指示

- Git操作前に必ずユーザーの確認を取る
- `git push`は実行前に必ず確認
- コミットメッセージは日本語で記載
```

---

## 🚀 高度な使い方

### Q13: .claude/CLAUDE.mdには何を書けばいいですか？

**A**: プロジェクト固有のルールを記載します。

#### 最小限の例

```markdown
# プロジェクト名

## 技術スタック
- Python 3.13, Django

## コーディング規約
- PEP 8
- テストカバレッジ80%以上
```

#### 詳細な例

詳しくは [03_project_memory.md](./03_project_memory.md) を参照してください。

---

### Q14: MCPとは何ですか？どう使いますか？

**A**: Model Context Protocolの略で、外部サービスと連携する仕組みです。

#### MCPでできること

- **GitHub**: Issue/PRの作成・管理
- **Slack**: メッセージの送受信
- **Google Drive**: ドキュメントの読み書き
- **Postgres**: データベースクエリ
- **Notion**: ページの作成・編集

#### 設定方法

`.mcp.json`に記載：

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "your_token"
      }
    }
  }
}
```

詳しくは公式ドキュメントを参照：
https://docs.claude.com/ja/docs/claude-code/mcp

---

### Q15: サブエージェントとは何ですか？

**A**: 特定のタスク専用のAIアシスタントです。

#### 使用例

```bash
# 複数の専門家による並列レビュー
claude "security-auditor, performance-expert, code-quality チームでレビューして"
```

#### サブエージェントの作成

`.claude/agents/reviewer.yml`:

```yaml
name: Code Reviewer
description: Reviews code for quality and best practices
system_prompt: |
  You are a senior code reviewer...
tools:
  - Read
  - Write
```

詳しくは [02_research.md](./02_research.md) の「サブエージェント」セクションを参照。

---

## ⚠️ トラブルシューティング

### T1: Claude Codeが応答しない

**原因と対策**:

1. **インターネット接続**
   ```bash
   # 接続確認
   ping claude.ai
   ```

2. **API制限**
   - 利用量の上限に達している可能性
   - Claude Consoleで確認

3. **プロセスが固まっている**
   ```bash
   # Ctrl+Cで中断
   # または別ターミナルで
   pkill claude
   ```

---

### T2: 「Too many tokens」エラー

**原因**: コンテキストウィンドウの制限を超えている

**対策**:

1. **ファイルを明示的に指定**
   ```bash
   # ✗ プロジェクト全体を読み込もうとする
   claude "全体をリファクタリングして"

   # ✓ 特定ファイルを指定
   claude "refactor @src/auth.js"
   ```

2. **プロジェクトメモリを活用**
   - `.claude/CLAUDE.md`に必要な情報を記載
   - 毎回説明する必要がなくなる

3. **タスクを分割**
   ```bash
   # ✗ 一度に全てをやろうとする
   claude "全モジュールにテストを追加して"

   # ✓ モジュールごとに分割
   claude "add tests to @src/auth.js"
   claude "add tests to @src/users.js"
   ```

---

### T3: Git操作がうまくいかない

**原因と対策**:

1. **コミットメッセージが空**
   ```bash
   # エラー: empty commit message
   # 解決: メッセージを指定
   claude "コミットして。メッセージは「ログイン機能を追加」で"
   ```

2. **コンフリクトがある**
   ```bash
   # 手動で解決
   git status
   git diff
   # コンフリクトを解決後
   git add .
   claude "コミットして"
   ```

3. **ブランチが間違っている**
   ```bash
   # 現在のブランチ確認
   git branch

   # ブランチ切り替え
   git checkout main
   ```

---

### T4: セキュリティエラー「untrusted code」

**原因**: 信頼されていないコードの実行を試みている

**対策**:

1. **サンドボックスモードを無効化**（慎重に）
   ```bash
   claude --no-sandbox "コマンド"
   ```

2. **プロジェクトを信頼する**
   ```bash
   claude trust
   ```

**注意**: 信頼できるプロジェクトのみで実行してください。

---

### T5: 「Rate limit exceeded」エラー

**原因**: API呼び出しの制限を超えている

**対策**:

1. **少し待つ** - 数分後に再試行
2. **プランをアップグレード** - より高い制限のプランに変更
3. **使用頻度を調整** - 連続して大量のリクエストを避ける

---

## 📊 パフォーマンスの問題

### Q16: 応答が遅いです

**A**: 以下を確認してください。

1. **プロジェクトサイズ**
   - 大規模プロジェクト（数千ファイル）では遅くなる
   - ファイルを明示的に指定することで改善

2. **ネットワーク速度**
   - インターネット接続を確認
   - VPN使用時は速度が低下する可能性

3. **API応答時間**
   - Anthropicのサーバーの状態を確認
   - https://status.anthropic.com

---

### Q17: メモリ使用量が多いです

**A**: 以下を試してください。

```bash
# Claude Codeを再起動
# Ctrl+C で終了後、再度起動
claude

# キャッシュをクリア
claude cache clear

# 環境診断
claude doctor
```

---

## 🔒 セキュリティとプライバシー

### Q18: コードはAnthropicのサーバーに保存されますか？

**A**: 学習には使用されませんが、処理のために送信されます。

**公式の説明**:
- コードはAPIリクエストとして送信される
- **学習には使用されない**（Anthropicのポリシー）
- エンタープライズプラン（Bedrock/Vertex AI）なら社内で完結

**対策**:
- 機密情報を含むファイルは`.gitignore`
- エンタープライズプランを検討

---

### Q19: APIキーが漏れないか心配です

**A**: 適切に管理すれば安全です。

**ベストプラクティス**:

1. **環境変数で管理**
   ```bash
   # .envファイル
   API_KEY=your_key_here

   # .gitignore
   .env
   ```

2. **`.claude/CLAUDE.md`に記載しない**
   - プロジェクトメモリには機密情報を含めない

3. **`.mcp.json`をgitignore**
   ```gitignore
   .mcp.json
   ```

---

## 💡 ベストプラクティス

### Q20: 効果的な使い方のコツは？

**A**: 以下を実践してください。

#### 1. プロジェクトメモリを活用

`.claude/CLAUDE.md`を設定することで、毎回説明する手間が省けます。

#### 2. 具体的なプロンプト

- ✅ 「src/auth.jsのログイン関数にエラーハンドリングを追加」
- ❌ 「コードを改善して」

#### 3. 小さなタスクに分割

- ✅ 「まずユーザー認証を実装」→「次にトークン検証」
- ❌ 「認証システム全体を実装」

#### 4. レビューを忘れずに

- AIが生成したコードも必ず人間がレビュー
- テストを実行して動作確認

#### 5. Gitで管理

- 変更を細かくコミット
- 問題があれば簡単に元に戻せる

---

## 📚 さらに学ぶ

### 関連ガイド

- **[00_quickstart.md](./00_quickstart.md)** - 5分で始めるクイックスタート
- **[02_research.md](./02_research.md)** - 基本機能の詳細
- **[03_project_memory.md](./03_project_memory.md)** - プロジェクトメモリの活用
- **[04_use_cases.md](./04_use_cases.md)** - 具体的な活用例

### 公式リソース

- **公式ドキュメント**: https://docs.claude.com/en/docs/claude-code/overview
- **コミュニティ**: https://discord.gg/anthropic
- **ステータスページ**: https://status.anthropic.com

---

## 🆘 それでも解決しない場合

1. **公式ドキュメントを確認** - https://docs.claude.com/
2. **コミュニティで質問** - Discord、Reddit
3. **サポートに連絡** - support@anthropic.com

---

**作成日**: 2025-11-05
**更新日**: 2025-11-05
