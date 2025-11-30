---
description: 保存されているPRレビューの知識ファイルを解析し、統合されたサマリーを作成する。
---

# kcc-summarize-pr-knowledge

保存されているPRレビューの知識ファイルを解析し、統合されたサマリーを作成してください

使用方法: /kcc-summarize-pr-knowledge [repository_pattern]

実行手順:
1. 環境変数 PR_REVIEW_KNOWLEDGE_PATH の確認とベースディレクトリ設定（一度だけ実行）
    - 環境変数が未設定または空の場合のみ、デフォルト値を設定
    - デフォルト値: git リポジトリ内の場合は $(git rev-parse --show-toplevel 2>/dev/null)/.claude
    - git リポジトリ外の場合は $(pwd)/.claude
    - 設定後はその値を継続使用
2. repository_pattern指定時は対象を絞り込み、未指定時は全リポジトリを対象
3. **差分処理の実行（高速化）**
    - 既存サマリーファイルの最終更新日時を確認
    - それより新しいPRレビューファイルのみを処理対象として抽出
    - 既存サマリーがない場合は全ファイルを処理（初回実行）
4. pr-reviews ディレクトリ内の対象Markdownファイルを収集・解析
5. 技術的価値の高い知見を抽出・分類
6. repository_patternからファイル名を生成（*→all、/→_に変換）
7. **既存サマリーとの統合処理**
    - 既存サマリーがある場合：新規内容をマージして更新
    - 既存サマリーがない場合：新規作成
8. ${PR_REVIEW_KNOWLEDGE_PATH}/knowledge/pr-reviews/summary/[リポジトリパターン].md に統合サマリーを保存

引数:
- repository_pattern (オプション): 特定のリポジトリパターンで絞り込み
  - 例: "microsoft/vscode" - 特定リポジトリのみ
  - 例: "microsoft/*" - microsoft組織のすべてのリポジトリ
  - 例: "*/vscode" - どの組織でも vscode という名前のリポジトリ

収集対象:
```
${PR_REVIEW_KNOWLEDGE_PATH}/knowledge/pr-reviews/github.com/*/*/pr-*-summary.md
```

解析・分類カテゴリ:
1. **アーキテクチャ設計**
    - システム設計の議論
    - モジュール分割・依存関係
    - インターフェース設計

2. **パフォーマンス最適化**
    - レスポンス時間改善
    - メモリ使用量最適化
    - データベース・クエリ最適化

3. **セキュリティ**
    - 脆弱性の指摘・修正
    - 認証・認可の実装
    - データ保護・暗号化

4. **コード品質**
    - 可読性・保守性の改善
    - テスト戦略・カバレッジ
    - エラーハンドリング

5. **開発・運用プロセス**
    - CI/CD の改善
    - 監視・ログ設定
    - デプロイメント戦略

6. **時系列分析**
    - 技術トレンドの変遷
    - 議論パターンの変化
    - 品質向上の軌跡
    - 繰り返し発生する問題の特定

7. **未来ビジョン管理**
    - 技術的理想像の追跡
    - アーキテクチャロードマップ
    - 品質目標とマイルストーン
    - ビジョンの妥当性検証

