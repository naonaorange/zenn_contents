---
title: "Sens'it SDKを使ってSens'it V3用のソフトウェアを作成する"
emoji: "😀"
type: "tech"
topics: ["20220508","sigfox"]
published: true
---
Sens'itは購入時にサンプルプログラムが書き込まれており、モードに応じて各種センサ値を周期送信できます。
定期的にデータを吸い上げるだけであればそのまま使うこともできると思いますが、今回はSens'it SDKを使ってソフトウェアを作成していきます。

作成する理由としてはSigfoxの通信フォーマットを自分で設定したいためです。
サンプルプログラムでは通信フォーマットが決められてしまっていますが、SDKを使えば自分で好きなように設定することができます。
デフォルトの通信フォーマットは下記で確認できます。
https://d2air1d4eqhwg2.cloudfront.net/media/files/1db11f7e-1be2-4146-91d5-0321aa9c8120.pdf

# 環境構築
下記ソフトウェアをインストールしていきます。
- GNU Arm Embedded Toolchain 7-2017-q4-major

https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads

- dfu-util 0.9

http://dfu-util.sourceforge.net/releases/

- zadig

https://zadig.akeo.ie/

- MSYS2

https://www.msys2.org/

MSYS2をインストールした後、先ほどのページの記載にもあるようにMSYS2のターミナルを起動し、下記コマンドを実行します。
```
pacman -Syu
```
その後下記コマンドを実行して必要なソフトをインストールします。
```
pacman -S base-devel
pacman -S mingw-w64-x86_64-toolchain
```

その後、インストールしたソフトの場所にPATHを通します。
私の環境では下記を通しましたが、各種環境によって変えてください。
```
C:\Program Files (x86)\GNU Tools ARM Embedded\7 2017-q4-major\bin
C:\Tools\dfu-util-0.9-win64
```

最後にMSYS2がWindowsのPATHを参照できるよう、環境変数を追加します。
変数名：MSYS2_PATH_TYPE
値：inherit

# Sens'it SDKのダウンロード
https://build.sigfox.com/sensit-for-developers/#sensit-sdk
下図の場所にダウンロードリンクがあるので、ダウンロードします。
ネットで検索するとgithubでsigfox/sensit-sdkが見つかりますが、入っているファイルに違いがあるようです。
https://api.build.sigfox.com/files/5d31c8bbc33691abfeee30c2

# プログラムのコンパイル
MSYS2のターミナルを起動し、ダウンロードしたSDKのsdkフォルダに移動します。Makefileファイルがある階層です。
今回は"src\main_TEMPERATURE.c"のサンプルプログラムを使っていきます。
下記コマンドを実行してください。
```
make temperature
```

エラー等が出ていないことを確認してください。

# Sens'it V3デバイスとの接続
そのままデバイスをPCに接続しても書き込むことができないので、一工夫必要みたいです。

### Sens'it V3とPCをケーブルで接続する

### Sens'it V3をブートローダーで起動する
ここがわかりにくいところなのですが、デバイス側で下記操作を行います。
+ 「中央ボタンを4回連続で押す。-->> USBコネクタ部分のLEDが白点滅する。」
+ 「白点滅している間に中央ボタンを長押す。 -->> 中央ボタンとUSBコネクタ部分のLEDが白点灯する。」
この操作を行って2つのLEDが白点灯したらOKです。
ですが、私が購入したデバイスでは下記操作でできました。最新のソフトは下記に変更になったのでしょうか？
+ 「中央ボタンを2回連続で押す。-->> USBコネクタ部分のLEDが白点滅する。」
+ 「白点滅している間に中央ボタンを長押す(ボタンは押しっぱなし)。 -->> 中央ボタンが4or5色とUSBコネクタ部分のLEDが順番に点灯する。」
+ 「中央ボタンのLEDが消え、USBコネクタ部のLEDのみ白点滅している間に中央ボタンを離す。 -->> 中央ボタンが4or5色とUSBコネクタ部分のLEDが順番に点灯する。」

### zadigによるUSBドライバのインストール
zadigを起動し、"Options -> List all devices"を選択します。
プルダウンでSens'it V3のデバイスを選択してWinUSBのドライバをインストールします。

# プログラムの書き込み
コンパイル時に使用したMSYS2のターミナル上で下記コマンドを実行すると自動的に書き込みが行われます。
```
make prog
```

書き込み終了後には自動的にブートローダー起動が終了します。
再度書き込みたいときにはまたブートローダーでの起動が必要です。

# 参考サイト
https://www.kccs.co.jp/sigfox/blog/technical/2018/0202/

https://www.kccs.co.jp/sigfox/blog/technical/2018/0205/

https://www.kccs.co.jp/sigfox/blog/technical/2018/0205-02/

https://github.com/sigfox/sensit-sdk

https://build.sigfox.com/sensit-for-developers/#sensit-sdk


