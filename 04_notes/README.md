# Claude Code ガイドノート集

このディレクトリには、Claude Codeに関する8つの詳細ガイドが含まれています。
自分の目的や役割に合わせて、必要なノートをお読みください。

---

## 📋 ノート一覧

| ノート | 内容 | 読了時間 | 対象者 |
|--------|------|----------|--------|
| **[00_quickstart.md](./00_quickstart.md)** | 5分で始める基本の使い方 | 5分 | 全員（必読） |
| **[01_tools_comparison.md](./01_tools_comparison.md)** | 4大AIツール徹底比較 | 15分 | ツール選定担当者 |
| **[02_research.md](./02_research.md)** | 基本機能の詳細 | 20分 | 深く理解したい人 |
| **[03_project_memory.md](./03_project_memory.md)** | プロジェクトメモリガイド | 15分 | チームリーダー |
| **[04_use_cases.md](./04_use_cases.md)** | 職種別ユースケース集 | 20分 | 実践的な活用を知りたい人 |
| **[05_faq.md](./05_faq.md)** | よくある質問とトラブル対処 | 10分 | 困った時に参照 |
| **[06_agents_skills_mcp.md](./06_agents_skills_mcp.md)** | エージェント・スキル・MCP完全ガイド | 30分 | 上級者 |
| **[07_github_actions_worktree.md](./07_github_actions_worktree.md)** | CI/CD自動化と並列開発 | 30分 | DevOps・チーム開発 |

---

## 🎯 読む順番ガイド

### 初めてClaude Codeに触れる方

```
1. 00_quickstart.md        ← まずはここから！
   ↓
2. 05_faq.md (Q1-Q5)      ← 基本的な疑問を解消
   ↓
3. 04_use_cases.md         ← 自分の職種のセクションを読む
```

**所要時間**: 約20分

---

### ツール選定を担当している方

```
1. 01_tools_comparison.md  ← Claude Code vs 他ツールの比較
   ↓
2. 00_quickstart.md        ← 実際の使い方を確認
   ↓
3. 04_use_cases.md         ← 導入効果を確認
```

**所要時間**: 約40分

---

### 既にClaude Codeを使っている方

```
1. 03_project_memory.md    ← チームで活用する方法
   ↓
2. 06_agents_skills_mcp.md ← 高度な機能を習得
   ↓
3. 07_github_actions_worktree.md ← ワークフロー自動化
```

**所要時間**: 約75分

---

### 困った時・トラブルが起きた時

```
→ 05_faq.md を参照
```

目次から該当する質問を探すか、Ctrl+F（Cmd+F）でキーワード検索してください。

---

## 📚 各ノートの詳細

### 00_quickstart.md - クイックスタートガイド

**こんな人におすすめ**:
- Claude Codeを初めて使う
- とりあえず試してみたい
- 5分で基本を理解したい

**内容**:
- インストール方法
- 基本的な使い方
- 最初の一歩

---

### 01_tools_comparison.md - AIツール徹底比較

**こんな人におすすめ**:
- どのツールを選べばいいか迷っている
- Claude Codeと他ツールの違いを知りたい
- 料金や機能を比較したい

**内容**:
- Claude Code vs GitHub Copilot CLI vs Gemini CLI vs Cursor
- 料金、機能、技術仕様の詳細比較
- シチュエーション別おすすめツール
- OpenAI Codexの歴史と現状

---

### 02_research.md - 基本機能の詳細

**こんな人におすすめ**:
- Claude Codeの機能を深く理解したい
- 中核機能と高度な機能を知りたい
- 技術仕様を確認したい

**内容**:
- 4つの中核機能（コンテキスト、ファイル操作、コマンド実行、会話UI）
- 高度な機能（プロジェクトメモリ、サブエージェント、MCP統合）
- 技術的な詳細と制限事項

---

### 03_project_memory.md - プロジェクトメモリガイド

**こんな人におすすめ**:
- チームでClaude Codeを使いたい
- プロジェクト固有のルールを設定したい
- `.claude/CLAUDE.md`の活用方法を知りたい

**内容**:
- プロジェクトメモリとは
- `.claude/CLAUDE.md`の書き方
- チーム活用のベストプラクティス
- 実践例（Python、React、Rust）

---

### 04_use_cases.md - 職種別ユースケース集