出力Markdown構造:
```markdown
# PRレビュー知識サマリー

生成日時: YYYY-MM-DD HH:mm:ss
対象: {repository_pattern または "全リポジトリ"}
解析対象ファイル数: {count}

## カテゴリ別知見サマリー

### アーキテクチャ設計

#### 頻出パターン・ベストプラクティス
- **{パターン名}**: {説明}
  - 事例: [{リポジトリ名} PR#{番号}](file_path)
  - 学習ポイント: {要点}

#### 避けるべきアンチパターン
- **{アンチパターン名}**: {説明}
  - 事例: [{リポジトリ名} PR#{番号}](file_path)
  - 代替案: {推奨アプローチ}

### パフォーマンス最適化
[同様の構造]

### セキュリティ
[同様の構造]

### コード品質
[同様の構造]

### 開発・運用プロセス
[同様の構造]

## 時系列分析

### 技術トレンドの変遷
#### {期間}の主要な変化
- **新技術の導入**: {導入された技術・ライブラリ}
  - 初回言及: [{リポジトリ名} PR#{番号}](file_path) ({YYYY-MM-DD})
  - 採用理由: {技術選択の背景}
  - 現在の状況: {定着度・活用状況}

- **廃止された技術**: {フェードアウトした技術}
  - 最終言及: [{リポジトリ名} PR#{番号}](file_path) ({YYYY-MM-DD})
  - 廃止理由: {技術変更の背景}
  - 移行先: {後継技術}

#### 議論パターンの変化
- **{期間}前期**: {主要な議論テーマ}
- **{期間}中期**: {議論の変化・進化}
- **{期間}後期**: {現在の議論傾向}

### 品質向上の軌跡
#### メトリクス改善の推移
- **コード品質スコア**: {初期値} → {現在値} ({変化率})
- **セキュリティ意識**: {具体的な改善事例}
- **テストカバレッジ**: {測定可能な場合の数値推移}

#### 繰り返し発生する問題
- **{問題パターン1}**: {発生回数}回
  - 初回発生: {YYYY-MM-DD}
  - 最新発生: {YYYY-MM-DD}
  - 根本原因: {分析結果}
  - 対策状況: {現在の取り組み}

## 未来ビジョン管理

### 技術的理想像の追跡
#### アーキテクチャビジョン
- **目指すべき設計**: {理想的なアーキテクチャ}
  - 初回提案: [{リポジトリ名} PR#{番号}](file_path) ({YYYY-MM-DD})
  - 現在の進捗: {達成状況・進捗度}
  - 次のマイルストーン: {具体的な目標}

- **技術スタック戦略**: {長期的な技術選択方針}
  - 方針決定: {YYYY-MM-DD}
  - 実装状況: {現在の適用範囲}
  - 課題・障害: {実現に向けた課題}

#### 品質目標とマイルストーン
- **品質目標**: {具体的な品質指標}
  - 目標設定日: {YYYY-MM-DD}
  - 現在の達成度: {数値・評価}
  - 達成予定: {目標時期}

### ビジョンの妥当性検証
#### 陳腐化チェック結果
- **最新技術トレンドとの整合性**: ✅/⚠️/❌
  - 業界標準との比較: {現状評価}
  - 更新推奨事項: {具体的な見直し提案}

- **実現可能性の再評価**: ✅/⚠️/❌
  - 技術的実現性: {現時点での評価}
  - リソース要件: {必要な人的・時間リソース}
  - 優先度の再考: {他の課題との優先順位}

#### ビジョン更新履歴
- **{YYYY-MM-DD}**: {ビジョン策定}
- **{YYYY-MM-DD}**: {第1回見直し - 変更内容}
- **{YYYY-MM-DD}**: {第2回見直し - 変更内容}

### 推奨アクション
#### 短期アクション（3ヶ月以内）
1. {即座に取り組むべき項目}
2. {技術的負債の解消}

#### 中期アクション（6ヶ月〜1年）
1. {アーキテクチャ改善}
2. {新技術の段階的導入}

#### 長期アクション（1年以上）
1. {大規模リファクタリング}
2. {次世代システムへの移行}

## 横断的な学習ポイント

### 言語・技術別の特徴
- **JavaScript/TypeScript**: {特徴的な議論}
- **Python**: {特徴的な議論}
- **Go**: {特徴的な議論}
- **その他**: {特徴的な議論}

### チーム・プロジェクト別の傾向
- **{リポジトリ名}**: {特徴的な議論の傾向}

## 統計情報

### 最も議論されたトピック
1. {トピック名} - {出現回数}回
2. {トピック名} - {出現回数}回

### 最も学習価値の高いPR
1. [{リポジトリ名} PR#{番号}: {タイトル}](file_path) - {理由}
2. [{リポジトリ名} PR#{番号}: {タイトル}](file_path) - {理由}

### 技術スタック別分析
- **フロントエンド**: {件数}件の議論
- **バックエンド**: {件数}件の議論
- **インフラ**: {件数}件の議論
- **データベース**: {件数}件の議論

## 今後の改善アクション

### 継続学習推奨トピック
1. {トピック} - {理由}
2. {トピック} - {理由}

### 注意深く監視すべき領域
1. {領域} - {理由}
2. {領域} - {理由}

## 参考リンク・リソース

### 関連する外部リソース
- [{タイトル}]({URL}) - {説明}

### 内部ドキュメント・ガイドライン
- [{タイトル}]({パス}) - {説明}
```

