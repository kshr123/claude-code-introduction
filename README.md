# Claude Code 社内紹介プロジェクト

## 📁 プロジェクト構造

```
.
├── 01_reference/           # 参考資料・公式ドキュメント
├── 02_templates/           # テンプレートファイル
│   ├── SPECIFICATION.template.md
│   ├── pyproject.toml.template
│   └── test_*.template.py
├── 03_my_implementations/  # デモ実装・サンプルコード
├── 04_notes/               # 調査ノート・メモ
├── 05_progress/            # 進捗記録
│   └── learning_log.md     # タスク進捗ログ
└── 06_docs/                # 社内紹介資料
```

## 🎯 プロジェクトの目的

社内向けにClaude Codeを紹介するための資料を作成し、以下を達成する：

1. **Claude Codeとは何か**を明確に説明
2. **実際のユースケース**を具体的に示す
3. **導入メリット**を定量的・定性的に示す
4. **実践的なデモ**を通じて理解を深める

### 開発方法論

- **仕様駆動開発（SDD）**: 資料構成とコンテンツを事前に設計
- **段階的作成**: 小さなステップで確実に進める
- **フィードバック重視**: 実際の利用者の声を反映

詳細は [.claude/CLAUDE.md](./.claude/CLAUDE.md) を参照してください。

## 📚 作成する資料コンテンツ

1. **イントロダクション** - Claude Codeとは何か
2. **主要機能** - コア機能の説明
3. **ユースケース** - 具体的な活用シーン
4. **デモンストレーション** - 実際の使用例
5. **導入ガイド** - セットアップ手順
6. **ベストプラクティス** - 効果的な使い方
7. **FAQ** - よくある質問と回答
8. **まとめ** - 導入メリットと次のステップ

## 🔧 開発環境

- **Python**: 3.13以上（デモ用）
- **パッケージマネージャー**: uv
- **プレゼンテーションツール**: Markdown/Marp/Reveal.js（検討中）

### セットアップ

#### uvのインストール

```bash
# macOS/Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# または Homebrew
brew install uv
```

#### Python 3.13のインストール

```bash
# uvでPython 3.13をインストール
uv python install 3.13
```

#### 参考資料の配置

```bash
# 01_reference/に公式ドキュメントやリソースを配置
mkdir -p 01_reference
cd 01_reference
# Claude Code公式ドキュメントやサンプルをダウンロード
```

## 🚀 使い方

### 資料作成フロー

1. **調査**: 既存のドキュメント、事例を収集
2. **構成設計**: 資料の構成とストーリーを設計
3. **コンテンツ作成**: 各セクションを作成
4. **デモ準備**: 実演用のサンプルコードを作成
5. **レビュー**: 内容の確認と改善
6. **プレゼン準備**: スライドやデモ環境の準備
7. **振り返り**: フィードバックの収集と改善

### テンプレートの活用

`02_templates/` フォルダに以下のテンプレートが用意されています：

- `SPECIFICATION.template.md` - 資料仕様のテンプレート
- `pyproject.toml.template` - デモプロジェクト設定のテンプレート
- テストテンプレート - デモコードのテスト用

### 進捗を記録する

`05_progress/learning_log.md` にタスクの進捗を記録

### 調査ノートを取る

`04_notes/` 配下に調査内容やアイデアを記録

### 資料を作成する

`06_docs/` 配下に最終的な社内紹介資料を配置

## 📚 重要なドキュメント

- **[.claude/CLAUDE.md](./.claude/CLAUDE.md)** - プロジェクトルールとベストプラクティス（必読）
- **[05_progress/learning_log.md](./05_progress/learning_log.md)** - タスク進捗と詳細記録

## 📝 プロジェクト情報

- **開始日**: 2025-11-05
- **進捗状況**: [05_progress/learning_log.md](./05_progress/learning_log.md) を参照
- **完了タスク数**: 3 / 8 タスク
- **完了タスク**:
  - ✅ Claude Code公式ドキュメント調査
  - ✅ ユースケース収集と整理
  - ✅ プレゼンテーション構成設計

## 📖 参考

- **Claude Code公式ドキュメント**: https://docs.anthropic.com/claude/docs
- **Claude Code GitHub**: https://github.com/anthropics/claude-code

---

**License**: MIT
