---
title: "pyenvの仮想環境下でpybluezをインストール"
emoji: "😀"
type: "tech"
topics: ["20200129","python"]
published: true
---
タイトルのようにpyenv仮想環境下でpybluezをインストールしようとしたのですが、
色々躓いたことがあったので、メモします。

環境はUbuntu18.04 64bit、Python3.6です。
また、pyenvによる仮想環境作成方法は省略します。
まだの方は下記のURLを参考に作成してください。
https://naonaorange.hatenablog.com/entry/pyenv%E3%81%A8pyenv-virtualenv%E3%81%AE%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB


# 必要なソフトウェアをインストール
今回試行錯誤したので、不要なものや重複しているものがあると思いますが、インストールします。
```
sudo apt install libboost-python-dev libboost-thread-dev libbluetooth-dev python-dev libbluetooth3 libboost-all-dev
```

# pybluez, gattlibをインストール
```
pip install pybluez gattlib
```

### "/usr/bin/ld: -lboost_python が見つかりません"とエラーが出たら？
gattlibをインストールすると、"/usr/bin/ld: -lboost_python36 が見つかりません"というようなエラーが出てしまった場合は、
libboost_python36.soというファイルをシンボリックリンクで作成することで解決できます。
```
sudo ln -s /usr/lib/x86_64-linux-gnu/libboost_python3-py36.so /usr/lib/x86_64-linux-gnu/libboost_python36.so
```

# pybluezサンプルを実行
pybluezのgithubからサンプルプログラムをコピーして、実際に動くかどうか確認します。
```
git https://github.com/pybluez/pybluez.git
cd pybluez/examples/ble
```

そして、beacon.pyを実行してみます。
まずは単純に実行してみると、、、”RuntimeError: Can't send hci request”というエラーが出てしまいました。
```
python3 beacon.py

Traceback (most recent call last):
  File "beacon.py", line 14, in <module>
    1, 1, 1, 200)
RuntimeError: Can't send hci request
```
これは、BLEデバイスにアクセスするには管理者権限が必要なためです。よって、素直に管理者権限をつけて、再度実行します。
次は、"ModuleNotFoundError: No module named 'gattlib'"となってしまいました。

ログに表示されているpythonのpathを見ると、/usr/local/bin/python3.6となっています。
管理者権限で実行したことによって、pyenv下のpython3.6ではなく、OSでインストールされているpython3.6が使用されてしまっているみたいです。
```
sudo python3 beacon.py

Traceback (most recent call last):
  File "beacon.py", line 9, in <module>
    from bluetooth.ble import BeaconService
  File "/usr/local/lib/python3.6/dist-packages/bluetooth/ble.py", line 1, in <module>
    from gattlib import *
ModuleNotFoundError: No module named 'gattlib'
```

よって、pyenv下のpython3.6のpathを絶対PATHで指定して実行します。
しかし、エラーの内容は変わりません。
たぶん、pyenv下のpython3.6を使用していても、userの環境変数が受け継がれていないためgattlibを見つけられていないのではないかと思います。
```
sudo ~/.pyenv/shims/python3 beacon.py

Traceback (most recent call last):
  File "beacon.py", line 9, in <module>
    from bluetooth.ble import BeaconService
  File "/usr/local/lib/python3.6/dist-packages/bluetooth/ble.py", line 1, in <module>
    from gattlib import *
ModuleNotFoundError: No module named 'gattlib'
```

よって、userの環境変数を受け継ぐようにして実行します。
私の環境ではこれでうまく行きました。
```
sudo -E ~/.pyenv/shims/python3 beacon.py
```

問題としては2つです。下の方はまあ当たり前のことですね。
- pybluezは管理者権限で実行する必要があること
- 管理者権限で実行した際にはpyenv下ではなく、OS上のpythonが実行されてしまうこと

毎度このように実行するのは非常に面倒ですが、まずはこれで対応していきたいと思います。

