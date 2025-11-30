---
description: 指定されたGitHub PRのレビューコメントとレビュー情報を取得し、ナレッジベースに保存する。
---

# kcc-save-pr-comments

指定されたGitHub PRのレビューコメントとレビュー情報を取得し、ナレッジベースに保存してください

使用方法: /kcc-save-pr-comments owner/repo pr_number

実行手順:
1. 引数のバリデーション（owner/repo形式、PR番号の確認）
2. 環境変数 PR_REVIEW_KNOWLEDGE_PATH の確認とベースディレクトリ設定
   - 未設定の場合: リポジトリルート/.claude（git rev-parse --show-toplevel）/.claude をデフォルトとして使用
   - Gitリポジトリ外の場合: 現在のディレクトリ/.claude（pwd/.claude）をデフォルトとして使用
3. GitHub CLI (gh)でPR情報とコメントを取得
4. コメントを技術的価値に基づいてフィルタリング
5. ${PR_REVIEW_KNOWLEDGE_PATH}/knowledge/pr-reviews/github.com/owner/repo/ ディレクトリを作成
6. 構造化されたMarkdownサマリーを作成・保存

GitHub CLI コマンド:
```bash
# PR基本情報を取得
gh pr view $PR_NUMBER --repo $OWNER/$REPO --json title,body,author,createdAt,updatedAt,url

# レビューコメントを取得
gh pr view $PR_NUMBER --repo $OWNER/$REPO --json reviews

# 一般的なコメントを取得
gh pr view $PR_NUMBER --repo $OWNER/$REPO --json comments
```

コメントフィルタリング基準:
【含める】
- コード改善提案（パフォーマンス、可読性、保守性）
- アーキテクチャや設計に関する議論
- セキュリティや脆弱性の指摘
- テスト戦略やエラーハンドリングの提案
- 運用・監視・デプロイメントに関する考慮事項
- 技術的負債や将来への影響に関する議論

【除外する】
- 挨拶、感謝、雑談のみのコメント
- 単純なLGTM、approve、リアクションのみ
- タイポ修正やフォーマット指摘のみ
- CI/CDの状態報告や統計情報

出力Markdown構造:
```markdown
# PR #{番号}: {タイトル}

## 基本情報
- **リポジトリ**: owner/repo
- **作成者**: @username
- **作成日時**: YYYY-MM-DD
- **URL**: [GitHub PR URL]

## 主要な技術的議論

### {カテゴリ（例：アーキテクチャ、パフォーマンス、セキュリティ）}
- **指摘内容**:
- **提案された解決策**:
- **結論・採用された方針**:

## 学習ポイント
### 他のPRでも応用できる知見
1. {一般化された知見1}
2. {一般化された知見2}

### 今後注意すべき点
1. {注意点1}
2. {注意点2}
```

保存ファイル名: pr-{番号}-{YYYY-MM-DD}-summary.md

必要なツール:
- gh コマンド（GitHub CLI）- 認証済みであること
- jq コマンド（JSONパース用）

エラーハンドリング:
- GitHub CLI認証の確認
- リポジトリ存在確認
- PR番号の妥当性確認
- 環境変数 PR_REVIEW_KNOWLEDGE_PATH の存在確認
- ディレクトリ作成権限の確認
- ネットワークエラーやAPI制限の処理

使用例:
```bash
/kcc-save-pr-comments microsoft/vscode 123
/kcc-save-pr-comments rails/rails 456
/kcc-save-pr-comments your-org/your-repo 789
```

前提条件:
- GitHub CLI (gh) がインストールされ、認証済みであること
- 対象のリポジトリへの読み取り権限があること
- jq コマンドがインストールされていること（JSONパース用）
- 環境変数 PR_REVIEW_KNOWLEDGE_PATH がオプションで設定可能（未設定の場合はリポジトリルート/.claude をデフォルト使用）

環境変数設定:
```bash
# デフォルト: リポジトリルート/.claude配下に設定（推奨）
export PR_REVIEW_KNOWLEDGE_PATH="$(git rev-parse --show-toplevel 2>/dev/null || pwd)/.claude"

# 例: ホームディレクトリ配下に設定
export PR_REVIEW_KNOWLEDGE_PATH="$HOME/.claude"

# 例: 専用ディレクトリに設定
export PR_REVIEW_KNOWLEDGE_PATH="/path/to/your/notes"

# 例: プロジェクトディレクトリ配下に設定
export PR_REVIEW_KNOWLEDGE_PATH="$HOME/workspace/pr-knowledge"
```

ディレクトリ構造:
```
${PR_REVIEW_KNOWLEDGE_PATH}/
└── knowledge/
    └── pr-reviews/
        └── github.com/
            └── owner/
                └── repo/
                    ├── pr-1-2025-11-08-summary.md
                    ├── pr-2-2025-11-09-summary.md
                    └── ...
```

インストール確認:
```bash
# GitHub CLI の確認
gh --version && gh auth status

# jq の確認
jq --version

# 環境変数の確認
echo "PR_REVIEW_KNOWLEDGE_PATH: $PR_REVIEW_KNOWLEDGE_PATH"

# ディレクトリの作成テスト
mkdir -p "$PR_REVIEW_KNOWLEDGE_PATH/knowledge/pr-reviews/github.com/test/repo"
ls -la "$PR_REVIEW_KNOWLEDGE_PATH/knowledge/pr-reviews/github.com/"
```

動作確認手順:
1. 小さなパブリックリポジトリのPRで動作テストを行う
2. 出力されるMarkdownファイルの内容を確認
3. エラーハンドリングが適切に動作することを確認
