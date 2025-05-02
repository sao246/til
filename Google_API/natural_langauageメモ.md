## Google API natural languageの学習メモ
#### 2025/05/01
#### DMMのカリキュラムに沿ってGoogle Cloudの「Natural Language(NLP)」というAPIを使った実装を学習。
* Google Cloud とは : visionメモに記載したので割愛。
* Natural Language API とは : Google APIの一つで、テキストの意味や感情を分析するAI機能。
#### できること例）　
* 投稿やコメントの感情分析（数値で表す。マイナスxxならネガティブ、プラスxxならポジティブなど）
* 内容の要点抽出
* カテゴリ分類
* 特定ワードの抽出（例：アオモリンゴラボのサイト開発なら「りんご」「農家」などのピックアップも可能になる。）

## 実装準備
1. まずは　Google Cloudアカウントを登録する。（一旦無料枠で利用する）
2. APIキーを取得する（プロジェクトを作成し、キーを取得する。）※取得したキーは他の人に漏れないように管理する！
#### ------ここまでは各API共通作業になる------
3. 利用するAPIの有効化（ブラウザでクラウドにログインし、Natural Language APIの有効化ボタンを押せばOK）
4. envファイルを使って利用するGoogle Cloudプロジェクトのキーを設定（CloudとEC2開発環境間での通信に必要）
__envファイルの　gitignoreを忘れない!__※envファイルの設定がJSONでGoogle APIにアクセスするための大前提になっている。

5. Google APIと通信するためのRailsプログラムを作成する。
## プログラム作成方法:
| ディレクトリ             | 向いているケース       |
| ------------------ | --------------- |
| `lib/`             | 小規模。Railsに依存しない。Railsのプログラムに依存しない機能に向いてる（汎用的、使い回しきく） |
| `app/services/`    | 明確な役割の単一処理。 Railsらしい構造、保守しやすい。RSpecのテスト向き |
| `app/interactors/` | 手続きの流れがある処理（複数のステップがある業務処理など。）|
#### →今回はカリキュラムに沿ってlib/の下にファイルを作って作成する。

## 2025/05/02 続き
## 書き方詳細:

1. API接続に必要なRubyのライブラリを読み込む。
#### 今回は以下3点。Google APIの仕様書を確認しながら、なんのライブラリが必要かを確認して、コーディングする。
| |
|------|
|require 'base64'|
|require 'json'|
|require 'net/https'|

