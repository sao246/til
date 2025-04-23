## 2025/4/8 モデル作成の復習
### ポートフォリオ制作に向けてindex、外部キー項目があるテーブルの作成を復習する。
### 手順:
#### 1. 外部キーを追加	t.references :user, foreign_key: true
#### 2. インデックスを追加	index: true（referencesなら自動）
#### 3. モデル作成と同時に外部キー追加	rails g model Post user:references
#### 外部キーを設定すると、Indexは自動付与されるので、あえてマイグレーションで設定する必要はなし。
