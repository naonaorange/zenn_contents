---
title: "VirtualBox環境のROS2でWebカメラを使えるようにする"
emoji: "😀"
type: "tech"
topics: ["20210523","ros2"]
published: true
---
VirtualBox環境下でROS2を使用するときに問題になりそうなのはWebカメラなどのデバイス接続がきちんとできるかということです。

この記事ではVirualBox環境下のUbuntu18.04 ROS2 DashingでWebカメラを使えるようにします。

# 前提条件
VirtualBox ExtensionがホストPCとゲストPCにどちらもインストールされていること

# USBをゲストPCに認識させる
VirturalBoxの設定で"USB3.0(xHCI)コントローラー"にチェックが入っていることを確認する。
私のPCではWebカメラをUSB3.0ポートにさすことになります。
その場合は下記の設定を行う必要があります。
![](/images/20210523_virtualbox_ros2_webcam/1.png)


# ゲストPCでWebカメラを認識させる
ゲストPCのデバイス設定でWebカメラを選択します。
これでゲストPCにWebカメラが認識されます。
![](/images/20210523_virtualbox_ros2_webcam/2.png)

# Webカメラの動作確認
とりあえずWebカメラにきちんとアクセスできているか確認しましょう。
```sh
cheese
```
![](/images/20210523_virtualbox_ros2_webcam/3.png)

* ROS2のV4L2用パッケージをインストールする
```sh
sudo apt-get install ros-dashing-v4l2-camera
```

* ROS2プログラムを実行時アクセスできるか確認する
```sh
ros2 run v4l2_camera v4l2_camera_node
```

```sh
ros2 run rqt_image_view rqt_image_view
```

![](/images/20210523_virtualbox_ros2_webcam/4.png)