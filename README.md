# 私募REIT情報サイト - 外部仕様書

## 1. サービス概要

**私募REIT（不動産投資信託）の情報を提供する会員制Webサイト**

適格機関投資家向けに、70以上の私募REITの詳細情報、財務資料、インタビュー記事などを提供している。

---

## 2. 利用者

### 2.1 一般会員（顧客）

**対象者**: 適格機関投資家
- 機関投資家
- 金融機関
- 資産運用会社
- 事業会社
- その他

**できること**:
- 私募REITの詳細情報を見る
- 財務資料（PDF）をダウンロードする
- インタビュー記事を読む
- 投資主一覧・関係法人一覧を見る

### 2.2 管理者（社内）

**対象者**: Japan REIT株式会社のスタッフ

**できること**:
- 会員登録の承認・却下
- 会員情報の編集・削除
- 一斉メールの送信
- アクセスログの閲覧

---

## 3. 主な機能

### 3.1 顧客向け機能

| 機能 | 説明 | URL |
|------|------|-----|
| トップページ | サイトのランディングページ | `/` |
| REIT詳細ページ | 各REITの詳細情報（70以上） | `/home/reit/xxx` |
| 財務資料ダウンロード | PDFファイルのダウンロード | `/home/:id/financedownload` |
| インタビュー記事 | 運用会社へのインタビュー（20記事以上） | `/interview/xxx` |
| 投資主一覧 | 全社横断の投資主リスト | `/home/unitholders` |
| 関係法人一覧 | 各REITの関係法人 | `/home/accounting` |
| 更新情報 | サイトの更新履歴 | `/home/updatelist` |
| FAQ | よくある質問 | `/home/faq` |

### 3.2 会員機能

| 機能 | 説明 | URL |
|------|------|-----|
| 会員登録 | 新規登録（外部フォーム経由） | 外部: `form.japan-private-reit.com` |
| ログイン | メール + パスワードで認証 | `/session` |
| ログアウト | セッション終了 | `/session` (DELETE) |
| パスワード再設定 | メールでリセットリンク送信 | `/members/reminder` |
| 退会 | 会員の退会処理 | `/members/:id/resign` |

### 3.3 管理者機能

| 機能 | 説明 | URL |
|------|------|-----|
| 会員一覧 | 登録済み会員の一覧表示・検索 | `/members/managelist` |
| 会員承認 | 新規登録の承認 + メール送信 | `/members/:id/managemail` |
| 会員却下 | 新規登録の却下 + メール送信 | `/members/:id/managemail` |
| 会員編集 | 会員情報の編集 | `/members/:id/manageedit` |
| 会員削除 | 会員の削除 | `/members/:id/managedelete` |
| 一斉メール | 全会員へメール送信 | `/members/managemailall` |
| ログ閲覧 | アクセスログの確認 | `/log/loglist` |

---

## 4. 会員登録フロー

```
┌─────────────────────────────────────────────────────────────────┐
│  1. ユーザーが外部フォームにアクセス                              │
│     URL: https://form.japan-private-reit.com/                   │
└───────────────────────────┬─────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  2. フォーム入力 + reCAPTCHA v2 チェック                         │
│     - 名前、会社名、部署名                                        │
│     - メールアドレス、電話番号                                    │
│     - 属性（機関投資家/金融機関/...）                             │
│     - 私募REITへの参加状況                                        │
│     - 興味のある私募リート（複数選択）                            │
│     - 利用規約への同意                                            │
└───────────────────────────┬─────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  3. Google Forms に送信                                          │
│     → スプレッドシートに保存                                      │
└───────────────────────────┬─────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  4. Google Apps Script (GAS) が自動実行                          │
│     ① reCAPTCHA トークンをサーバー側で検証                       │
│     ② 検証成功 → Rails API に POST                               │
│     ③ Slack に通知                                               │
└───────────────────────────┬─────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  5. Rails で会員データを保存                                     │
│     - members テーブルに登録                                      │
│     - member_status_on = 0 (未承認)                              │
└───────────────────────────┬─────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  6. 管理者が会員一覧で確認                                        │
│     URL: /members/managelist                                     │
└───────────────────────────┬─────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│  7. 承認 or 却下                                                 │
│     → 承認メール or 却下メールを送信                              │
│     → member_status_on を更新                                    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 5. システム構成

### 5.1 技術スタック

| 項目 | 内容 |
|------|------|
| フレームワーク | Ruby on Rails 4.1.4 |
| Ruby バージョン | 2.3.8 |
| データベース | MySQL 5.7 |
| 本番環境 | AWS EC2 |
| 認証 | bcrypt（パスワードハッシュ化） |
| ボット対策 | reCAPTCHA v2 |
| メール送信 | NoticeMailer |

### 5.2 主要ファイル構成

```
private-reit/
├── app/
│   ├── controllers/
│   │   ├── application_controller.rb  # 基底クラス
│   │   ├── top_controller.rb          # トップページ
│   │   ├── home_controller.rb         # 一般ページ
│   │   ├── reit_controller.rb         # REIT詳細ページ
│   │   ├── members_controller.rb      # 会員管理
│   │   ├── sessions_controller.rb     # ログイン/ログアウト
│   │   ├── interview_controller.rb    # インタビュー記事
│   │   └── log_controller.rb          # ログ閲覧
│   ├── models/
│   │   ├── member.rb                  # 会員
│   │   ├── brand.rb                   # REIT（ブランド）
│   │   └── log.rb                     # アクセスログ
│   ├── views/
│   │   ├── top/                       # トップページ
│   │   ├── home/                      # 一般ページ
│   │   ├── reit/                      # REIT詳細（70以上）
│   │   ├── members/                   # 会員関連
│   │   └── interview/                 # インタビュー記事
│   └── mailers/
│       └── notice_mailer.rb           # メール送信
├── config/
│   ├── routes.rb                      # ルーティング
│   └── database.yml                   # DB設定
└── db/
    ├── schema.rb                      # DBスキーマ
    └── seeds.rb                       # 初期データ