実装されるBashスクリプト:
```bash
#!/bin/bash

# 処理開始時間を記録（パフォーマンス計測用）
START_TIME=$(date +%s)

# 引数取得
REPOSITORY_PATTERN=${1:-""}  # オプション引数

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

# pr-reviewsディレクトリの存在確認
PR_REVIEWS_DIR="$PR_REVIEW_KNOWLEDGE_PATH/knowledge/pr-reviews"
if [ ! -d "$PR_REVIEWS_DIR" ]; then
    echo "エラー: PRレビューファイルが見つかりません: $PR_REVIEWS_DIR"
    echo "先に /kcc-save-pr-comments でPRデータを収集してください"
    exit 1
fi

# summaryディレクトリの作成
SUMMARY_DIR="$PR_REVIEWS_DIR/summary"
mkdir -p "$SUMMARY_DIR"
if [ $? -ne 0 ]; then
    echo "エラー: サマリーディレクトリの作成に失敗しました: $SUMMARY_DIR"
    exit 1
fi

echo "処理対象パターン: ${REPOSITORY_PATTERN:-'全リポジトリ'}"

# ファイル名の生成
filename=$(echo "$REPOSITORY_PATTERN" | sed 's/\*/all/g' | sed 's/\//_/g')
if [ -z "$filename" ]; then
    filename="all"
fi

OUTPUT_FILE="$SUMMARY_DIR/${filename}.md"
echo "出力ファイル: $OUTPUT_FILE"

# 差分処理：既存サマリーファイルの最終更新日時をチェック
LAST_UPDATE_TIME=0
if [ -f "$OUTPUT_FILE" ]; then
    LAST_UPDATE_TIME=$(stat -f %m "$OUTPUT_FILE" 2>/dev/null || stat -c %Y "$OUTPUT_FILE" 2>/dev/null)
    LAST_UPDATE_DATE=$(date -r $LAST_UPDATE_TIME +"%Y-%m-%d %H:%M:%S")
    echo "既存サマリーファイルが見つかりました（最終更新: $LAST_UPDATE_DATE）"
    echo "差分処理モードで実行します..."
else
    echo "新規サマリーファイルを作成します..."
fi
```

実装アルゴリズム:
1. **環境変数設定フェーズ（上記スクリプトに含まれる）**

2. **ファイル名生成フェーズ**
   ```bash
   # repository_patternからファイル名を生成
   # 例: "microsoft/vscode" → "microsoft_vscode.md"
   # 例: "microsoft/*"     → "microsoft_all.md"
   # 例: "*/vscode"        → "all_vscode.md"
   # 例: "*"               → "all.md"
   # 例: 未指定             → "all.md"

   filename=$(echo "$REPOSITORY_PATTERN" | sed 's/\*/all/g' | sed 's/\//_/g')
   if [ -z "$filename" ]; then
     filename="all"
   fi
   ```

