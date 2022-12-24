---
title: "JupyterLabでデバッグ機能を使ってみる"
emoji: "😀"
type: "tech"
topics: ["20210406","jupyterlab"]
published: true
---
JupyterLab v3.0からブレイクポイントを使ってデバッグできるようになりました。
これまではprintfで動作確認を行っていましたが、この機能を使えば変数の値を確認しながらステップ実行ができてとても便利そうです。
https://www.publickey1.jp/blog/20/jupyterlab.html

この記事ではJupyterLabのデバッグ機能を使用する方法を説明していきます。
特に初回の有効設定は何ステップか必要なことに加え、ネットの情報ではあまりできなかったため記録に残していきます。

# 動作環境
- Windows10 64bit
- Python 3.8 64bit
- Pipenvでの仮想環境(venv)を使用

# 初期設定
## JupyterLabインストール
```sh
pip install jupyterlab
```

## xeus-pythonインストール
```sh
pip install xeus-python
```

## kernel.jsonの編集
"\venv\share\jupyter\kernels\xpython\kernel.json"を開き、下記変更を行う
```
変更前
... "argv": [ "python.exe", "-m", "xpython_launcher", "-f", "{connection_file}" ], ...

変更後
... "argv": [ "python", "-m", "xpython_launcher", "-f", "{connection_file}" ], ...
```

これは後段でxpythonを有効にしたときに"No module named xpython_launcher"というエラーが出ることへの対策です。
xeus-pythonのGithub issueに挙げられており、記事作成時点ではOpenのままです。
現時点ではこの変更が必要ですが、ゆくゆくはいらなくなるかもしれません。
https://github.com/jupyter-xeus/xeus-python/issues/382

# 有効設定方法
設定方法は下のGIFを見て確認してみてください。
![](/images/20210406_jupyterlab_debug/1.gif)

# トラブルシューティング
## xeus-pythonインストール時にpkg_resources.VersionConflictでエラーになる
本環境ではエラーになりませんでしたが、Python 3.7でxeus-pythonをインストールすると下記のエラーが出ます。
`
pkg_resources.VersionConflict: (importlib-metadata 3.10.0 (c:\users\naolu\documents\python\jupyter37\.venv\lib\site-packages), Requirement.parse('importlib-metadata<2,>=0.12; python_version < "3.8"'))
`

xeus-pythonをインストールすると依存関係よりimportlib-metadataというパッケージがインストールされるようですが、
このパッケージはPython 3.8からpipでのインストール可能になったようです。
(Githubリポジトリに記載されていました。)
pipに頼らずに手動でインストールすればできるかもしれないですが、
私はPython 3.8に移行してこのエラーを回避しました。
https://github.com/python/importlib_metadata

