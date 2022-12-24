---
title: "BLEデバッグツール\"Blue Tropical Fish\"をリリースしました"
emoji: "😀"
type: "tech"
topics: ["20180602","ble","xamarin"]
published: true
---

BLEのデバイス/ソフト開発用のアプリ"Blue Tropical Fish"をリリースしました。
![](/images/20180602_bluetropicalfish/1.png)

BLEのデバッグアプリとしては「nrf connect」や「Light Blue」が有名ですが、
それらと同じような(というか機能縮小版。。。)アプリになります。

Google playからインストール可能です。
https://play.google.com/store/apps/details?id=com.naonaorange.bluetropicalfish
Windows storeでもインストール可能です。
https://www.microsoft.com/en-us/p/blue-tropical-fish/9p14nwqz276h?activetab=pivot%3aoverviewtab

ソースはGithubにアップしています。
https://github.com/naonaorange/BlueTropicalFish

現時点では、周囲のBluetoothアドバタイズパケットを受信して表示します。
将来的には接続・ペアリングもサポート予定ですが、現時点では未サポートです。
リストを選択することでデバイスの詳細情報を見ることができます。
![](/images/20180602_bluetropicalfish/2.png)

表示するデバイスをフィルターすることもできます。
![](/images/20180602_bluetropicalfish/3.png)

Nameの項目に入力にした後、"Show Only Filtered Devices"をONにすることでフィルター条件にマッチしたものだけ表示します。
![](/images/20180602_bluetropicalfish/4.png)

また、リストの右側のスイッチをONにしてお気に入りに登録することで、
"Show Only Favorite Devices"をONにすることでお気に入りのデバイスのみを表示することができます。
![](/images/20180602_bluetropicalfish/5.png)