3. **ファイル収集フェーズ（差分処理対応）**
   ```bash
   # 全てのPRサマリーファイルを取得
   echo "PRレビューファイルを収集中..."

   if [ -n "$REPOSITORY_PATTERN" ]; then
       # パターンマッチングでファイルパスを絞り込み
       pattern_escaped=$(echo "$REPOSITORY_PATTERN" | sed 's/\*/[^\/]*/g')
       ALL_FILES=$(find "$PR_REVIEWS_DIR" -name "pr-*-summary.md" -type f | grep -E "github\.com/$pattern_escaped/")
   else
       ALL_FILES=$(find "$PR_REVIEWS_DIR" -name "pr-*-summary.md" -type f)
   fi

   TOTAL_FILES=$(echo "$ALL_FILES" | wc -l | tr -d ' ')
   echo "対象ファイル総数: $TOTAL_FILES 件"

   # 差分処理：最終更新日時より新しいファイルのみを抽出
   if [ $LAST_UPDATE_TIME -gt 0 ]; then
       NEW_FILES=""
       PROCESSED_COUNT=0

       echo "差分チェック中..."
       for file in $ALL_FILES; do
           if [ -f "$file" ]; then
               FILE_TIME=$(stat -f %m "$file" 2>/dev/null || stat -c %Y "$file" 2>/dev/null)
               if [ $FILE_TIME -gt $LAST_UPDATE_TIME ]; then
                   NEW_FILES="$NEW_FILES$file\n"
                   ((PROCESSED_COUNT++))
               fi
           fi
       done

       if [ $PROCESSED_COUNT -eq 0 ]; then
           echo "✅ 新しいPRレビューファイルはありません。サマリーは最新状態です。"
           exit 0
       else
           echo "新規・更新ファイル: $PROCESSED_COUNT 件を処理します"
           TARGET_FILES=$(echo -e "$NEW_FILES" | grep -v '^$')
       fi
   else
       echo "初回実行: 全ファイルを処理します"
       TARGET_FILES="$ALL_FILES"
       PROCESSED_COUNT=$TOTAL_FILES
   fi
   ```

4. **コンテンツ解析フェーズ**
   ```bash
   # 各ファイルからメタデータと技術的議論を抽出
   # セクション単位での内容分類
   # キーワード頻度分析
   ```

5. **統合・分類フェーズ**
   ```bash
   # 類似する議論をグループ化
   # カテゴリ別の知見抽出
   # 統計情報の計算
   ```

6. **時系列分析フェーズ**
   ```bash
   # PR作成日時による時系列ソート
   sort -t'-' -k3,3 -k4,4 pr_files_with_dates.txt

   # 技術キーワードの出現頻度を時期別に集計
   # 例: React, Vue, Angular等の言及回数推移

   # 問題パターンの再発検出
   grep -E "同じ|類似|再発|繰り返し" */pr-*-summary.md

   # 品質改善の軌跡追跡
   # セキュリティ、パフォーマンス、テスト等の指標変化
   ```

7. **未来ビジョン分析フェーズ**
   ```bash
   # 既存のビジョンファイル読み込み
   if [ -f "$PR_REVIEW_KNOWLEDGE_PATH/vision/architecture-vision.md" ]; then
     vision_last_update=$(stat -f %m "$PR_REVIEW_KNOWLEDGE_PATH/vision/architecture-vision.md")
     current_time=$(date +%s)
     days_since_update=$(((current_time - vision_last_update) / 86400))
   fi

   # ビジョンの陳腐化チェック（90日以上更新なしで警告）
   if [ $days_since_update -gt 90 ]; then
     echo "⚠️ ビジョンの見直しが推奨されます"
   fi

   # 最新技術トレンドとの整合性チェック
   # 新技術キーワードの検出と既存ビジョンとの比較
   ```

