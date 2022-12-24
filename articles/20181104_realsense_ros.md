---
title: "Intel RealSense D435 をROSで使う"
emoji: "😀"
type: "tech"
topics: ["20181194","ros"]
published: true
---
Intel RealSense D435を買いました！

https://click.intel.com/intelr-realsensetm-depth-camera-d435.html

https://www.switch-science.com/catalog/3633/

とりあえず、ROSで動かせるようにします。
環境は下記です。
- Ubuntu 16.04 64bit
- ROS Kinetic

この記事は、下記のGithubの説明通りなので、これをちょっとわかりやすく説明しているだけです。
https://github.com/intel-ros/realsense

# Intel RealSense D435 単体で動かせるようにする
まずはROSうんぬんではなく、単体で動かせるようにする必要があります。
aptでインストールできるので、比較的簡単です。

```sh
sudo apt-key adv --keyserver keys.gnupg.net --recv-key C8B3A55A6F3EFCDE || sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key C8B3A55A6F3EFCDE
#リポジトリの登録
sudo add-apt-repository "deb http://realsense-hw-public.s3.amazonaws.com/Debian/apt-repo xenial main" -u

sudo apt-get update
#インストール
sudo apt-get install librealsense2-dkms librealsense2-utils librealsense2-dev librealsense2-dbg  intel-realsense-dfu
```

これでインストールは終わりです。
Viewerソフトが一緒にインストールされているので、実行します。

```sh
realsense-viewer
```

ウィンドウが表示されるので、"RGB Camera"のトグルをONにして、画像を表示してみます。
しかし、私の環境では画像が表示されませんでした。
また、ウィンドウが表示されると同時に、D435のファームウェアアップデートの通知が表示されています。
![](/images/20181104_realsense_ros/1.jpeg)
結果的に言うと、このファームウェアをアップデートすると画像が表示されたので、アップデート作業が必要そうです。
基本的に、アップデートしようとすると該当のWebページに飛ぶので、そこの通りにすればよいです。

https://downloadcenter.intel.com/download/28237/Latest-Firmware-for-Intel-RealSense-D400-Product-Family?v=t

https://www.intel.com/content/dam/support/us/en/documents/emerging-technologies/intel-realsense-technology/Linux-RealSense-D400-DFU-Guide.pdf

```sh
#まずは現在のファームウェアバージョンを確認
intel-realsense-dfu –p
#D435が認識されているか確認
lsusb
```

lsusbを実行すると私の環境では下記のように表示されました。
![](/images/20181104_realsense_ros/2.jpeg)
"Intel Corp"というのがRealSense D435です。
この行のBus No(ここでは004)とDevice No(005)の数字を次のコマンドで使います。
```sh
#ファームウェアのアップデート
intel-realsense-dfu -b 004 -d 005 -f -i ./Signed_Image_UVC_5_10_6_0.bin
```

アップデート後D435を再度挿しなおして、Viewerソフトをもう一度起動すると、カメラ画像が表示されました。
デプスもきちんと取れています。
![](/images/20181104_realsense_ros/3.jpeg)


# ROSを使って動かす
aptには登録されていないので、githubからソースコードをダウンロードしてきます。
https://github.com/intel-ros/realsense/releases

今回はversion 2.1.3を使用します。
```sh
sudo apt-get install ros-kinetic-rgbd-launch
catkin_make -DCATKIN_ENABLE_TESTING=False -DCMAKE_BUILD_TYPE=Release
catkin_make install
```

# カメラ画像の表示
下記コマンドをそれぞれ別端末で実行すればOK
```sh
roslaunch realsense2_camera rs_camera.launch
rosrun image_view image_view  image:=/camera/color/image_raw
```
![](/images/20181104_realsense_ros/4.png)

# 3D情報の表示
下記コマンドをそれぞれ別端末で実行すればOK
```sh
roslaunch realsense2_camera rs_rgbd.launch
rosrun rviz rviz
```
Rvizの設定でFixed Frameをcamera_linkに設定。
PointCloud2を追加すると表示できます。
![](/images/20181104_realsense_ros/5.png)
