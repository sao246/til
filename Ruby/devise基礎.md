# 2025/4/6 deviseの復習
## ポートフォリオ制作に向けてdeviseの使い方、インストール方法を復習する。
## 手順
### 1. 何よりもまずGemの追加・bundleインストールを実施する。
#### gem 'devise'の１行をGemファイルに追加し、bundle　install
### 2. rails　g　を行う。
#### 「rails g devise:install」をコマンド入力でインストール完了。
#### rails g devise:installで以下の二種類のファイルが生成される。
#### Running via Spring preloader in process 11238
####      create  config/initializers/devise.rb → Deviseの設定ファイル。この中でメールの設定やセッションの設定を行う。
####      create  config/locales/devise.en.yml　→ エラーメッセージや日本語のローカライズなどを管理するファイル。
### 3. ユーザー情報テーブルを作成する。(モデルの作成)
#### rails g devise User をコマンド入力。
#### もし、テーブル名が異なる場合（Customerなど）は、そのテーブル名に合わせてモデルを作成すること！
#### マイグレーションファイルにカラム追加の上、rails db:migrate

## モデル解説
#### deviseのモデルを作成すると以下の機能定義が記載される。
#### :database_authenticatable（パスワードの正確性を検証）
#### :registerable（ユーザ登録や編集、削除）
#### :recoverable（パスワードをリセット）
#### :rememberable（ログイン情報を保存）
#### :validatable（email のフォーマットなどのバリデーション）

## 注意点
#### ・デフォルトで生成されるマイグレーションファイルには名前のカラムがないので、dbmigrateする前にカラム追加してから実施する必要あり。

## 複数権限（name spaceでadminとPublicに分ける）の場合