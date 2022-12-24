---
title: "JupyterLabで決定木アルゴリズムをGraphvizで可視化する"
emoji: "😀"
type: "tech"
topics: ["20210522","jupyterlab"]
published: true
---
決定木アルゴリズムはロジックを可視化しやすいことがメリットです。
JupytarLabでもツリーの内容を可視化することができます。

しかし、可視化するためにはGraphvizというソフトを使用するのが一般的なのですが、
そのインストールに一手間かかるので手順を記載します。

* Graphvizをダウンロード
使用するためにはGraphiviz自体をWindowsにインストールする必要があります。
pipでもインストールできるみたいですが、venv等の仮想環境を使用している場合はいろいろ面倒なのでWindowsにインストール要です。

https://www.graphviz.org/download/


"not all tools and libraries are included"と記載されていますが、これだけできちんと動きました。

# Graphvizをインストール
設定は基本デフォルトでよいですが、1点だけ注意です。
"Install Options"でGraphvizを環境変数に追加しておくようにします。

![](/images/20210522_jupyterlab_graphviz/1.png)

* pipでGraphvizラッパーをインストール
最後にpipでGraphvizのラッパーをインストールします。
```sh
pip install graphviz
```

これでOKです。
