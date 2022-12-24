---
title: "Embeddable packageでPortableなPython環境を構築する"
emoji: "😀"
type: "tech"
topics: ["20211010","python"]
published: true
---
# はじめに
最近はPythonがポピュラーになってきており、簡単な便利ツールをPythonで作成することが多くなってきました。
そうすると自分以外の人がそのツールを使う機会も多くなってきますが、
一般的なPythonのデメリットとしてはPythonがインストールされている環境でないと動作しないということです。

解決策の一つとして、Pythonファイルをexe化してPythonがないPCでも動かせるようにすることがあります。
https://naonaorange.hatenablog.com/entry/2019/08/17/185150

しかしこの方法も万能ではなく、下記のような長所短所があります。

**長所**
- exeを実行するだけでよくなるため、だれでも使いやすい形式になる(複数pyファイルで動いているソフトも1つのexeにできるので使いやすい)
- ソースコードを隠し、技術流出や不用意な変更による不具合をなくすことができる

**短所**
- exe作成時や動作時にエラーが発生してしまうことがあり、エラー内容から解決策がわかりにくい
- ソフト動作時の処理速度が遅くなってしまうことがある

場合に応じて短所が無視できない時があります。
そのような場合は「環境を持ち運べるPortableなPython環境を作成し一緒に配布する」という解決方法もあります。
長所短所は、、、

**長所**
- pyinstallerでexe化したときの各種わかりにくいエラーが発生しない

**短所**
- 全体のファイルサイズが大きい
- 動作環境ごとにPython環境を準備しなければいけない場合がある。

この記事はこの方法のやり方を説明します。

# Windows embeddable packageをダウンロード/展開する
Python公式HPからダウンロードします。
https://www.python.org/downloads/release/python-389/
必要に応じて別のVersionをダウンロードしてください。

# Pythonが動作するかどうか確認する
展開した場所に移動し、python.exeを実行して動作することを確認してください。

# Pythonの設定をする
pythonXX._pthを変更します
```
# Uncomment to run site.main() automatically
import site         #<===(この行のコメントアウトを削除し有効にする)
```

# pipをインストールする
下記ファイルをダウンロードし、python.exeと同じ場所に置きます。
https://bootstrap.pypa.io/get-pip.py

その後pipをインストールします
```
.\python.exe get-pip.py
```

# pipを使う
Scriptsフォルダにpip.exeが作られるのでそれでpip installできます。
```
cd Scripts
.\pip.exe
```

# 参考
https://engineer-milione.com/programming/python-embeddable.html
https://qiita.com/rhene/items/68941aced93ccc9c3071

