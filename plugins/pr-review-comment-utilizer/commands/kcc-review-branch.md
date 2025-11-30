---
description: 蓄積されたPRレビュー知識を活用して、現在のブランチの変更をレビューし、改善提案を行う。
---

# kcc-review-branch

蓄積されたPRレビュー知識を活用して、現在のブランチの変更をレビューし、改善提案を行ってください

使用方法: /kcc-review-branch [base_branch] [--severity=all|high|medium|low]

実行手順:
1. 環境変数 PR_REVIEW_KNOWLEDGE_PATH の確認とベースディレクトリ設定
   - 未設定の場合: リポジトリルート/.claude（git rev-parse --show-toplevel）/.claude をデフォルトとして使用
   - Gitリポジトリ外の場合: 現在のディレクトリ/.claude（pwd/.claude）をデフォルトとして使用
2. 現在のブランチと base_branch（デフォルト: main/master）の差分を取得
3. 知識ベース・ビジョンファイルの読み込み
   - 蓄積されたPR知識サマリーファイル（時系列分析含む）
   - 未来ビジョンファイル群（アーキテクチャ理想像、技術戦略、品質目標等）
   - ビジョン変更履歴とロードマップファイル
4. 変更内容を解析し、過去の知見・未来ビジョンと照合
5. ビジョン整合性チェックとレビューコメント生成
   - アーキテクチャビジョンとの整合性評価
   - 技術戦略・品質目標との適合性確認
   - 陳腐化技術の使用チェック
6. レビュー結果をMarkdown形式で出力

引数:
- base_branch (オプション): 比較対象のベースブランチ（デフォルト: main または master を自動検出）
- --severity (オプション): 出力する指摘のレベル
  - all: すべての指摘を出力（デフォルト）
  - high: 重要度が高い指摘のみ
  - medium: 重要度が中程度以上の指摘
  - low: 重要度が低い指摘も含む

Git差分取得:
```bash
# 現在のブランチ名を取得
current_branch=$(git branch --show-current)

# ベースブランチの自動検出
base_branch=${1:-$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@' || echo "main")}

# 差分の取得
git diff --name-status $base_branch..HEAD  # 変更されたファイル一覧
git diff $base_branch..HEAD               # 詳細な差分
git log --oneline $base_branch..HEAD      # コミット履歴
```

知識活用ロジック:
1. **関連知識の特定**:
   - 変更されたファイルの拡張子・技術スタックから関連する知見を抽出
   - ファイルパス（フロントエンド/バックエンド/インフラ）に基づく分類
   - コードパターンのマッチング
   - 時系列分析からの過去問題パターン検出

2. **ビジョン整合性チェック**:
   - アーキテクチャビジョンとの整合性：目指すべき設計との比較
   - 技術スタック戦略との整合性：承認された技術選択との照合
   - 品質目標との整合性：設定された品質基準・メトリクスとの比較
   - 廃止予定技術の使用チェック：時系列分析からの非推奨技術検出

3. **レビューポイントの生成**:
   - アーキテクチャ設計: モジュール分割、依存関係の妥当性、ビジョンとの整合性
   - パフォーマンス: N+1問題、不要な処理、リソース消費
   - セキュリティ: 入力検証、権限チェック、データ保護
   - コード品質: 命名規則、可読性、テストカバレッジ、品質目標達成度
   - 運用面: ログ出力、エラーハンドリング、監視対応
   - 戦略的整合性: 技術選択の妥当性、将来性、ロードマップとの整合

4. **重要度の判定**:
   - high: セキュリティ脆弱性、パフォーマンス劣化、アーキテクチャ違反、ビジョン逸脱
   - medium: コード品質、保守性の問題、ベストプラクティス違反、品質目標未達
   - low: コードスタイル、命名規則、軽微な改善提案

