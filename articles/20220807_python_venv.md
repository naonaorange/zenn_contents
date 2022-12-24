---
title: "pythonデフォルトの仮想環境venvを使う"
emoji: "😀"
type: "tech"
topics: ["20220807","python"]
published: true
---
最近はpythonを色々な分野で使用することが多くなりました。
その時に便利なのがpythonの仮想環境です。
今回はデフォルトで使用できるvenvのよく使うコマンドを記載します。
環境はmacを想定しています。

# 新しい仮想環境の作成
```sh
python -m venv XXX
```

# 仮想環境の有効化
```sh
source /XXX/bin/active
```

# 仮想環境の無効化
```sh
deactivate
```