8. **出力生成フェーズ（マージ対応）**
   ```bash
   # 統合サマリーの生成
   echo "統合サマリーを生成中: $OUTPUT_FILE"

   # 既存サマリーファイルの内容を保持（差分処理時）
   EXISTING_CONTENT=""
   IS_UPDATE_MODE=false

   if [ $LAST_UPDATE_TIME -gt 0 ]; then
       echo "既存サマリーとマージします..."
       IS_UPDATE_MODE=true
       # 既存コンテンツを読み取り（必要に応じて）
       # EXISTING_CONTENT=$(cat "$OUTPUT_FILE")
   fi

   # 新規コンテンツの解析と統合処理を実行
   # （技術的議論の抽出、分類、統計情報の計算など）
   # 差分処理時は新規ファイルのみを解析してマージ

   # 最終サマリーファイルの生成
   cat > "$OUTPUT_FILE" << 'EOF_TEMPLATE'
# PRレビュー知識サマリー

生成日時: $(date "+%Y-%m-%d %H:%M:%S")
対象: ${REPOSITORY_PATTERN:-"全リポジトリ"}
解析対象ファイル数: $PROCESSED_COUNT 件（総数: $TOTAL_FILES 件）

EOF_TEMPLATE

   # 処理モード表示
   if [ "$IS_UPDATE_MODE" = true ]; then
       echo "✅ PRレビュー知識サマリーを更新しました: $OUTPUT_FILE"
       echo "新規追加: $PROCESSED_COUNT 件"
       echo "総合計: $TOTAL_FILES 件"
   else
       echo "✅ PRレビュー知識サマリーを作成しました: $OUTPUT_FILE"
       echo "処理ファイル数: $PROCESSED_COUNT 件"
   fi

   # パフォーマンス情報
   END_TIME=$(date +%s)
   if [ -n "$START_TIME" ]; then
       ELAPSED=$((END_TIME - START_TIME))
       echo "処理時間: ${ELAPSED}秒"
   fi
   ```

エラーハンドリング:
- 環境変数 PR_REVIEW_KNOWLEDGE_PATH の存在確認
- pr-reviews ディレクトリの存在確認
- summary ディレクトリの作成権限確認
- Markdownファイルの読み取り権限確認
- 不正なファイル形式のスキップ処理
- repository_pattern の妥当性確認
- 出力先ファイルの書き込み権限確認
- **差分処理関連のエラー処理**
  - ファイル更新日時の取得失敗時の処理
  - 既存サマリーファイルが破損している場合のフォールバック
  - 差分チェック中のファイルアクセスエラー処理

パフォーマンス考慮:
- **差分処理による高速化**
  - 既存サマリーファイルがある場合、新規・更新ファイルのみを処理
  - 処理対象ファイル数の大幅削減により実行時間を短縮
  - 大規模なPRデータベースでの実用性向上
- 大量ファイル処理時のメモリ効率化
- 並列処理での高速化（適用可能な場合）
- 進捗表示による ユーザビリティ向上
- 処理時間の計測と表示

使用例と出力ファイル:
```bash
# 全リポジトリの知識をサマリー化（初回実行）
/kcc-summarize-pr-knowledge
# → 全ファイルを処理してサマリーを作成
# → ${PR_REVIEW_KNOWLEDGE_PATH}/knowledge/pr-reviews/summary/all.md

# 2回目以降の実行（差分処理）
/kcc-summarize-pr-knowledge
# → 前回以降の新規・更新ファイルのみを処理
# → 出力例：「新規・更新ファイル: 3 件を処理します」

# 特定リポジトリのみをサマリー化
/kcc-summarize-pr-knowledge microsoft/vscode
# → ${PR_REVIEW_KNOWLEDGE_PATH}/knowledge/pr-reviews/summary/microsoft_vscode.md

# Microsoft組織のすべてのリポジトリをサマリー化
/kcc-summarize-pr-knowledge "microsoft/*"
# → ${PR_REVIEW_KNOWLEDGE_PATH}/knowledge/pr-reviews/summary/microsoft_all.md

# すべての組織のvscodeリポジトリをサマリー化
/kcc-summarize-pr-knowledge "*/vscode"
# → ${PR_REVIEW_KNOWLEDGE_PATH}/knowledge/pr-reviews/summary/all_vscode.md

# ワイルドカードのみ
/kcc-summarize-pr-knowledge "*"
# → ${PR_REVIEW_KNOWLEDGE_PATH}/knowledge/pr-reviews/summary/all.md
```