```

### 5.3 外部サービス連携

```
┌──────────────────────┐
│  外部フォーム         │  form.japan-private-reit.com
│  (HTML + JS)         │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Google Forms        │  回答を収集・保存
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Google Apps Script  │  reCAPTCHA検証 → Rails API → Slack通知
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  Rails (EC2)         │  会員登録・管理
│  japan-private-reit  │
│  .com                │
└──────────────────────┘
```

---

## 6. データベース構成

### 6.1 主要テーブル

| テーブル | 説明 |
|----------|------|
| `members` | 会員情報（名前、会社、メール、パスワード等） |
| `brands` | 私募REIT情報（70以上のブランド） |
| `member_interesting_reits` | 会員 ↔ REIT の中間テーブル（興味のあるREIT） |
| `interesting_contents` | 期待するコンテンツのマスタ（4種類） |
| `member_interesting_contents` | 会員 ↔ コンテンツ の中間テーブル |
| `logs` | アクセスログ |
| `inquiries` | お問い合わせ（現在無効） |

### 6.2 会員ステータス

| フィールド | 値 | 意味 |
|-----------|-----|------|
| `member_status_on` | 0 | 未承認 |
| `member_status_on` | 1 | 承認済み |
| `member_status_off` | 1 | 却下 |
| `admin` | 1 | 管理者 |

---

## 7. 本番環境情報

| 項目 | 値 |
|------|-----|
| URL | https://japan-private-reit.com |
| サーバー | AWS EC2 |
| ホスト | ec2-52-198-187-10.ap-northeast-1.compute.amazonaws.com |
| SSH ユーザー | ec2-user |
| SSH ポート | 22 |
| アプリケーションパス | /var/www/JapanPrivateReit/ |
| ログファイル | /var/www/JapanPrivateReit/log/production.log |
| DB名 | JapanPrivateReit_production |

---

## 8. 既知の問題と対応履歴

### 2025-11-26: CSRF検証エラー修正

**問題**: GASからの会員登録リクエストがCSRFトークンエラーで失敗

**原因**: GASからのPOSTリクエストにCSRFトークンが含まれていなかった

**対応**: `MembersController` に以下を追加
```ruby
skip_before_filter :verify_authenticity_token, :only => ['create']
```

**影響**: 2025年10月以降、12件以上の正規登録が失敗していた

---

## 9. 関連ドキュメント

- [会員登録データフロー](../.serena/memory/app-member-registration-data-flow-brands-interesting-contents-checkbox-post-data-many-to-many-relations.md)
- [GAS連携詳細](../.serena/memory/app-google-apps-script-automation-recaptcha-validation-rails-api-integration-slack-notification.md)
- [reCAPTCHA実装](../.serena/memory/form-recaptcha-google-forms-integration-external-registration-form-migration.md)
- [CSRF修正履歴](../.serena/memory/app-csrf-skip-fix-for-gas-registration-2025-11-26.md)
