## numpy復習 2025/05/13

## numpyとは
#### pandasと同じく、pythonのライブラリの一つ。（高速な計算を行うためのツール・便利機能のようなもの）。もう少し詳しく説明すると、
* 行列やベクトルを手軽に扱うことができる計算ツール
* Pythonで数学やデータ処理をする場合の基礎ライブラリ
#### という位置付け。

## 機械学習でのnumpyの使われ方
#### 前段にもあるように、機械学習で必要な、行列計算やベクトルの計算で必須となってくる。また、大量データを省メモリで扱えると言う点でnumpyが必要となってくる。また、他のpythonライブラリ（pandasなども！）の土台となっている点でも重要！

#### 重要な計算の場面：
* 線形回帰、ロジスティック回帰 → 重みと特徴量の内積
* ニューラルネットワーク → 行列の積や活性化関数の処理
* 勾配降下法などの最適化アルゴリズム → ベクトルの差・勾配計算
* 特徴量を算出する際の前処理→ 正規化・標準化（平均・分散の計算）、欠損値処理（平均で埋める、など）、データのシャッフルや分割

## 文法まとめ
#### 下記の「np」はある配列の変数という前提で説明。
| 書き方 | 意味 | 例 | 目的 |
|---|---|---|---|
| np.zeros | 全て値が0のaxb配列| np.zeros((2,3)) |モデルの初期化：ニューラルネットワークの重みを初期化する際に使うことが多い（例：重み行列）。|
| np.mean | 平均値を求める | np.mean(a) |	特徴量の標準化：データの特徴量を平均0、標準偏差1にスケーリングする際に、平均値を求めて使用。|
| np.sum | 合計値を求める | np.sum(a) |	損失関数の計算：クロスエントロピー損失やその他の損失関数で、合計値を求めて最適化に利用。|
| np.shape | 配列の形（サイズ）を求める | np.zeros((2,3)) |	データの確認：データセットの次元を確認したり、モデルに入力する前にデータの形が正しいか確認するために使用。|
| np.dim | 行列の軸数（次元）を求める（１〜３次元？） | npが[[1,2],[3,4]]ならば、2次元配列なので、2を返す。|	データの次元確認：特徴量がどのような形で格納されているかを確認する際に利用。例えば、1次元が特徴量、2次元が画像データ。|
| | | | |


* .shapeと.dimの違い
#### shapeは配列のサイズ感を掴む（何行何列ある行列か？？）一方で、ndimは軸（axis）を示す。例えば、２次元配列なら、axis=0,axis=1