**こんな人におすすめ**:
- 自分の職種でどう使えるか知りたい
- 実践的な活用例を見たい
- 具体的なコマンド例が欲しい

**内容**:
- 対象者別（新人、シニア、フロントエンド、バックエンド、DevOps、QA）
- シチュエーション別（リファクタリング、バグ修正、新技術導入など）
- チーム・組織レベルの活用

---

### 05_faq.md - よくある質問とトラブルシューティング

**こんな人におすすめ**:
- 困った時の解決策を探している
- エラーが出て動かない
- ベストプラクティスを知りたい

**内容**:
- 基本的な質問（料金、対応言語、他ツールとの違い）
- インストールとセットアップ
- トラブルシューティング（応答しない、エラー対処）
- ベストプラクティス

---

### 06_agents_skills_mcp.md - エージェント・スキル・MCP完全ガイド

**こんな人におすすめ**:
- Claude Codeを使いこなしたい上級者
- エージェント、スキル、MCPの違いを理解したい
- 拡張機能を活用したい

**内容**:
- エージェント（サブエージェント）の作成と活用
- スキル（個人、プロジェクト、プラグイン）の使い方
- MCP（Model Context Protocol）統合
- 使い分けガイドと実践例

---

### 07_github_actions_worktree.md - GitHub Actions & Git Worktree完全ガイド

**こんな人におすすめ**:
- CI/CD自動化に興味がある
- 並列開発のワークフローを構築したい
- GitHub Actionsと統合したい

**内容**:
- GitHub Actions統合（@claudeメンション、自動PRレビュー）
- Git Worktreeで複数ブランチを並列開発
- 統合ワークフロー（Worktree + Actions）
- 自動化スクリプト例

---

## 🚀 こんな時はどのノートを読む？

### ケース1: 「Claude Codeって何？」

→ **[00_quickstart.md](./00_quickstart.md)** を読んで、まずは触ってみましょう

---

### ケース2: 「Cursorと迷っている」

→ **[01_tools_comparison.md](./01_tools_comparison.md)** で詳細比較を確認

---

### ケース3: 「チームで使いたい」

→ **[03_project_memory.md](./03_project_memory.md)** でチーム活用方法を学習

---

### ケース4: 「自分の仕事でどう使える？」

→ **[04_use_cases.md](./04_use_cases.md)** で職種別の活用例を確認

---

### ケース5: 「エラーが出て動かない」

→ **[05_faq.md](./05_faq.md)** のトラブルシューティングを参照

---

### ケース6: 「もっと高度な使い方を知りたい」

→ **[06_agents_skills_mcp.md](./06_agents_skills_mcp.md)** で上級機能を習得

---

### ケース7: 「開発フローを自動化したい」

→ **[07_github_actions_worktree.md](./07_github_actions_worktree.md)** でCI/CD統合を学習

---

## 💡 おすすめの学習パス

### パス1: 最短ルート（30分）

最小限の時間で基本を理解したい方向け

```
00_quickstart.md (5分)
↓
05_faq.md の Q1-Q5 (5分)
↓
04_use_cases.md の自分の職種セクション (20分)
```

---

### パス2: 標準ルート（90分）

Claude Codeをしっかり理解したい方向け

```
00_quickstart.md (5分)
↓
01_tools_comparison.md (15分)
↓
02_research.md (20分)
↓
03_project_memory.md (15分)
↓
04_use_cases.md (20分)
↓
05_faq.md (15分)
```

---

### パス3: 完全マスタールート（150分）

全機能を使いこなしたい方向け

```
標準ルート (90分)
↓
06_agents_skills_mcp.md (30分)
↓
07_github_actions_worktree.md (30分)
```

---

## 🔗 関連リンク

- **プロジェクトルートのREADME**: [../README.md](../README.md)
- **進捗記録**: [../05_progress/learning_log.md](../05_progress/learning_log.md)
- **完全ガイド（統合版）**: [../06_docs/complete_guide.md](../06_docs/complete_guide.md)
- **公式ドキュメント**: https://docs.claude.com/en/docs/claude-code/overview

---

## ⚠️ 重要な注意事項

このガイドは **AI（Claude Code）によって作成** されています。

- 内容に誤りや古い情報が含まれている可能性があります
- 重要な情報は必ず公式ドキュメントで確認してください
- 最新情報: https://docs.claude.com/en/docs/claude-code/overview

---

**作成日**: 2025-11-05
**最終更新**: 2025-11-05