出力Markdown構造:
```markdown
# ブランチレビュー結果

## 基本情報
- **ブランチ**: {current_branch}
- **ベースブランチ**: {base_branch}
- **レビュー日時**: YYYY-MM-DD HH:mm:ss
- **変更ファイル数**: {count}
- **追加行数**: +{additions}
- **削除行数**: -{deletions}

## 変更サマリー
### 主要な変更
- {主要な変更の概要1}
- {主要な変更の概要2}

### 技術スタック分析
- **言語**: {検出された言語}
- **フレームワーク**: {検出されたフレームワーク}
- **変更カテゴリ**: {フロントエンド/バックエンド/インフラ/設定等}

## ビジョン整合性評価

### 🎯 アーキテクチャビジョンとの整合性
- **評価**: ✅ 整合 / ⚠️ 要注意 / ❌ 不整合
- **理想像との比較**: {アーキテクチャビジョンファイルから読み取った内容との照合結果}
- **指摘事項**: {ビジョンからの逸脱点}
- **推奨対応**: {ビジョンに沿った修正提案}

### 📈 技術戦略との適合性
- **評価**: ✅ 適合 / ⚠️ 要検討 / ❌ 不適合
- **戦略目標**: {技術スタックロードマップからの目標}
- **現在の選択**: {変更で採用された技術・手法}
- **将来性**: {長期的な技術戦略との整合性}

### 🎚️ 品質目標達成度
- **設定目標**: {品質目標ファイルから読み取った基準}
- **達成度評価**: {目標に対する現在の変更の評価}
- **改善提案**: {品質向上のための具体的提案}

### ⚠️ 陳腐化技術チェック
- **使用技術の評価**: {時系列分析から検出された技術の現状}
- **非推奨技術**: {廃止予定・非推奨とされた技術の使用有無}
- **移行推奨**: {新しい技術への移行提案}

## レビュー指摘事項

### 🔴 高重要度（High）
#### {カテゴリ名}
**ファイル**: `{file_path}:{line_number}`
**指摘内容**: {具体的な問題点}
**根拠**: {過去のPR事例や知見への参照}
**ビジョン関連**: {関連するビジョン・戦略文書への言及}
**推奨対応**: {具体的な修正提案}
**参考**: [{関連PR事例}](path_to_knowledge_file)

### 🟡 中重要度（Medium）
[同様の構造]

### 🟢 低重要度（Low）
[同様の構造]

## 推奨改善アクション

### 即座に対応すべき項目
1. {重要度の高い修正項目}
2. {セキュリティ関連の修正}
3. {ビジョン逸脱の修正}

### 今後検討すべき項目
1. {リファクタリング提案}
2. {技術的負債の解消}
3. {ビジョン実現に向けた段階的改善}

### 学習・参考資料
- [{関連する知識サマリー}](path_to_summary)
- [{類似のPR事例}](path_to_pr_knowledge)
- [{アーキテクチャビジョン}](path_to_vision)
- [{技術戦略ロードマップ}](path_to_roadmap)

## 全体評価

### ✅ 良い点
- {良い実装や設計の評価}
- {ベストプラクティスの遵守}
- {ビジョンとの整合性が取れている点}

### ⚠️ 注意点
- {今後気をつけるべき点}
- {継続的な改善提案}
- {ビジョン実現に向けた課題}

### 📊 品質メトリクス
- **コード品質スコア**: {点数}/100
- **セキュリティリスク**: {Low/Medium/High}
- **保守性**: {点数}/10
- **テスト充実度**: {点数}/10
- **ビジョン整合度**: {点数}/10

## 戦略的評価

### 🚀 ビジョン実現への貢献度
- **短期目標への影響**: {3ヶ月以内の目標への寄与}
- **中期目標への影響**: {6ヶ月〜1年の目標への寄与}
- **長期ビジョンとの整合**: {1年以上の長期戦略との整合性}

### 📅 ロードマップへの影響
- **予定されていた変更**: {ロードマップで計画済みの変更との関係}
- **想定外の変更**: {計画にない変更とその妥当性}
- **優先度の調整**: {今回の変更によるロードマップへの影響}

## 次のステップ
1. 高重要度の指摘事項への対応
2. ビジョン整合性の改善
3. 関連するテストの追加・更新
4. ドキュメントの更新（必要に応じて）
5. ビジョン・戦略文書の見直し検討
6. レビュー指摘の修正後の再確認
```

