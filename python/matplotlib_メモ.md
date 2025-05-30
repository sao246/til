### matplotlib復習 2025/05/30

-------------------------------
### matplotlibとは
#### pandasと同じく、Pythonのライブラリの一つで、グラフ描画をするために使われる。科学技術計算やデータ分析などで、データの可視化によく使われる。
#### 使い方 ： pyplotモジュールを使うことで、手軽にグラフを描ける。そのために、「import matplotlib.pyplot as plt」でインポートを行う。
#### pyplotとは： matplotlibライブラリの中にある、サブモジュール。matplotlib.pyplotとして提供されている。グラフを作るための便利な関数を集めたツールを指す。グラフを描画するためのコーディングを段階的に行えるのでわかりやすい。
#### 描画例：
```
import matplotlib.pyplot as plt

x = [1, 2, 3, 4]
y = [10, 20, 15, 25]

plt.plot(x, y)           # グラフの描画
plt.xlabel('X軸')        # X軸ラベル
plt.ylabel('Y軸')        # Y軸ラベル
plt.title('サンプル')    # グラフタイトル
plt.show()               # グラフを表示
```
-------------------------------
### ヒストグラムの書き方：
```
# 身長というデータのヒストグラムグラフを作る。
import numpy as np
import pandas as pd

data = pd.DataFrame()
np.random.seed(0)
data['身長'] = np.random.normal(170, 5, 100)
data['身長'].hist()
```
### 棒線グラフの書き方：
```
import numpy as np
import pandas as pd
data = pd.DataFrame()
np.random.seed(0)
data['体重'] = np.random.normal(60, 3, 100)
plt.plot(data['体重'])
```