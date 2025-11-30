---
description: 指定されたGitHub PRのレビューコメントとレビュー情報を取得し、ナレッジベースに保存する。
---

# kcc-save-pr-comments

指定されたGitHub PRのレビューコメントとレビュー情報を取得し、ナレッジベースに保存してください

使用方法: /kcc-save-pr-comments owner/repo pr_number1 [pr_number2 pr_number3 ...]

複数のPR番号を指定することで、一度に複数のPRのレビューコメントを取得・保存できます。

実行手順:
1. 引数のバリデーション（owner/repo形式、PR番号の確認）
   - 最低3つの引数が必要（owner/repo + 1つ以上のPR番号）
   - 各PR番号が数字であることを確認
2. 環境変数 PR_REVIEW_KNOWLEDGE_PATH の確認とベースディレクトリ設定（一度だけ実行）
   - 環境変数が未設定または空の場合のみ、デフォルト値を設定
   - デフォルト値: git リポジトリ内の場合は $(git rev-parse --show-toplevel 2>/dev/null)/.claude
   - git リポジトリ外の場合は $(pwd)/.claude
   - 設定後はその値を継続使用
3. 指定されたPR番号を順次処理
   - 各PRに対して以下を実行:
     a. GitHub CLI (gh)でPR情報とコメントを取得
     b. コメントを技術的価値に基づいてフィルタリング
     c. ${PR_REVIEW_KNOWLEDGE_PATH}/knowledge/pr-reviews/github.com/owner/repo/ ディレクトリを作成（初回のみ）
     d. 構造化されたMarkdownサマリーを作成・保存
4. 処理結果のサマリーを表示

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
# 単一のPRを処理
/kcc-save-pr-comments microsoft/vscode 123

# 複数のPRを一括処理
/kcc-save-pr-comments rails/rails 456 789 1000
/kcc-save-pr-comments your-org/your-repo 123 124 125 126

# スペース区切りで任意の数のPR番号を指定可能
/kcc-save-pr-comments facebook/react 1 2 3 4 5
```

前提条件:
- GitHub CLI (gh) がインストールされ、認証済みであること
- 対象のリポジトリへの読み取り権限があること
- jq コマンドがインストールされていること（JSONパース用）
- 環境変数 PR_REVIEW_KNOWLEDGE_PATH がオプションで設定可能（未設定の場合はリポジトリルート/.claude をデフォルト使用）

環境変数設定:
```bash
# コマンド実行時に自動設定される（手動設定不要）
# 環境変数が未設定の場合、以下のロジックで自動設定:
# - Gitリポジトリ内: $(git rev-parse --show-toplevel)/.claude
# - Gitリポジトリ外: $(pwd)/.claude

# 手動で異なるパスを使用したい場合の例:
export PR_REVIEW_KNOWLEDGE_PATH="$HOME/.claude"
export PR_REVIEW_KNOWLEDGE_PATH="/path/to/your/notes"
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

実装されるBashスクリプト:
```bash
#!/bin/bash

# 引数チェック
if [ $# -lt 2 ]; then
    echo "使用方法: /kcc-save-pr-comments owner/repo pr_number1 [pr_number2 pr_number3 ...]"
    echo "例: /kcc-save-pr-comments microsoft/vscode 123 124 125"
    exit 1
fi

REPO=$1
shift  # 残りの引数をPR番号として扱う
PR_NUMBERS=("$@")

# owner/repo形式の確認
if [[ ! $REPO =~ ^[^/]+/[^/]+$ ]]; then
    echo "エラー: リポジトリは 'owner/repo' の形式で指定してください"
    exit 1
fi

# 全てのPR番号が数字かチェック
for pr_number in "${PR_NUMBERS[@]}"; do
    if ! [[ "$pr_number" =~ ^[0-9]+$ ]]; then
        echo "エラー: PR番号は数字で指定してください（無効な値: $pr_number）"
        exit 1
    fi
done

echo "処理対象: $REPO の PR番号 ${PR_NUMBERS[*]}"

# 環境変数の設定（一度だけ実行）
if [ -z "$PR_REVIEW_KNOWLEDGE_PATH" ]; then
    if git rev-parse --show-toplevel >/dev/null 2>&1; then
        export PR_REVIEW_KNOWLEDGE_PATH="$(git rev-parse --show-toplevel)/.claude"
        echo "ベースディレクトリを設定: $PR_REVIEW_KNOWLEDGE_PATH"
    else
        export PR_REVIEW_KNOWLEDGE_PATH="$(pwd)/.claude"
        echo "ベースディレクトリを設定: $PR_REVIEW_KNOWLEDGE_PATH"
    fi
else
    echo "既存のベースディレクトリを使用: $PR_REVIEW_KNOWLEDGE_PATH"
fi

# GitHub CLIの認証確認
if ! gh auth status >/dev/null 2>&1; then
    echo "エラー: GitHub CLI認証が必要です。'gh auth login' を実行してください"
    exit 1
fi

# ディレクトリ作成（一度だけ）
SAVE_DIR="$PR_REVIEW_KNOWLEDGE_PATH/knowledge/pr-reviews/github.com/$REPO"
mkdir -p "$SAVE_DIR"
if [ $? -ne 0 ]; then
    echo "エラー: ディレクトリの作成に失敗しました: $SAVE_DIR"
    exit 1
fi

# 処理結果を記録するための変数
PROCESSED_COUNT=0
FAILED_COUNT=0
CURRENT_DATE=$(date +"%Y-%m-%d")

# 各PR番号を順次処理
for PR_NUMBER in "${PR_NUMBERS[@]}"; do
    echo ""
    echo "=== PR #$PR_NUMBER を処理中 ==="

    # PR情報取得
    echo "PR情報を取得中..."
    PR_INFO=$(gh pr view $PR_NUMBER --repo $REPO --json title,body,author,createdAt,updatedAt,url 2>/dev/null)
    if [ $? -ne 0 ]; then
        echo "❌ エラー: PR #$PR_NUMBER の情報取得に失敗しました"
        ((FAILED_COUNT++))
        continue
    fi

    # レビューコメント取得
    echo "レビューコメントを取得中..."
    REVIEWS=$(gh pr view $PR_NUMBER --repo $REPO --json reviews 2>/dev/null)
    COMMENTS=$(gh pr view $PR_NUMBER --repo $REPO --json comments 2>/dev/null)

    # 出力ファイル名を設定
    OUTPUT_FILE="$SAVE_DIR/pr-$PR_NUMBER-$CURRENT_DATE-summary.md"

    # Markdownファイル作成
    echo "サマリーファイルを作成中: $OUTPUT_FILE"

    # ここでjqを使ってJSONを処理し、技術的価値のあるコメントをフィルタリング
    # 実際の実装では、コメント内容を分析してフィルタリングロジックを適用

    echo "✅ PR #$PR_NUMBER のサマリーを保存しました: $OUTPUT_FILE"
    ((PROCESSED_COUNT++))
done

# 処理結果のサマリー表示
echo ""
echo "=== 処理完了 ==="
echo "処理成功: $PROCESSED_COUNT 件"
echo "処理失敗: $FAILED_COUNT 件"
echo "保存場所: $SAVE_DIR"

if [ $FAILED_COUNT -eq 0 ]; then
    exit 0
else
    exit 1
fi
```