## 差分処理の実行結果例

### 初回実行時
```
処理対象パターン: 全リポジトリ
出力ファイル: /path/to/.claude/knowledge/pr-reviews/summary/all.md
新規サマリーファイルを作成します...
PRレビューファイルを収集中...
対象ファイル総数: 150 件
初回実行: 全ファイルを処理します
統合サマリーを生成中: /path/to/.claude/knowledge/pr-reviews/summary/all.md
✅ PRレビュー知識サマリーを作成しました: /path/to/.claude/knowledge/pr-reviews/summary/all.md
処理ファイル数: 150 件
処理時間: 45秒
```

### 2回目以降（差分あり）
```
処理対象パターン: 全リポジトリ
出力ファイル: /path/to/.claude/knowledge/pr-reviews/summary/all.md
既存サマリーファイルが見つかりました（最終更新: 2025-01-15 14:30:00）
差分処理モードで実行します...
PRレビューファイルを収集中...
対象ファイル総数: 153 件
差分チェック中...
新規・更新ファイル: 3 件を処理します
統合サマリーを生成中: /path/to/.claude/knowledge/pr-reviews/summary/all.md
既存サマリーとマージします...
✅ PRレビュー知識サマリーを更新しました: /path/to/.claude/knowledge/pr-reviews/summary/all.md
新規追加: 3 件
総合計: 153 件
処理時間: 2秒
```

### 差分なしの場合
```
処理対象パターン: 全リポジトリ
出力ファイル: /path/to/.claude/knowledge/pr-reviews/summary/all.md
既存サマリーファイルが見つかりました（最終更新: 2025-01-15 14:35:00）
差分処理モードで実行します...
PRレビューファイルを収集中...
対象ファイル総数: 153 件
差分チェック中...
✅ 新しいPRレビューファイルはありません。サマリーは最新状態です。
```

前提条件:
- 環境変数 PR_REVIEW_KNOWLEDGE_PATH がオプションで設定可能（未設定の場合はリポジトリルート/.claude をデフォルト使用）
- pr-reviews ディレクトリとPRサマリーファイルが存在すること
- find, grep, sed, awk等の基本的なテキスト処理コマンドが利用可能であること

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
├── knowledge/
│   └── pr-reviews/
│       ├── github.com/
│       │   └── owner/
│       │       └── repo/
│       │           ├── pr-1-2025-11-08-summary.md
│       │           ├── pr-2-2025-11-09-summary.md
│       │           └── ...
│       └── summary/
│           ├── all.md                    # 全リポジトリのサマリー
│           ├── microsoft_vscode.md       # 特定リポジトリのサマリー
│           ├── microsoft_all.md          # 組織別サマリー
│           ├── all_vscode.md             # プロジェクト別サマリー
│           └── ...
└── vision/
    ├── architecture-vision.md           # アーキテクチャの理想像
    ├── tech-stack-roadmap.md            # 技術スタック戦略
    ├── quality-goals.md                 # 品質目標とマイルストーン
    └── vision-history.md                # ビジョン変更履歴
```

動作確認手順:
1. テスト用のPRサマリーファイルを複数作成
2. 引数なしでの全体サマリー生成を確認（all.mdの生成）
3. repository_pattern指定での絞り込み動作を確認
4. 生成されたファイル名の正確性を確認（*→all、/→_変換）
5. 生成されたサマリーファイルの内容品質を確認
6. 時系列分析機能の検証
   - 技術キーワードの時期別出現頻度集計
   - 問題パターンの再発検出
   - 品質改善の軌跡追跡
7. 未来ビジョン管理機能の検証
   - 既存ビジョンファイルの読み込み
   - 陳腐化チェック（90日経過警告）
   - 新技術トレンドとの整合性評価
8. エラーケース（存在しないパス等）での適切な処理を確認