#### 💡これらのAPIが必要な理由
| ライブラリ|なぜ必要か？|Google API仕様記載箇所|
| ----------- | ----------- | ---------------------- |
| net/https | GoogleのAPIは **HTTPS通信** を使ってアクセスする必要があるから| **APIのURLの形式（[https://...）に記載あり](https://...）に記載あり)** |
|json| Googleに送るデータや、返ってくるデータは **JSON形式** だから | **リクエストボディの例やレスポンスがJSONで示されている**|
|base64| ※今回は使ってないが、画像やバイナリを送る時に **Base64にエンコードして送る必要がある** ため。(VisionAPIなど)|**画像解析APIなどの仕様書に記載あり（Base64形式で送信）**|
#### HTTPS、JSONについてはAPI全般に一般的にこれらが必要という理解。Base64は状況に応じて必要になると覚えておく。

2. アプリでの「言語解析」というカテゴリで、共通機能をひとまとめにするために、「module」という単位で機能を集約する。（　モジュール名が、language。）プログラム構成で言うと一番上位階層になる。

3. モジュールに含まれる今回の処理メソッドを定義する。（ここでは　def get_data(text)）クラスメソッドになるので、「class << self」と言う書き方をする。

4. get_data(text)メソッドの中身を記載。
#### &nbsp;&nbsp;&nbsp;🌼エンドポイントのURLを指定する。　
      api_url = "https://language.googleapis.com/v1/documents:analyzeSentiment?key=#{ENV['GOOGLE_API_KEY']}"

| 記載箇所 | 説明 |
| ------ | ---- |
| https://language.googleapis.com/v1/documents:analyzeSentiment | GoogleのAPIのエンドポイント（URL）|
| ?key=#{ENV['GOOGLE_API_KEY']}| クエリパラメータ（一番最初にGoogle Cloudでキーを作った際に.envファイルに記載したデータとリンクする）|
#### エンドポイントURLは使うAPIによって変わるので、都度調べる。今回は文章の感情分析を行うためのパスで記載。

#### &nbsp;&nbsp;&nbsp;🌼APIリクエスト用のJSONデータを作成する。

      params = { // パラメータ設定囲う
        document: { // 送るデータが文書である
          type: 'PLAIN_TEXT',  // プレーンテキスト（平文）であることを記載
          content: text // 実際に分析を行うデータが入っている引数はここで指定！
        }
      }.to_json // JSON形式で渡さないといけないので、ここで変換処理。

#### &nbsp;&nbsp;&nbsp;🌼リクエストデータ部分の作成
      uri = URI.parse(api_url)
      https = Net::HTTP.new(uri.host, uri.port)
      https.use_ssl = true
      request = Net::HTTP::Post.new(uri.request_uri)
      request['Content-Type'] = 'application/json'
      response = https.request(request, params)

#### &nbsp;&nbsp;&nbsp;🌼受け取ったAPIレスポンスデータを出力させる
      response_body = JSON.parse(response.body)
      if (error = response_body['error']).present?
        raise error['message']
      else
        response_body['documentSentiment']['score']
      end  

## リクエストデータの考え方：
| 手順 | コード                                            | イメージ                     |
| ------ | ---------------------------------------------- | ------------------------ |
| URL分解 | `uri = URI.parse(api_url)`                     | 送信先のGoogleの住所を確認するために、URLを分解 |
| HTTP接続の設定 | `https = Net::HTTP.new(...)`                   | 郵便の手配（どこに出すか設定）          |
| SSL認証の設定 | `https.use_ssl = true`                         | 安全に送る（暗号化＝セキュリティ対策）      |
| POSTリクエストの作成（API接続にもHTTPの設定が必要） | `request = Net::HTTP::Post.new(...)`           | 手紙を書く（「POST＝お願いします」）     |
| JSON形式で送ることを設定 | `request['Content-Type'] = 'application/json'` | 手紙の形式を「JSONです」と指定        |
| リクエスト送信 | `response = https.request(...)`                | 郵便で送る（リクエストを実際に送る）       |

## URIとは
### Uniform Resource Identifierの略
インターネット上のリソースの場所を一意に示すための記述ルール。URLはリソース自体の場所を識別 <--> URIはリソースの場所を識別するが、URLやURNよりも広義の意味になる。URLやURNがURIに含まれる。URLはただの文字羅列でそのままだと、ポート番号等の情報（URIを分解することで得られる）は見つけることができないので、uri.perseが必要。

## URIの分解とは
| プロパティ        | 変換後の例                     | プロパティの意味                     |
| ------------ | --------------------- | ---------------------- |
| `uri.scheme` | `"https"`             | 通信プロトコル（http or https） |
| `uri.host`   | `"example.com"`       | サーバーのホスト名（ドメイン名）       |
| `uri.port`   | `443`                 | 通信ポート番号（httpsは通常443）   |
| `uri.path`   | `"/path/to/resource"` | リソースのパス（APIのエンドポイント）   |
| `uri.query`  | `"key=abc123"`        | クエリパラメータ（GETで使う情報）     |

### Rails上で、上記のように書けばなぜURIの中身を変換できるのか？　
→　Railsの標準ライブラリ機能「URI」があるから。URI.perseとやれば、上記のようにURL全体を使えるオブジェクトに分割して変換ができる。
分解することで初めて、後続のドメインやポート指定がしやすくなる。

