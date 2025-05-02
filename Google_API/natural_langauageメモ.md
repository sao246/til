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
#### 1. まずは　Google Cloudアカウントを登録する。（一旦無料枠で利用する）
#### 2. APIキーを取得する（プロジェクトを作成し、キーを取得する。）
#### ※取得したキーは他の人に漏れないように管理する！
#### ------ここまでは各API共通作業になる------
#### 3. 利用するAPIの有効化（ブラウザでクラウドにログインし、Natural Language APIの有効化ボタンを押せばOK）
#### 4. envファイルを使って利用するGoogle Cloudプロジェクトのキーを設定（CloudとEC2開発環境間での通信に必要）
__envファイルの　gitignoreを忘れない!__
#### ※envファイルの設定がJSONでGoogle APIにアクセスするための大前提になっている。

#### 5. Google APIと通信するためのRailsプログラムを作成する。
#### 作成方法：
| ディレクトリ             | 向いているケース       |
| ------------------ | --------------- |
| `lib/`             | 小規模。Railsに依存しない。Railsのプログラムに依存しない機能に向いてる（汎用的、使い回しきく） |
| `app/services/`    | 明確な役割の単一処理。 Railsらしい構造、保守しやすい。RSpecのテスト向き |
| `app/interactors/` | 手続きの流れがある処理（複数のステップがある業務処理など。）|

#### →今回はカリキュラムに沿ってlib/の下にファイルを作って作成する。

## 2025/05/02 続き
## 書き方
1. API接続に必要なRubyのライブラリを読み込む。
####
&nbsp;&nbsp;&nbsp;require 'base64'
&nbsp;&nbsp;&nbsp;
#### require 'json'
#### require 'net/https'

#### 💡これらのAPIが必要な理由

| ライブラリ|なぜ必要か？|Google API仕様記載箇所|
| ----------- | ----------- | ---------------------- |
| net/https | GoogleのAPIは **HTTPS通信** を使ってアクセスする必要があるから| **APIのURLの形式（[https://...）に記載あり](https://...）に記載あり)** |
|json| Googleに送るデータや、返ってくるデータは **JSON形式** だから | **リクエストボディの例やレスポンスがJSONで示されている**|
|base64| ※今回は使ってないが、画像やバイナリを送る時に **Base64にエンコードして送る必要がある** ため。(VisionAPIなど)|**画像解析APIなどの仕様書に記載あり（Base64形式で送信）**|
### HTTPS、JSONについてはAPI全般に一般的にこれらが必要という理解。
### Base64は状況に応じて必要になると覚えておく。


module Language
  class << self
    def get_data(text)
      # APIのURL作成
      api_url = "https://language.googleapis.com/v1/documents:analyzeSentiment?key=#{ENV['GOOGLE_API_KEY']}"
      # APIリクエスト用のJSONパラメータ
      params = {
        document: {
          type: 'PLAIN_TEXT',
          content: text
        }
      }.to_json
      # Google Cloud Natural Language APIにリクエスト
      uri = URI.parse(api_url)
      https = Net::HTTP.new(uri.host, uri.port)
      https.use_ssl = true
      request = Net::HTTP::Post.new(uri.request_uri)
      request['Content-Type'] = 'application/json'
      response = https.request(request, params)
      # APIレスポンス出力
      response_body = JSON.parse(response.body)
      if (error = response_body['error']).present?
        raise error['message']
      else
        response_body['documentSentiment']['score']
      end  
    end
  end
end