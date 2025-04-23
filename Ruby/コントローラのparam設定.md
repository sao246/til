## 2025/4/9 コントローラのパラメータ設定について
## 実現したいこと：
### @post = Post.find(params[:id])のように、parameterを私て変数設定し、特定のデータ1件を取得する処理をまとめて１行に書くことで各メソッド（showメソッドやedit,update,destroy）には書かなくても処理ができるようにする。

## 書き方
### １）　コントローラの先頭に記載：
#### before_action :set_post, only: [:show, :edit, :update, :destroy]
#### ※ここにはパラメータの取得をしたいメソッドを指定する。[]内

### ２）　private　メソッドにパラメータ取得のメソッドを記載する。
####  private
####    def set_post
####      @post = Post.find(params[:id])
####    end
####
####    def post_params
####      params.require(:post).permit(:title, :body, :image)
####    end