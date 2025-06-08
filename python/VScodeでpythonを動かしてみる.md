## 2025/6/5 VScode上でpythonのコーディング練習をする。
### DMMの勉強ではGoogle colabを使ってきたが、VS Codeでの環境構築・コーディングに挑戦する。
-----------------------
### 手順
```
1. Homebrewを使って自分のMacにPythonをインストールする。
2. インストールしたPythonとpipのバージョンを確認しておく。
3. VS Codeで新しいプロジェクトを作成する。(VS Codeがインストールされている前提)
4. cd　で新しいプロジェクトのルートディレクトリへ移動
5. python -m venv venvをターミナルで実行。（Pythonの仮想環境を作る(*1)）
6. source venv/bin/activateで仮想環境を有効化。
7. which pythonでpythonを参照しているパスを確認する。また、python --versionでバージョンも確認。
```
#### *1: ここで仮想環境とは…？
```
他のプロジェクトとパッケージを分離して安全に使えるようにする
自分だけの「Pythonの箱庭」を作るイメージ。
プロジェクトごとにバージョンの違いを受けることがあるらしく、こういった仮想環境を作るのが一般的と理解。
```
-----------------------
### 用語
#### pip:
Pythonのパッケージ管理ツール（パッケージマネージャー）。Pythonで機能拡張をしたい場合には必須のツールになる。HomebrewのPythonにはセットで入っているものだが、Pythonインストール時に入っていることを確認する。
#### venv:
Pythonの仮想環境を作るためのコマンド。
```
python -m venv [仮想環境名]
```
-----------------------
#### 発生した問題：
venvで仮想環境を作成しようとしたところ、エラーが発生。
```
/usr/bin/python: No module named venv
```
#### エラーの意味：venvがvscodeで動かそうとしているPythonに存在しないと怒られている。

#### 対策：pythonのバージョンが古いことが原因の可能性あり、バージョン・インストール状況を確認してみる。元々Macに入っているPythonのバージョンは3.7だったが、venvが安定的に動くのは、HomebrewのPython3（バージョン3.12系）らしく、Homebrewでインストールしてみることに。

#### インストール
```
〜Mac のターミナルで実行。〜
brew install python
```

#### 再び問題発生🔥
#### python --versionでPythonのバージョンを確認したが、思った通りのバージョンではないことが判明（Python 3.9.6）
#### これはアップグレードで解決。
-----------------------
#### 結果、、これでもうまくいかず！！
#### しかし、それでもvenvがうまくいかず、原因は、VScodeが見ているPythonがHomebrewのPythonではない可能性あり、パスの確認をすることに。パスをみたところ、パスは合っていた。
#### ではなぜうまくいかないか？：　調べると、Intel版のHomebrew Pythonと、Apple Silicon版のPythonというのがあるらしい！今回先にインストールしているのはIntelだったので、MacのOSがM3の場合、これだとうまくいかないらしい
#### そのためApple Silicon版を再度インストール　
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
#### ※Apple Silicon版と、Intel版が両方入っていると何か悪さしないかも調べてみたが、とりあえず、両方が入っていること自体は問題にならないらしい。（環境変数でどちらをみるか決まるから！）
```
✏️注意点： 今インストールしたPythonがどちらかわからない場合は、Macのターミナルでwhich brewを実行し、出てきたパスを確認する！
Intel Mac向けは /usr/local にインストールされる
Apple Silicon向けは /opt/homebrew にインストールされる
```

export PATH="/opt/homebrew/bin:$PATH"を、環境変数に書き込む必要あり。.zshrcファイルに１行追加！

#### メモ：環境変数の場所を調べるには？　ls -a ~ | grep zshまたはls -a ~ | grep bashを実行し、.zshrcといったファイルがないかを確認。今回は、.zshrcだったので、中身を見ていく。
```
参考：代表的なシェルの環境変数設定ファイル例
zsh（macOSのデフォルトシェル）
　~/.zshrc
　~/.zprofile
　~/.zshenv
　~/.zlogin
bash
　~/.bash_profile
　~/.bashrc
　~/.profile
```

#### 結果、、これでもうまくいかず！！！（2回目）
#### 色々な仮定をもとに原因調査🔥
* HomebrewのPythonはApple Silicon版なら /opt/homebrew/bin/python3 にあるはず
* しかし、現状は「which python3」 すると、 /usr/local/bin/python3 が優先されている
➡️ つまり、PATH環境変数上で /usr/local/bin が /opt/homebrew/bin より優先されているか、そもそも /opt/homebrew/bin のPython3が存在していない可能性というのがわかってきた。

#### Python3は/opt/homebrew/binには存在しているので、まだパスがおかしいと思い、パスの並びを変更。
#### /opt/homebrew/binが、/usr/local/binの下にあったので、これを並び替えて、which python3を再度実施すると、パスが読み変わり/opt/homebrew/bin に！🎉

#### この後、　python3 -m venv venv　と　source venv/bin/activate　を実行し、Python仮想環境の作成が無事完了🌸
#### ターミナルの表示に(venv) という表示が出るように🌸（これで完了）