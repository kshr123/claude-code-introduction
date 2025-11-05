# AI コーディングツール徹底比較

**作成日**: 2025-11-05
**最終更新**: 2025-11-05

Claude Code、GitHub Copilot CLI、Gemini CLI、Cursorの4つの主要AIコーディングツールを徹底比較します。

また、OpenAI Codexの歴史と現状についても解説します。

---

## ⚠️ OpenAI Codexについて

**OpenAI Codex**は、AIコーディングツールの歴史において重要な役割を果たしましたが、現在は2つの異なる時代に分かれています。

### 旧Codex（2021-2023）: コード補完モデル

- **発表**: 2021年8月10日
- **基盤**: GPT-3ベース（159GBのPythonコードで追加学習）
- **用途**: コード補完、GitHub Copilotの初期モデル
- **精度**: 37%（1回試行）、70.2%（100回試行）
- **廃止**: 2023年3月23日（GPT-3.5 Turboへの移行を推奨）

**歴史的意義**:
- GitHub Copilotの基盤技術として、AIコーディングアシスタントの先駆け
- コード生成AIの商用化を実現
- 12以上のプログラミング言語に対応（特にPythonに強い）

### 新Codex（2025年〜）: 自律型ソフトウェアエンジニアリングエージェント

- **発表**: 2025年5月16日（リサーチプレビュー）
- **基盤**: OpenAI o3モデルの特化版（codex-1）
- **用途**: 完全自律型のソフトウェア開発エージェント
- **精度**: 75%（o3の最高性能版より5%向上）
- **ライセンス**: Apache 2.0（Codex CLI）

**主な機能**:
- 機能実装の完全自動化
- テスト実行
- PR（プルリクエスト）作成
- 一時的なLinuxコンテナで安全に実行

**現状**: リサーチプレビュー段階（2025年5月時点）

---

**このドキュメントの対象**: 本ガイドでは、**現在商用利用可能な4つのツール**（Claude Code、GitHub Copilot CLI、Gemini CLI、Cursor）を中心に比較します。新Codexは参考情報として記載しますが、リサーチプレビュー段階のため詳細な比較対象には含めていません。

---

## 📋 目次

