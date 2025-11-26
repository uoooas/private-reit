# Claude Code プロジェクト設定

## プロジェクト構成

このプロジェクトは以下のサブプロジェクトで構成されています：

- `private-reit/` : 私募REIT情報サイト本体（Ruby on Rails 4.1.4）
- `private-reit-form/` : 会員登録フォーム
- その他補助ディレクトリ

## Serena メモリ命名規則

### ファイル名プレフィックス

メモリファイルは以下の命名規則に従います：

- **`app-*`** : private-reit本体（`/home/kaitotam/private-reit/private-reit/`）に関する情報
  - 例: `app-directory-structure.md`, `app-database-schema.md`

- **`form-*`** : private-reit-form（`/home/kaitotam/private-reit/private-reit-form/`）に関する情報
  - 例: `form-registration-flow.md`, `form-validation-rules.md`

- **プレフィックスなし** : プロジェクト全体、または複数サブプロジェクトに共通する情報
  - 例: `project_overview.md`, `suggested_commands.md`, `code_style_and_conventions.md`

### メモリ作成時のルール

1. **新規メモリを作成する際は、上記プレフィックスを必ず使用する**
   - 対象が明確な場合は `app-` または `form-` を付ける
   - プロジェクト全体に関わる場合はプレフィックスなし

2. **各メモリの冒頭に対象範囲を明記する**
   ```markdown
   > **対象**: app本体 (`/home/kaitotam/private-reit/private-reit/`)
   ```
   または
   ```markdown
   > **対象**: form (`/home/kaitotam/private-reit/private-reit-form/`)
   ```

3. **ファイル名はケバブケース（kebab-case）を使用**
   - 良い例: `app-controller-routing.md`
   - 悪い例: `AppControllerRouting.md`, `app_controller_routing.md`

4. **ファイル名は内容を的確に表現する**
   - 具体的で検索しやすい名前を付ける
   - 例: `app-member-registration-flow.md` (Good)
   - 例: `app-misc.md` (Bad)

### メモリ読み込み時のルール

1. **タスクの対象が明確な場合、該当プレフィックスのメモリを優先的に参照**
   - app本体の作業 → `app-*` メモリを確認
   - formの作業 → `form-*` メモリを確認

2. **不明な場合はプレフィックスなしのメモリから確認**
   - `project_overview.md` で全体像を把握
   - `suggested_commands.md` でよく使うコマンドを確認

3. **関連するメモリは複数参照する**
   - 1つのメモリだけでなく、関連情報も確認する
   - 例: データベース変更時は `app-database-schema.md` と `app-setup-guide.md` の両方を参照

## メモリ管理のベストプラクティス

### 重複を避ける
- 同じ情報を複数のメモリに書かない
- 既存メモリを更新するか、新規作成が必要か判断する

### 定期的な見直し
- 古い情報は更新または削除する
- プロジェクトの変更に合わせてメモリも更新する

### 明確な責務分担
- 1つのメモリファイルは1つのトピックに集中する
- 複数のトピックがある場合は分割する

## コーディング規約

プロジェクト全体のコーディング規約は `code_style_and_conventions.md` メモリを参照してください。

## タスク完了時のチェックリスト

タスク完了時に実行すべき項目は `task_completion_checklist.md` メモリを参照してください。
