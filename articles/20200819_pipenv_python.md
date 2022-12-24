---
title: "pyコマンドとpipenvを使ったWindowsでのPython仮想環境構築"
emoji: "😀"
type: "tech"
topics: ["20200819","python"]
published: true
---
Pythonでの仮想環境構築はツールが色々あるが故に様々な方法があるのでわかりにくいです。
今まではAnacondaをインストールしてcondaコマンドを使っていたのですが、今回色々調べた結果をまとめます。
簡潔に説明すると下記になります。

- Pythonは公式からVanilla Pythonをインストールする

- Pythonバージョン管理はpyコマンドで行う

- Pythonのパッケージ管理はpipenvコマンドで行う

# pyコマンドを使ったPythonバージョン管理
WindowsではPythonをインストールするとPowershellでpyコマンドを使えるようになります。
最新のバージョンのインタプリタを起動したい場合は下記コマンドを実行してください。
```
py -m
```
複数バージョンのPythonがインストールしてある場合には、パージョンを明示することもできます。
前まではpython.exeがあるフォルダにPATHを通さないといけなかったのですが、pyコマンドを使えばそんなことをしなくても良いので楽ですね。
```
py -3.6
```

# pipenvを使ったパッケージ管理
pipenvのインストールはpipで行います。
```
py -m pip install pipenv
```
pipenvはデフォルトで仮想環境をユーザーディレクトリに作成します。
仮想環境をプロジェクトディレクトリに作成したい場合は環境変数に下を追加してください。
```
set PIPENV_VENV_IN_PROJECT=1
```
使い方はhelpオプションをみると大体わかるのですが、良く使いそうなものだけ説明します。

## 仮想環境の作成
まず、作成したい仮想環境の設定を行います。
```
py -m pipenv --three    #python3の最新版で設定
py -m pipenv --two     #python2の最新版で設定
py -m pipenv --3.7     #python3.7で設定
py -m pipenv install     #最新版で初期化＆作成
```
次に仮想環境の作成を行います。
設定の際にpy -m pipenv installを行っている場合は再度実行する必要はありません。
```
py -m pipenv install
```

## 仮想環境の削除
```
py -m pipenv --rm
```

## 仮想環境の有効
```
py -m pipenv shell
```

## 仮想環境のPATHを確認
現在どの仮想環境が有効になっているか確認するためにPATHを確認できます。
```
py -m pipenv --venv
```

## ソフトウェアのインストール
```
py -m pipenv intall numpy
```