実装アルゴリズム:
```bash
# 1. 環境準備フェーズ
PR_REVIEW_KNOWLEDGE_PATH=${PR_REVIEW_KNOWLEDGE_PATH:-"$(git rev-parse --show-toplevel 2>/dev/null || pwd)/.claude"}
current_branch=$(git branch --show-current)
base_branch=${1:-$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null | sed 's@^refs/remotes/origin/@@' || echo "main")}

# 2. 差分解析フェーズ
git diff --name-status $base_branch..HEAD > changed_files.txt
git diff --stat $base_branch..HEAD > diff_stats.txt
git diff $base_branch..HEAD > detailed_diff.txt

# 3. 知識読み込みフェーズ
# サマリーファイルの読み込み
find "$PR_REVIEW_KNOWLEDGE_PATH/knowledge/pr-reviews/summary" -name "*.md" -type f

# 個別PR知識の読み込み（関連する技術スタック）
find "$PR_REVIEW_KNOWLEDGE_PATH/knowledge/pr-reviews" -name "pr-*-summary.md" -type f

# 4. ビジョンファイル読み込みフェーズ
# アーキテクチャビジョンの読み込み
if [ -f "$PR_REVIEW_KNOWLEDGE_PATH/vision/architecture-vision.md" ]; then
  architecture_vision=$(cat "$PR_REVIEW_KNOWLEDGE_PATH/vision/architecture-vision.md")
fi

# 技術戦略ロードマップの読み込み
if [ -f "$PR_REVIEW_KNOWLEDGE_PATH/vision/tech-stack-roadmap.md" ]; then
  tech_roadmap=$(cat "$PR_REVIEW_KNOWLEDGE_PATH/vision/tech-stack-roadmap.md")
fi

# 品質目標の読み込み
if [ -f "$PR_REVIEW_KNOWLEDGE_PATH/vision/quality-goals.md" ]; then
  quality_goals=$(cat "$PR_REVIEW_KNOWLEDGE_PATH/vision/quality-goals.md")
fi

# ビジョン変更履歴の読み込み
if [ -f "$PR_REVIEW_KNOWLEDGE_PATH/vision/vision-history.md" ]; then
  vision_history=$(cat "$PR_REVIEW_KNOWLEDGE_PATH/vision/vision-history.md")
fi

# 5. 解析・照合フェーズ
# ファイル拡張子から技術スタック判定
# 変更内容から潜在的問題パターンの検出
# 過去の知見との照合
# ビジョンファイルとの整合性チェック

# 6. ビジョン整合性評価フェーズ
# アーキテクチャビジョンとの比較
# 技術戦略目標との適合性確認
# 品質目標達成度の評価
# 陳腐化技術の使用チェック（時系列分析結果から）

# 7. レビュー生成フェーズ
# 重要度別の指摘事項の整理（ビジョン逸脱を含む）
# 具体的な改善提案の生成（ビジョン実現に向けた提案含む）
# 参考資料・事例の紐付け（ビジョンファイルへのリンク含む）
# 戦略的評価の生成

# 8. 出力フェーズ
output_file="$PR_REVIEW_KNOWLEDGE_PATH/reviews/branch-review-$(date +%Y%m%d-%H%M%S).md"
mkdir -p "$(dirname "$output_file")"
```

