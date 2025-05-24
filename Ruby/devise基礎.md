## 2025/4/6 deviseの復習
## ポートフォリオ制作に向けてdeviseの使い方、インストール方法を復習する。
## 手順

1. 何よりもまずGemの追加・bundleインストールを実施する。

    gem 'devise'の１行をGemファイルに追加し、bundle　installする。

2. rails g を行う。

    「rails g devise:install」をコマンド入力でインストール完了。

    rails g devise:installで以下の二種類のファイルが生成される。
``` ログ
Running via Spring preloader in process 11238
  create  config/initializers/devise.rb → Deviseの設定ファイル。この中でメールの設定やセッションの設定を行う。
  create  config/locales/devise.en.yml　→ エラーメッセージや日本語のローカライズなどを管理するファイル。
```
3. ユーザー情報テーブルを作成する。(モデルの作成)

    rails g devise User をコマンド入力。

    もし、テーブル名が異なる場合（Customerなど）は、そのテーブル名に合わせてモデルを作成すること！

    マイグレーションファイルにカラム追加の上、rails db:migrate

## モデル解説
#### deviseのモデルを作成すると以下の機能定義が記載される。
```
  :database_authenticatable（パスワードの正確性を検証）
  :registerable（ユーザ登録や編集、削除）
  :recoverable（パスワードをリセット）
  :rememberable（ログイン情報を保存）
  :validatable（email のフォーマットなどのバリデーション）
```

## 注意点
#### ・デフォルトで生成されるマイグレーションファイルには名前のカラムがないので、db　migrateする前にカラム追加してから実施する必要あり。

## 複数権限（name spaceでadminとPublicに分ける）の場合
#### gemのインストールは大前提
1. 各権限用のモデルを準備する
```
rails generate devise User
rails generate devise Admin
rails db:migrate でマイグレート。
```

2. ルーティングの設定（⭐︎ここが重要）
```
publicの設定例:
  scope module: 'public' do
    resource :users
  end
  → こう書くと、
      コントローラ: Public::UsersController
      ビュー: app/views/public/users/
    というルーティングになる。
```

```
deviseのルーティング例：
  devise_for :users, controllers: {
    # public名前空間に作ったオリジナル（カスタム）コントローラに差し替え設定をする
    registrations: 'public/registrations',
    sessions: 'public/sessions',
  }
```

####    カスタムコントローラを使う理由：
  * deviseのデフォルトの挙動を変えたい・追加したい場合に処理をするため。
  * devise以外の他のコントローラと構成を統一したい場合（今回はこの理由が大きい）
  今回はpublic/~ とadmin/~で明示的に名前空間を分けたいので、deviseもこの運用に揃える。
  こうすると明示的に見やすい＆管理しやすい

3. ビューの分離
#### 必要なビューを仕様に応じて、管理者とPublicユーザー用にそれぞれDeviseビューを用意する
```
例）
app/views/admin/sessions/new.html.erb
app/views/users/sessions/new.html.erb
```

4. アクセス制御の設定
#### Admin/Publicの処理用コントローラに以下のようにそれぞれ書くことで、権限を分離する。
```
管理者用コントローラー：
before_action :authenticate_admin!
```

```
Public用コントローラー：
before_action :authenticate_user!
```

#### 特殊ケース: ゲストユーザーログイン等のルーティングはこちら
```
devise_scope :user do
  post '/guest_sign_in', to: 'public/sessions#guest_sign_in'
end
```