1. [各ツールの概要](#各ツールの概要)
2. [詳細比較表](#詳細比較表)
3. [各ツールの特徴](#各ツールの特徴)
4. [選び方ガイド](#選び方ガイド)
5. [OpenAI Codexの詳細](#openai-codexの詳細)

---

## 各ツールの概要

### 1. Claude Code

**種類**: ターミナルベースのAIエージェント
**開発元**: Anthropic
**リリース**: 2025年2月
**モデル**: Claude Sonnet 4.5, Opus 4.1

**特徴**:
- ターミナルファーストの設計思想
- プロジェクトメモリ（`.claude/CLAUDE.md`）でチーム全体のルール共有
- 200Kトークンの大規模コンテキストウィンドウ
- エージェント型（自律的にタスクを実行）
- ファイル編集からGit操作まで完全自動化

**主な用途**:
- 大規模リファクタリング
- 複数ファイルにまたがる機能実装
- コードベース全体の理解と分析

---

### 2. GitHub Copilot CLI

**種類**: ターミナルベースのAIアシスタント
**開発元**: GitHub（Microsoft）
**リリース**: 2025年9月（パブリックプレビュー）
**モデル**: Claude Sonnet 4（デフォルト）、GPT-5 Codex（オプション）

**特徴**:
- GitHub生態系とのネイティブ統合
- Issue、PR、リポジトリ情報を自動で把握
- MCP対応（2025年7月〜）
- GitHub Copilot有料プランに含まれる
- macOS、Linux、Windows（WSL）対応

**主な用途**:
- GitHubベースの開発ワークフロー
- Issue/PRと連携したコーディング
- CI/CD統合

---

### 3. Gemini CLI

**種類**: オープンソースのターミナルAIエージェント
**開発元**: Google
**リリース**: 2025年6月25日
**モデル**: Gemini 2.5 Pro
**ライセンス**: Apache 2.0（オープンソース）

**特徴**:
- **完全無料**（個人開発者向け）
- 1,000リクエスト/日、60リクエスト/分の無料枠
- 1Mトークンのコンテキストウィンドウ（最大2M予定）
- FastMCPとの統合
- Google Search、YouTube、Drive連携

**主な用途**:
- コスト重視の開発
- 大規模コードベースの分析
- Google生態系との統合

---

### 4. Cursor

**種類**: IDEベースのAIコードエディタ
**開発元**: Cursor Team
**リリース**: 2023年〜（継続開発中）
**モデル**: GPT-4.1、Claude Opus 4、Gemini 2.5 Pro（複数対応）

**特徴**:
- VS Codeベースのエディタ
- IDEとしての完全な機能
- Agent（複雑なタスク）とTab（自動補完）の2モード
- Background Agentで並列実行
- 複数のAIモデルを切り替え可能

**主な用途**:
- IDE環境で完結したい開発者
- リアルタイムコード補完重視
- UI/UXデザインとの統合開発

---

## 詳細比較表

### 基本情報

| 項目 | Claude Code | GitHub Copilot CLI | Gemini CLI | Cursor |
|------|-------------|-------------------|-----------|--------|
| **種類** | CLI | CLI | CLI | IDE |
| **実行環境** | ターミナル | ターミナル | ターミナル | エディタ |
| **オープンソース** | ✗ | ✗ | ✓（Apache 2.0） | ✗ |
| **リリース時期** | 2025年2月 | 2025年9月 | 2025年6月 | 2023年〜 |

### 料金体系（2025年11月時点）

| プラン | Claude Code | GitHub Copilot CLI | Gemini CLI | Cursor |
|--------|-------------|-------------------|-----------|--------|
| **無料** | ✗ | ✗ | ✓（1,000req/日） | ✓（制限あり） |
| **個人プラン** | $20/月（Pro） | $10/月（Copilot Individual） | 無料 | $20/月（Pro） |
| **中価格帯** | $100/月（Max 5x） | $19/月（Copilot Business） | $40/月（Pay-as-you-go） | $60/月（Pro Plus） |
| **ハイエンド** | $200/月（Max 20x） | $39/月（Copilot Enterprise） | カスタム | $200/月（Ultra） |
| **チーム** | Pro以上 | $19/月/人（Business） | カスタム | $40/月/人 |

### 技術仕様

| 項目 | Claude Code | GitHub Copilot CLI | Gemini CLI | Cursor |
|------|-------------|-------------------|-----------|--------|
| **AIモデル** | Claude Sonnet 4.5, Opus 4.1 | Claude Sonnet 4, GPT-5 Codex | Gemini 2.5 Pro | GPT-4.1, Claude Opus 4, Gemini 2.5 Pro |
| **コンテキスト** | 200Kトークン | ファイルレベル | 1M〜2Mトークン | マルチファイル |
| **MCP対応** | ✓（ネイティブ） | ✓（2025年7月〜） | ✓（FastMCP） | ✓ |
| **プロジェクトメモリ** | ✓（`.claude/CLAUDE.md`） | ✗ | ✗ | ✗ |
| **カスタムコマンド** | ✓（スラッシュコマンド） | 限定的 | ✓ | ✓ |

### 機能比較

| 機能 | Claude Code | GitHub Copilot CLI | Gemini CLI | Cursor |
|------|-------------|-------------------|-----------|--------|
| **ファイル編集** | ✓（直接編集） | △（提案） | ✓（直接編集） | ✓（直接編集） |
| **複数ファイル編集** | ✓ | ✗ | ✓ | ✓ |
| **コマンド実行** | ✓（実行） | ✓（提案） | ✓（実行） | ✓（実行） |
| **Git操作** | ✓（フル対応） | △（限定的） | ✓ | ✓ |
| **コード補完** | ✗ | ✗ | ✗ | ✓（Tab機能） |
| **エージェント機能** | ✓ | ✓ | ✓ | ✓（Agent機能） |
| **サブエージェント** | ✓ | ✗ | ✗ | ✗ |
| **Background実行** | ✓ | ✗ | ✗ | ✓ |

### 外部連携

| 連携 | Claude Code | GitHub Copilot CLI | Gemini CLI | Cursor |
|------|-------------|-------------------|-----------|--------|
| **GitHub** | MCP経由 | ネイティブ統合 | MCP経由 | 統合あり |
| **Slack** | MCP経由 | ✗ | ✗ | 拡張機能 |
| **Postgres** | MCP経由 | MCP経由 | MCP経由 | MCP経由 |
| **Google Drive** | MCP経由 | MCP経由 | ネイティブ統合 | MCP経由 |
| **Google Search** | ✗ | ✗ | ネイティブ統合 | ✗ |
| **YouTube** | ✗ | ✗ | ネイティブ統合 | ✗ |

### 開発体験

| 項目 | Claude Code | GitHub Copilot CLI | Gemini CLI | Cursor |
|------|-------------|-------------------|-----------|--------|
| **学習曲線** | 中 | 低 | 低 | 低 |
| **セットアップ** | 簡単 | 簡単 | 非常に簡単 | 中 |
| **VS Code統合** | ✗ | 別ツール | ✗ | ネイティブ（フォーク） |
| **エディタ非依存** | ✓ | ✓ | ✓ | ✗ |
| **既存ワークフロー** | 統合しやすい | 統合しやすい | 統合しやすい | 移行必要 |

---

## 各ツールの特徴

### Claude Code の強み

**✅ 最適な場面**:
- 大規模リファクタリング
- プロジェクト全体の理解が必要
- チームでルールを統一したい
- ターミナルワークフローを崩したくない

**強み**:
1. **プロジェクトメモリ**
   - `.claude/CLAUDE.md`でチーム全体のルールを永続化
   - Gitで共有してワークフロー統一
   - 新メンバーのオンボーディング時間を50-60%削減

2. **200Kコンテキストウィンドウ**
   - プロジェクト全体を一度に把握
   - 複数ファイルにまたがる変更を正確に実行

3. **エージェント型アーキテクチャ**
   - 高レベルの指示から自律的に実行
   - テスト、Git操作まで自動化

**弱み**:
- コード補完機能なし（ターミナルベース）
- 無料プランなし
- VS Code拡張として使えない

---

### GitHub Copilot CLI の強み

**✅ 最適な場面**:
- GitHubベースの開発
- PR/Issueと連携したい
- GitHub生態系から離れたくない
- 既にCopilotを使っている

**強み**:
1. **GitHub生態系統合**
   - Issue、PR、リポジトリ情報を自動把握
   - Code Scanning、Actionsと連携
   - レビューからデプロイまでシームレス

2. **モデル選択**
   - Claude Sonnet 4（デフォルト）
   - GPT-5 Codex（エージェント向け最適化）
   - 用途に応じて切り替え

3. **エンタープライズ対応**
   - SSO、ガバナンス、コンプライアンス
   - 組織ポリシーの自動適用

**弱み**:
- 複数ファイル編集は苦手
- プロジェクトメモリなし
- GitHubに強く依存

---

### Gemini CLI の強み

**✅ 最適な場面**:
- コストを抑えたい
- 大規模コードベースを扱う
- Google生態系と統合したい
- オープンソースを好む

**強み**:
1. **完全無料（個人開発者）**
   - 1,000リクエスト/日の無料枠
   - APIキー不要（Googleログインのみ）
   - 予測可能なコスト

2. **1Mトークンコンテキスト**
   - 最大規模のコードベースも一度に処理
   - プロジェクト全体の分析が可能

3. **Google統合**
   - Google Search: 最新情報を自動取得
   - YouTube: 技術動画から学習
   - Drive: ドキュメント連携

4. **オープンソース**
   - Apache 2.0ライセンス
   - カスタマイズ自由
   - コミュニティ貢献可能

**弱み**:
- 歴史が浅い（2025年6月リリース）
- エンタープライズ機能は有料
- Geminiモデルのみ

---

### Cursor の強み

**✅ 最適な場面**:
- IDE環境で完結したい
- リアルタイムコード補完が必須
- 複数のAIモデルを使い分けたい
- UI/UXデザインも扱う

**強み**:
1. **VS CodeベースのIDE**
   - 既存の拡張機能がそのまま使える
   - 完全なIDE機能
   - デバッグ、テスト、Git統合

2. **Tab（自動補完）+ Agent**
   - Tab: リアルタイムのコード補完
   - Agent: 複雑なタスクの自動実行
   - Background Agent: 並列処理

3. **複数モデル対応**
   - GPT-4.1、Claude Opus 4、Gemini 2.5 Pro
   - タスクに応じて最適なモデルを選択
   - Max Modeですべてのトップモデル利用可能

4. **統合開発体験**
   - コード、デザイン、ドキュメントが一箇所
   - ターミナルとの切り替え不要

**弱み**:
- エディタロックイン（Cursor専用）
- 既存ワークフローの移行コスト
- 料金体系の変更が頻繁（2025年6月に大きな変更）

---

## 選び方ガイド

### シチュエーション別おすすめ

#### 1. 個人開発者（コスト重視）
**推奨**: Gemini CLI

**理由**:
- 完全無料（1,000req/日）
- 1Mトークンで大規模コードベースも対応
- オープンソースで安心

**代替案**: Claude Code Pro ($20/月) または GitHub Copilot Individual ($10/月)

---

#### 2. スタートアップ・小規模チーム
**推奨**: Claude Code Pro ($20/月)

**理由**:
- プロジェクトメモリでチームルール統一
- Gitで共有して標準化
- オンボーディング時間削減

**代替案**: Gemini CLI（無料） + プロジェクトドキュメント整備

---

#### 3. GitHub中心の開発チーム
**推奨**: GitHub Copilot Business ($19/月/人)

**理由**:
- Issue/PRとのネイティブ統合
- GitHub生態系からの離脱コストゼロ
- Code Scanning、Actions連携

**代替案**: Claude Code + GitHub MCP統合

---

#### 4. エンタープライズ（大規模組織）
**推奨**: GitHub Copilot Enterprise ($39/月/人) または Claude Code Max ($200/月)

**理由**:
- SSO、ガバナンス、コンプライアンス
- 専用サポート
- 組織ポリシー自動適用

**代替案**: 複数ツールの組み合わせ（用途別）

---

#### 5. IDE環境から離れたくない
**推奨**: Cursor Pro ($20/月) または Cursor Pro Plus ($60/月)

**理由**:
- VS Codeベースでそのまま使える
- リアルタイムコード補完
- 統合開発体験

**代替案**: VS Code + GitHub Copilot（IDE版）

---

#### 6. 大規模リファクタリング重視
**推奨**: Claude Code Max ($100-200/月)

**理由**:
- 200Kトークンで全体把握
- 複数ファイルにまたがる変更を正確に実行
- チェックポイント/ロールバック機能

**代替案**: Gemini CLI（1Mトークン、無料）

---

#### 7. Google生態系統合
**推奨**: Gemini CLI（無料）

**理由**:
- Google Search、YouTube、Driveとネイティブ統合
- 最新情報を自動取得
- ドキュメント連携がスムーズ

**代替案**: Claude Code + Google MCP統合

---

### タスク別おすすめ

| タスク | 第1候補 | 第2候補 | 第3候補 |
|--------|---------|---------|---------|
| **コード補完（リアルタイム）** | Cursor | - | - |
| **大規模リファクタリング** | Claude Code | Gemini CLI | Cursor |
| **コードベース理解** | Gemini CLI | Claude Code | Cursor |
| **Issue/PR連携開発** | GitHub Copilot CLI | Cursor | Claude Code |
| **チームルール統一** | Claude Code | - | - |
| **コスト最小化** | Gemini CLI | - | - |
| **複数モデル使い分け** | Cursor | - | - |
| **エンタープライズ統制** | GitHub Copilot Enterprise | Claude Code Max | - |

---

## まとめ

### 各ツールの位置づけ

```
【IDE統合型】
Cursor: IDE環境で完結、リアルタイム補完重視

【CLI型】
├─ Claude Code: プロジェクトメモリ、チーム標準化
├─ GitHub Copilot CLI: GitHub生態系統合
└─ Gemini CLI: 完全無料、大規模コンテキスト
```

### 選択のポイント

1. **ターミナル vs IDE**
   - ターミナルワークフロー → Claude Code / GitHub Copilot CLI / Gemini CLI
   - IDE環境重視 → Cursor

2. **コスト**
   - 無料重視 → Gemini CLI
   - コストパフォーマンス → Claude Code Pro ($20) / GitHub Copilot ($10-19)
   - 予算制約なし → Claude Code Max / Cursor Ultra

3. **チーム開発**
   - ルール統一重視 → Claude Code（プロジェクトメモリ）
   - GitHub中心 → GitHub Copilot CLI
   - コスト重視 → Gemini CLI

4. **コンテキストサイズ**
   - 超大規模 → Gemini CLI（1M-2Mトークン）
   - 大規模 → Claude Code（200K）
   - 中規模 → Cursor、GitHub Copilot CLI

### 複数ツールの併用も検討

多くの開発者は複数のツールを使い分けています：

```bash
# リアルタイム補完: Cursor
# 大規模リファクタリング: Claude Code
# 調査・学習: Gemini CLI（無料）
# PR作成・レビュー: GitHub Copilot CLI
```

---

## OpenAI Codexの詳細

### 旧Codex（2021-2023）の歴史

#### 開発の背景

OpenAI Codexは、GPT-3の自然言語処理能力をコード生成に特化させたモデルとして2021年8月に発表されました。

**学習データ**:
- 54百万のGitHubリポジトリ
- 159GB のPythonコード
- 12以上のプログラミング言語（Python, JavaScript, Go, Perl, PHP, Ruby, Swift, TypeScript等）

#### 技術的特徴

**性能**:
```
単発試行（n=1）: 37% の成功率
100回試行（n=100）: 70.2% の成功率
```

**対応言語の優先度**:
1. Python（最も強い）
2. JavaScript
3. TypeScript
4. その他の主要言語

#### 影響と遺産

**GitHub Copilot**:
- 2021年: Copilotの初期バージョンの基盤モデル
- Codex廃止後: 独自の進化を遂げ、Claude Sonnet 4やGPT-5 Codexなど複数モデルに対応

**AIコーディングツール市場の創出**:
- コード生成AIの商用化を実現
- 開発者向けAIアシスタントの標準を確立
- Claude Code、Cursor、Gemini CLIなどの後続ツールに影響

#### 廃止の理由

**2023年3月23日に廃止**:
1. GPT-3.5/GPT-4の登場により、Codexの相対的な性能が低下
2. より汎用的なモデルでコード生成が可能に
3. メンテナンスコストと性能のトレードオフ

**移行先**:
- GPT-3.5 Turbo（推奨）
- GPT-4（より高性能）

---

### 新Codex（2025年〜）の詳細

#### 発表とリリース

**タイムライン**:
- **2025年4月16日**: Codex CLI（Apache 2.0）をGitHubに公開
- **2025年5月16日**: 新Codexをリサーチプレビューとして発表
- **2025年9月23日**: GPT-5-Codexが開発者API経由で利用可能に

#### 技術アーキテクチャ

**基盤モデル**:
- **codex-1**: OpenAI o3の特化版
- コーディングタスクに最適化
- o3の最高性能版（ハードウェア集約型）より5%高い精度

**実行環境**:
```
Linux一時コンテナ
├── リポジトリクローン
├── コード生成
├── テスト実行
└── PR作成
```

**安全性**:
- サンドボックス化された環境
- 一時的なコンテナで実行後破棄
- ローカルシステムへの影響を最小化

#### 主な機能

**完全自律型開発**:
1. **コードベース理解**: プロジェクト全体を分析
2. **機能実装**: 要件から実装まで自動化
3. **テスト実行**: 自動テスト実施と結果確認
4. **PR作成**: コミットメッセージとPR説明を自動生成

**性能指標**:
- **精度**: 75%（OpenAI内部テスト）
- **対応タスク**: 機能実装、バグ修正、リファクタリング、テスト作成

#### 現在の提供形態

**利用可能なプラン**:
- ChatGPT Plus
- ChatGPT Pro
- ChatGPT Business
- ChatGPT Edu
- ChatGPT Enterprise

**API アクセス**:
- 開発者APIキー経由で利用可能（2025年9月〜）
- プログラマティックな統合が可能

#### 他ツールとの比較

| 項目 | 新Codex (2025) | Claude Code | GitHub Copilot CLI | Gemini CLI | Cursor |
|------|---------------|-------------|-------------------|-----------|--------|
| **リリース状態** | リサーチプレビュー | 商用 | 商用 | 商用 | 商用 |
| **基盤モデル** | o3特化版 | Claude Sonnet 4.5 | Claude/GPT-5 | Gemini 2.5 Pro | 複数モデル |
| **精度** | 75% | - | - | - | - |
| **実行環境** | Linuxコンテナ | ローカル | ローカル | ローカル | IDE |
| **自律性** | 完全自律 | 高 | 中 | 高 | 中 |
| **料金** | ChatGPT Plus〜 | $20〜/月 | $10〜/月 | 無料〜 | $20〜/月 |

---

### Codexの将来展望

#### 期待される進化

**短期（2025-2026）**:
- リサーチプレビューから正式リリースへ
- より多くの言語とフレームワークへの対応
- 精度のさらなる向上

**中期（2026-2027）**:
- 複雑なアーキテクチャ設計の自動化
- マルチリポジトリ対応
- チーム開発ワークフローへの統合

**長期ビジョン**:
- ソフトウェアエンジニアリングの完全自動化
- 要件定義からデプロイまでのエンドツーエンド対応
- 人間エンジニアとの協調作業の最適化

#### 課題と懸念

**技術的課題**:
- 複雑なビジネスロジックの理解
- レガシーコードとの統合
- セキュリティとプライバシーの保証

**倫理的懸念**:
- 雇用への影響
- コード品質の責任所在
- AIへの過度な依存

---

### まとめ：Codexの意義

**旧Codex（2021-2023）**:
- AIコーディングアシスタント市場を創出
- GitHub Copilotを通じてコード生成AIを普及
- 現在のAIコーディングツールの基礎を築いた

**新Codex（2025〜）**:
- コード補完から完全自律型エージェントへの進化
- ソフトウェア開発の新しいパラダイム
- AIとエンジニアの協働モデルの提示

**現在の選択肢**:
- **新Codexを試したい** → ChatGPT Plus契約後にリサーチプレビューに参加
- **商用環境で安定利用** → Claude Code、GitHub Copilot CLI、Gemini CLI、Cursorから選択
- **無料で試したい** → Gemini CLI（完全無料）

---

## 参考リンク

### 公式ドキュメント

- **Claude Code**: https://docs.claude.com/en/docs/claude-code/overview
- **GitHub Copilot CLI**: https://github.com/features/copilot/cli
- **Gemini CLI**: https://google-gemini.github.io/gemini-cli/
- **Cursor**: https://cursor.com/
- **OpenAI Codex**: https://openai.com/index/introducing-upgrades-to-codex/ (旧版)
- **Codex CLI (GitHub)**: https://github.com/openai/codex (新版)

### 料金ページ

- **Claude Code**: https://claude.com/pricing
- **GitHub Copilot**: https://github.com/features/copilot/plans
- **Gemini CLI**: https://ai.google.dev/gemini-api/docs/pricing
- **Cursor**: https://cursor.com/pricing

### 比較記事

- [GitHub Copilot CLI vs Claude Code (CometAPI)](https://www.cometapi.com/github-copilot-cli-vs-claude-code/)
- [Gemini CLI vs Claude Code (Composio)](https://composio.dev/blog/gemini-cli-vs-claude-code-the-better-coding-agent)
- [Cursor AI Pricing Guide (CometAPI)](https://www.cometapi.com/cursor-ai-pricing-2025-complete-guide-analysis/)
- [OpenAI Codex: Definition, History & Alternatives (Skywork AI)](https://skywork.ai/blog/openai-codex-definition/)
- [OpenAI Codex - Wikipedia](https://en.wikipedia.org/wiki/OpenAI_Codex)

---

**作成日**: 2025-11-05
**最終更新**: 2025-11-05
**対象**: 全ての開発者、チームリーダー、技術選定担当者