技術スタック判定ロジック:
```bash
# ファイル拡張子ベースの判定
detect_tech_stack() {
  local files="$1"
  local tech_stack=""

  if echo "$files" | grep -q '\.js$\|\.ts$\|\.jsx$\|\.tsx$'; then
    tech_stack="$tech_stack JavaScript/TypeScript"
  fi

  if echo "$files" | grep -q '\.py$'; then
    tech_stack="$tech_stack Python"
  fi

  if echo "$files" | grep -q '\.go$'; then
    tech_stack="$tech_stack Go"
  fi

  if echo "$files" | grep -q '\.java$'; then
    tech_stack="$tech_stack Java"
  fi

  if echo "$files" | grep -q '\.rs$'; then
    tech_stack="$tech_stack Rust"
  fi

  if echo "$files" | grep -q 'Dockerfile\|docker-compose\|\.yml$\|\.yaml$'; then
    tech_stack="$tech_stack Infrastructure"
  fi

  echo "$tech_stack"
}
```

エラーハンドリング:
- Gitリポジトリ外での実行チェック
- ベースブランチの存在確認
- PR知識ファイルの存在確認
- ビジョンファイルの存在確認（存在しない場合は警告出力）
- 差分が存在しない場合の処理
- 環境変数 PR_REVIEW_KNOWLEDGE_PATH の確認
- 出力ディレクトリの作成権限確認

使用例:
```bash
# 現在のブランチをmainブランチと比較してレビュー
/kcc-review-branch

# developブランチと比較してレビュー
/kcc-review-branch develop

# 高重要度の指摘のみ表示
/kcc-review-branch --severity=high

# 特定のベースブランチと重要度を指定
/kcc-review-branch develop --severity=medium
```

前提条件:
- Gitリポジトリ内で実行すること
- 環境変数 PR_REVIEW_KNOWLEDGE_PATH がオプションで設定可能（未設定の場合はリポジトリルート/.claude をデフォルト使用）
- PR知識サマリーファイルが存在すること（kcc-summarize-pr-knowledge で生成済み）
- git コマンドが利用可能であること

環境変数設定:
```bash
# デフォルト: リポジトリルート/.claude配下に設定（推奨）
export PR_REVIEW_KNOWLEDGE_PATH="$(git rev-parse --show-toplevel 2>/dev/null || pwd)/.claude"

# 例: ホームディレクトリ配下に設定
export PR_REVIEW_KNOWLEDGE_PATH="$HOME/.claude"

# 例: 専用ディレクトリに設定
export PR_REVIEW_KNOWLEDGE_PATH="/path/to/your/notes"
```

ディレクトリ構造:
```
${PR_REVIEW_KNOWLEDGE_PATH}/
├── knowledge/
│   └── pr-reviews/
│       ├── github.com/*/*/pr-*-summary.md  # 個別PR知識
│       └── summary/*.md                     # 統合サマリー（時系列分析含む）
├── vision/
│   ├── architecture-vision.md              # アーキテクチャの理想像
│   ├── tech-stack-roadmap.md               # 技術スタック戦略
│   ├── quality-goals.md                    # 品質目標とマイルストーン
│   └── vision-history.md                   # ビジョン変更履歴
└── reviews/
    ├── branch-review-20251201-143022.md     # ブランチレビュー結果
    ├── branch-review-20251201-151143.md
    └── ...
```

期待される効果:
1. **学習効果の向上**: 過去のレビュー知見を活用した継続的学習
2. **品質の向上**: 既知の問題パターンの早期発見
3. **レビュー効率化**: 人的レビュー前のセルフチェック
4. **知識の活用**: 蓄積した知見の実践的活用
5. **一貫性の確保**: プロジェクト全体での品質基準の統一
6. **戦略的整合性**: ビジョンとロードマップに沿った開発の推進
7. **技術負債の予防**: 陳腐化技術の早期発見と移行推奨
8. **継続的改善**: ビジョン実現に向けた段階的な品質向上

動作確認手順:
1. テスト用のブランチで変更を作成
2. kcc-save-pr-comments でPR知識を蓄積
3. kcc-summarize-pr-knowledge でサマリーを生成
4. kcc-review-branch でブランチレビューを実行
5. 生成されたレビュー内容の妥当性を確認
6. 重要度フィルタリングの動作確認
