---
title: "Sens'it V3を使ってSigfoxで遊んでみる"
emoji: "😀"
type: "tech"
topics: ["20220429","sigfox"]
published: true
---
# sensit.ioへの登録
sensit.ioへアクセスします。
https://www.sensit.io/

Laungh Appをクリック
![](/images/20220429_senseitv3_sigfox/1.png)

Activate my sensitをクリック
![](/images/20220429_senseitv3_sigfox/2.png)

後はガイドに沿って操作を進めれば設定は完成します。
こちらのP5までに詳細が書かれています。
https://d2air1d4eqhwg2.cloudfront.net/media/files/1db11f7e-1be2-4146-91d5-0321aa9c8120.pdf

そのあとにSens'it V3のボタンを長押しし、緑色に光る「温湿度モード」に設定すると定期的に温度と湿度が送信されます。
![](/images/20220429_senseitv3_sigfox/3.png)

# PACの取得
![](/images/20220429_senseitv3_sigfox/4.png)

![](/images/20220429_senseitv3_sigfox/5.png)
PACが表示されるので、メモしてください。

# Sigfox Buyでのアクティベート
デバイスをアクティベートします。
Sigfox Buyのサイトで「Activate my Devkit」をクリックし、ガイドに従います。
https://buy.sigfox.com/

下記情報を入力します。
- SigfoxデバイスID
   Sens'it V3裏面に記載されているIDを入力
- PAC
　上の操作で取得した値を入力
- Product certification ID
　P_0006_DAD8_01を入力

# 通信内容の確認
Sens'it V3はデフォルトで周期的に通信を行うプログラムが入っています。
(確か1時間毎だと思います。)
Sigfox Backendでその通信をモニタリングできます。
https://backend.sigfox.com

サイト上にある「device」タブをクリックし、デバイス一覧を表示します。
確認したいデバイスのID欄をクリックするとそのデバイスの画面に行けるので、「Message」を確認することでリアルタイムに確認することができます。

参考サイト
https://d2air1d4eqhwg2.cloudfront.net/media/files/1db11f7e-1be2-4146-91d5-0321aa9c8120.pdf
https://users.soracom.io/ja-jp/guides/devices/sigfox/sensit/
https://users.soracom.io/ja-jp/guides/devices/sigfox/sensit/
https://gugenka.co.jp/technique/sensit-ver3-1/
https://gugenka.co.jp/technique/sensit-ver3-2/
https://gugenka.co.jp/technique/sensit-ver3-3/




