# Claude Code 完全ガイド

**作成日**: 2025-11-05

Claude Codeを初めて使う人のための完全ガイドです。このドキュメント1つでClaude Codeの全体像を理解できます。

---

## 📚 目次

1. [Claude Codeとは](#claude-codeとは)
2. [クイックスタート](#クイックスタート)
3. [基本機能](#基本機能)
4. [プロジェクトメモリ](#プロジェクトメモリ)
5. [ユースケース](#ユースケース)
6. [FAQ](#faq)
7. [まとめ](#まとめ)

---

## Claude Codeとは

Claude Codeは、**ターミナルで動作するAIコーディングアシスタント**です。

### 3つの特徴

1. **ターミナルベース** - エディタに依存せず、既存のワークフローに自然に統合
2. **実行能力** - ファイル編集、コマンド実行、Gitコミットまで自動化
3. **カスタマイズ可能** - プロジェクトメモリで独自ルールを設定

### 他のツールとの違い

```
GitHub Copilot  → エディタ内のコード補完に特化
Cursor          → エディタ内でAI対話とコード編集
Claude Code     → ターミナルでファイル操作・コマンド実行・Git操作まで完結
```

**Claude Codeの強み**:
- プロジェクトメモリ（`.claude/CLAUDE.md`）で独自ルールを設定可能
- MCP（Model Context Protocol）で外部サービスと連携
- サブエージェントで専門的なタスクを並列処理

### CLI AI ツール詳細比較

| 機能 | GitHub Copilot CLI | Gemini CLI | Claude Code |
|------|-------------------|------------|-------------|
| **実行環境** | ターミナル | ターミナル | ターミナル |
| **ファイル編集** | ✗ | △（提案のみ） | ✓（直接編集） |
| **複数ファイル編集** | ✗ | ✗ | ✓ |
| **コマンド実行** | ✓（提案） | ✓（提案） | ✓（実行） |
| **Git操作** | ✗ | ✗ | ✓ |
| **プロジェクトメモリ** | ✗ | ✗ | ✓（.claude/CLAUDE.md） |
| **外部連携（MCP）** | ✗ | ✗ | ✓ |
| **コンテキスト理解** | 単発 | 単発 | プロジェクト全体 |
| **サブエージェント** | ✗ | ✗ | ✓ |

**Claude Codeの決定的な違い**:
1. **実行能力** - 提案だけでなく、実際にファイルを編集・コマンドを実行
2. **プロジェクトメモリ** - `.claude/CLAUDE.md`で独自ルールを永続化
3. **チーム共有** - プロジェクトメモリをGitで共有してワークフロー統一

---

## クイックスタート

### インストール（5分）

```bash
# macOS/Linux
curl -fsSL https://claude.ai/install.sh | bash

# Windows
irm https://claude.ai/install.ps1 | iex

# npm
npm install -g @anthropic-ai/claude-code
```

### 初めての3ステップ（5分）

#### Step 1: 起動

```bash
cd your-project
claude
```

#### Step 2: 質問してみる

```
このプロジェクトは何をするものですか？
```

#### Step 3: 簡単な変更

```
README.mdに「Hello, Claude Code!」という見出しを追加して
```

**詳細**: [00_quickstart.md](../04_notes/00_quickstart.md)

---

## 基本機能

Claude Codeには4つの中核機能があります。

### 1. 機能開発

自然言語で指示するだけで、計画・実装・検証まで自動化。

```bash
claude "JWT認証機能を追加して。ログイン、トークン検証、リフレッシュトークンを含めて"
```

### 2. デバッグ支援

エラーメッセージから問題を特定し、修正案を提示・実装。

```bash
claude "このエラーを修正して: [エラーメッセージ]"
```

### 3. コードベース探索

プロジェクト全体を理解し、アーキテクチャやデータフローを説明。

```bash
claude "認証の仕組みを説明して"
```

### 4. 自動化

リント修正、マージ競合解決、リリースノート作成などの定型業務を自動化。

```bash
claude "全てのlintエラーを修正してコミットして"
```

### 高度な機能

#### MCP（Model Context Protocol）

外部サービスと連携：
- GitHub - Issue/PRの管理
- Slack - メッセージ送受信
- Postgres - データベースクエリ
- Google Drive - ドキュメント操作

#### サブエージェント

専門的なAIチームを編成：
- コードレビューエージェント
- セキュリティ監査エージェント
- パフォーマンス最適化エージェント

#### スキル機能

カスタムワークフローをスキル化してチーム共有。

**詳細**: [02_research.md](../04_notes/02_research.md)

---

## プロジェクトメモリ

`.claude/CLAUDE.md`は、Claude Codeへの指示書です。

### なぜ重要か？

**Before（プロジェクトメモリなし）**:
```
ユーザー: "テストを書いて"
Claude Code: "どんなテストですか？"
ユーザー: "pytestで、カバレッジ80%以上で..."
（毎回説明が必要）
```

**After（プロジェクトメモリあり）**:
```
ユーザー: "テストを書いて"
Claude Code: [.claude/CLAUDE.mdを読む]
            → すぐに適切なテストを生成
```

### 何を書くべきか？

```markdown
# プロジェクト名

## 技術スタック
- 言語: Python 3.13
- フレームワーク: Django
- データベース: PostgreSQL

## コーディング規約
- PEP 8に準拠
- テストカバレッジ: 80%以上
- コメント: 日本語で記載

## Claude Codeへの指示
- Git操作前に必ず確認を取る
- テスト駆動開発を実践する
```

### 効果

- **オンボーディング時間**: 50-60%短縮
- **ルール説明の時間**: 80%削減
- **コード品質**: チーム全体で均一化

**詳細**: [03_project_memory.md](../04_notes/03_project_memory.md)

---

## ユースケース

### 職種別の活用例

#### 新人エンジニア

**課題**: コードベースが大きくて理解できない

**活用**:
```bash
claude "このプロジェクトの概要を説明して"
claude "認証フローを追跡して"
```

**効果**: オンボーディング時間を数週間→数日に短縮

#### シニアエンジニア

**課題**: コードレビューに時間がかかる

**活用**:
```bash
claude "全ての変更をレビューしてサマリーを作成して"
```

**効果**: レビュー時間を50%削減

#### フロントエンドエンジニア

**課題**: UIコンポーネントの作成に時間がかかる

**活用**:
```bash
claude "再利用可能なモーダルコンポーネントをReactとTypeScriptで作成して"
```

**効果**: ボイラープレートコードの自動生成

#### バックエンドエンジニア

**課題**: API設計とドキュメント作成が面倒

**活用**:
```bash
claude "ユーザー管理のRESTful APIを設計・実装して"
claude "OpenAPI仕様書を生成して"
```

**効果**: API設計のベストプラクティス適用

### シチュエーション別の活用例

#### レガシーコードのリファクタリング

```bash
# 1. 現状分析
claude "analyze @legacy/payment-service.js and identify technical debt"

# 2. テスト追加
claude "add characterization tests for the existing behavior"

# 3. リファクタリング
claude "extract payment validation into separate functions"
```

#### 緊急のバグ修正

```bash
# 1. 問題特定
claude "analyze this error log and identify the root cause"

# 2. 修正実装
claude "create a hotfix for the payment processing bug"

# 3. 再発防止
claude "add tests to prevent this bug from recurring"
```

#### 書籍・チュートリアルのリポジトリから学習コンテンツを作成

**課題**: 技術書のコードを読んで理解し、自分で実装してみたい

**活用**:

```bash
# 1. リポジトリをクローン
git clone https://github.com/author/book-repository.git
cd book-repository

# 2. プロジェクトメモリを作成
mkdir -p .claude
cat > .claude/CLAUDE.md << 'EOF'
# 書籍リポジトリ学習プロジェクト

## 目的
このリポジトリのコードを理解し、自分で実装しながら学習する

## 学習方法
1. 参考コードを分析・理解
2. 自分でゼロから実装
3. テスト駆動開発で進める

## Claude Codeへの指示
- コードの説明は丁寧に
- 実装は段階的に
- テストを必ず含める
EOF

# 3. コードを分析
claude "第1章のコードを分析して、アーキテクチャを説明して"

# 4. 理解を深める
claude "このデザインパターンの利点と欠点を説明して"

# 5. 自分で実装
claude "この機能を参考にして、テスト駆動開発で実装して"

# 6. チュートリアルを生成
claude "この章の内容を初心者向けチュートリアルにまとめて"
```

**実践例**:

```bash
# 機械学習の書籍
claude "analyze @src/neural_network.py and explain the backpropagation algorithm"
claude "create a tutorial on implementing neural networks from scratch"

# Webアプリケーションの書籍
claude "explain the authentication flow in @src/auth/"
claude "implement a similar auth system with modern best practices"
```

**効果**:
- 書籍の理解度が大幅に向上
- 手を動かしながら学習できる
- 自分のペースで深掘りできる

### 期待される効果

| 項目 | 改善率 |
|------|--------|
| 新機能開発 | 30-50%時間短縮 |
| バグ修正 | 40-60%時間短縮 |
| コードレビュー | 50%時間短縮 |
| テストカバレッジ | 20-30%向上 |
| オンボーディング | 50-60%短縮 |

**詳細**: [04_use_cases.md](../04_notes/04_use_cases.md)

---

## FAQ

### 基本的な質問

#### Q: Claude Codeは無料ですか？

**A**: いいえ、有料です。Claude Pro（約2万円/月）、Team（約3万円/月/人）、Enterpriseプランがあります。

#### Q: どのプログラミング言語に対応していますか？

**A**: 主要な言語はすべて対応（Python, JavaScript, TypeScript, Go, Rust, Java, C++, Ruby, PHP等）

#### Q: オフラインでも使えますか？

**A**: いいえ、インターネット接続が必須です。

### 使い方の質問

#### Q: プロンプトをどう書けばいいですか？

**A**: 明確で具体的に書くのがコツです。

❌ 悪い例: 「コードを直して」

✅ 良い例: 「src/auth.jsのログイン処理で、パスワードが間違っている時のエラーメッセージを「パスワードが正しくありません」に変更して」

#### Q: Claude Codeが理解してくれません

**A**: 以下を試してください。

1. **プロジェクトメモリを設定** - `.claude/CLAUDE.md`
2. **ファイルを明示的に参照** - `@src/models/user.py`
3. **コンテキストを与える** - エラーメッセージやログを共有

### トラブルシューティング

#### T: 「Too many tokens」エラー

**対策**:
- ファイルを明示的に指定
- タスクを分割
- プロジェクトメモリを活用

#### T: Git操作がうまくいかない

**対策**:
- コミットメッセージを明示的に指定
- コンフリクトを手動で解決
- ブランチを確認

**詳細**: [05_faq.md](../04_notes/05_faq.md)

---

## まとめ

### Claude Codeで何ができる？

| 分野 | できること |
|------|----------|
| 開発 | コード生成、リファクタリング、テスト作成 |
| デバッグ | エラー解析、修正提案、再発防止策 |
| レビュー | コード品質チェック、セキュリティ監査 |
| 自動化 | Git操作、リント修正、ドキュメント生成 |
| 学習 | コードベース理解、アーキテクチャ説明 |

### 導入のメリット

#### 定量的効果

- 開発速度: 30-60%向上
- コード品質: 20-50%改善
- オンボーディング: 50-60%短縮

#### 定性的効果

- 定型作業からの解放
- クリエイティブな作業に集中
- チーム全体のスキル向上

### 今日から始める5ステップ

1. **インストール** - 5分で完了
   ```bash
   curl -fsSL https://claude.ai/install.sh | bash
   ```

2. **試してみる** - 簡単な質問から
   ```bash
   claude "このプロジェクトを説明して"
   ```

3. **プロジェクトメモリを作る**
   ```bash
   mkdir -p .claude
   echo "# My Project" > .claude/CLAUDE.md
   ```

4. **チームで共有**
   ```bash
   git add .claude/
   git commit -m "Add project memory"
   git push
   ```

5. **継続的に改善** - 使いながらルールを充実

### 次のステップ

#### 初心者向け

1. [クイックスタートガイド](../04_notes/00_quickstart.md)を読む
2. 簡単なタスクから試す
3. プロジェクトメモリを設定

#### 中級者向け

1. [基本機能](../04_notes/02_research.md)を深く学ぶ
2. MCPで外部サービスと連携
3. サブエージェントを活用

#### チーム導入

1. パイロット導入（2-3人で試用）
2. プロジェクトメモリを共有
3. チーム全体に展開

---

## 📚 関連ドキュメント

### このリポジトリのガイド

- **[00_quickstart.md](../04_notes/00_quickstart.md)** - 5分で始めるクイックスタート
- **[02_research.md](../04_notes/02_research.md)** - 基本機能の詳細解説
- **[03_project_memory.md](../04_notes/03_project_memory.md)** - プロジェクトメモリ完全ガイド
- **[04_use_cases.md](../04_notes/04_use_cases.md)** - 職種別・シチュエーション別の活用例
- **[05_faq.md](../04_notes/05_faq.md)** - よくある質問とトラブルシューティング

### 公式リソース

- **公式ドキュメント**: https://docs.claude.com/en/docs/claude-code/overview
- **GitHub**: https://github.com/anthropics/claude-code
- **コミュニティ**: https://discord.gg/anthropic

---

## 🎯 最後に

Claude Codeは単なる「コード補完ツール」ではありません。

プロジェクト全体を理解し、あなたのルールに従って、ファイル編集からGit操作まで自動化する**AIペアプログラマー**です。

特に**プロジェクトメモリ**（`.claude/CLAUDE.md`）を活用することで、チーム全体で統一されたワークフローを実現できます。

**今日から始めて、開発生産性を2倍にしましょう。**

---

**作成日**: 2025-11-05
**更新日**: 2025-11-05
**対象**: Claude Codeを初めて使う全ての開発者
