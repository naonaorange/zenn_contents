---
title: "RaspberryPi Zero WをOTGで起動する"
emoji: "😀"
type: "tech"
topics: ["20190224","raspberrypi"]
published: true
---
RaspberryPi Zero Wを購入しました。
小さくてよいのですが、ssh等で操作するときには下記2本のケーブルをつなぐ必要があります。
+ 電源ケーブル
+ sshで操作する通信ケーブル

通常のRaspberry Piであれば2.5A程度出せる電源を用意する必要があるので難しいですが、
Zero W程度であれば上記の2本を1本でできた方がいいですよね。

調べてみたところ、OTGを利用することで1本でできるようになるそうです。
操作方法は下記で詳細に説明されています。
https://qiita.com/msquare33/items/dc6fc9098f50d9b9dbf2

https://qiita.com/Liesegang/items/dcdc669f80d1bf721c21

1点だけ説明すると、「RPI Driver OTG」がダウンロードできるサイトに変更が入っており、
下記サイトで代わりにダウンロードできます。

https://caron.ws/diy-cartes-microcontroleurs/raspberrypi/pi-zero-otg-ethernet/

https://caron.ws/wp-content/uploads/telechargement/RPI%20Driver%20OTG.zip

