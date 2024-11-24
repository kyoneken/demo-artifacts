# Markdown to PDF Converter with GitHub Actions

このプロジェクトは、GitHub Actions を使用して Markdown ファイルを PDF に自動変換するワークフローを提供します。PR へのプッシュ時に自動実行され、手動実行（workflow_dispatch）にも対応しています。

## 前提条件

1. **Container Registry アクセス権限**
   - `infra-md2pdf` プロジェクトの Container Registry へのアクセス権限が必要です
   - リポジトリの Settings > Secrets and variables > Actions に以下を設定済みであること
     ```
     GITHUB_TOKEN
     ```

2. **ファイル構成**
   ```
   .
   ├── .github/
   │   └── workflows/
   │       └── convert-docs.yml
   └── sample/
       └── markdown/
           └── *.md
   ```

## ワークフローの動作

### 自動実行（PR プッシュ時）

以下の条件で自動的にワークフローが実行されます：

- プルリクエストの作成時
- プルリクエストへの追加プッシュ時
- プルリクエストの再オープン時

対象となるファイル：
- `sample/markdown/*.md`
- `.github/workflows/convert-docs.yml`

### 手動実行（workflow_dispatch）

1. GitHub リポジトリの "Actions" タブに移動
2. 左サイドバーから "Convert Documentation to PDF" ワークフローを選択
3. "Run workflow" ボタンをクリック
4. 必要に応じてブランチを選択
5. "Run workflow" を実行

## 出力結果

ワークフロー実行後、以下のアーティファクトが生成されます：

- `documentation-pdfs`: 変換された PDF ファイル
- `documentation-html`: 中間生成物の HTML ファイル（デバッグ用）

アーティファクトは GitHub Actions の実行結果ページからダウンロード可能です。

## ワークフロー設定のカスタマイズ

`convert-docs.yml` の設定を変更することで、以下をカスタマイズ可能です：

```yaml
# 監視対象のファイルパス
paths:
  - 'sample/markdown/*.md'
  - '.github/workflows/convert-docs.yml'

# アーティファクトの保持期間
retention-days: 7

# コンテナイメージ
image: ghcr.io/kyoneken/infra-md2pdf:latest
```

## トラブルシューティング

1. **アクセス権限エラー**
   ```
   Error: Unable to pull image "ghcr.io/kyoneken/infra-md2pdf:latest"
   ```
   - Container Registry へのアクセス権限を確認してください
   - `GITHUB_TOKEN` が正しく設定されているか確認してください

2. **変換エラー**
   ```
   Error: No such file or directory
   ```
   - ファイルパスが正しいか確認してください
   - 必要なディレクトリが存在するか確認してください

3. **アーティファクトのアップロードエラー**
   ```
   Error: Artifact storage quota has been hit
   ```
   - 古いアーティファクトを削除してください
   - retention-days の値を短くすることを検討してください

## 制限事項

- GitHub Actions の無料プランでは、アーティファクトの保存容量に 500MB の制限があります
- アーティファクトの保持期間は最大 90 日です
- ワークフロー実行時間に制限があります
