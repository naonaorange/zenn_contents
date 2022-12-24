---
title: "WSLで複数のUbuntuを作って使い分けたい"
emoji: "😀"
type: "tech"
topics: ["20210523","wsl"]
published: true
---
WSLでUbuntuを使っているときに、「同じUbuntuバージョンを複数使い分けたい！」と思ったことはありませんか？
Windows StoreにはUbuntu18.04とUbuntu20.04が別々に存在しているので、それらは別々にインストールすることができますが、
Ubuntu18.04を複数個使いたいと思ったときにも別々にインストールして使い分けることができます。
本記事ではその方法を記載していきます。

# Windows StoreでUbuntuをインストール
まずは元になるUbuntuをWidnwos Storeからインストールします。
これはいつものやり方でOKです。
インストール後は初回起動だけ行い、ユーザー名やパスワードを設定してください。
この状態でwslコマンドをたたくときちんとUbuntu 18.04がインストールされたことがわかります。
```sh
wsl --list --verbose
  NAME            STATE           VERSION
* Ubuntu-18.04    Stopped         2
```

# Ubuntuの環境をファイルにエクスポート
ベースとなるUbuntuの環境をファイルにエクスポートします。
```sh
wsl --export Ubuntu-18.04 Ubuntu_18_04.tar
```

# ファイルからUbuntuの環境をエクスポート
次にファイルをからエクスポートします。
Ubuntu1804_2というフォルダが作られるので、適宜フォルダを決めてからコマンドを実行してください。
Ubuntuの環境自体はこれで何個も複製できます。
Windows Terminalで複製したUbuntuが表示されるため、簡単に起動できます。
```sh
wsl --import Ubu1804_2 Ubu1804_2 Ubuntu_18_04.tar
```

# 複製したUbuntuに一般ユーザーでログインする
Windows Terminalでログインしてみるとなぜかルートでログインされています。
いつものように一般ユーザーでログインする場合には下記対応が必要です。

Windows Terminalを管理者権限で実行してタブ周辺のプルダウンをクリック->設定を開きます。
複製したUbuntuのプロファイルを開き下記のように変更します。
これで指定したユーザー名でログインできます
```sh
#変更前
wsl.exe -d Ubu1804_2

#変更後
wsl.exe -d Ubu1804_2 -u <user name>
```

# 複製したUbuntuの削除方法
```sh
wsl --unregister Ubu1804_2
```
