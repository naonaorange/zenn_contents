---
title: "Intel RealSense D435 ãROSã§ä½¿ã"
emoji: "ð"
type: "tech"
topics: ["20181194","ros"]
published: true
---
Intel RealSense D435ãè²·ãã¾ããï¼

https://click.intel.com/intelr-realsensetm-depth-camera-d435.html

https://www.switch-science.com/catalog/3633/

ã¨ãããããROSã§åãããããã«ãã¾ãã
ç°å¢ã¯ä¸è¨ã§ãã
- Ubuntu 16.04 64bit
- ROS Kinetic

ãã®è¨äºã¯ãä¸è¨ã®Githubã®èª¬æéããªã®ã§ããããã¡ãã£ã¨ããããããèª¬æãã¦ããã ãã§ãã
https://github.com/intel-ros/realsense

# Intel RealSense D435 åä½ã§åãããããã«ãã
ã¾ãã¯ROSããã¬ãã§ã¯ãªããåä½ã§åãããããã«ããå¿è¦ãããã¾ãã
aptã§ã¤ã³ã¹ãã¼ã«ã§ããã®ã§ãæ¯è¼çç°¡åã§ãã

```sh
sudo apt-key adv --keyserver keys.gnupg.net --recv-key C8B3A55A6F3EFCDE || sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key C8B3A55A6F3EFCDE
#ãªãã¸ããªã®ç»é²
sudo add-apt-repository "deb http://realsense-hw-public.s3.amazonaws.com/Debian/apt-repo xenial main" -u

sudo apt-get update
#ã¤ã³ã¹ãã¼ã«
sudo apt-get install librealsense2-dkms librealsense2-utils librealsense2-dev librealsense2-dbg  intel-realsense-dfu
```

ããã§ã¤ã³ã¹ãã¼ã«ã¯çµããã§ãã
Viewerã½ãããä¸ç·ã«ã¤ã³ã¹ãã¼ã«ããã¦ããã®ã§ãå®è¡ãã¾ãã

```sh
realsense-viewer
```

ã¦ã£ã³ãã¦ãè¡¨ç¤ºãããã®ã§ã"RGB Camera"ã®ãã°ã«ãONã«ãã¦ãç»åãè¡¨ç¤ºãã¦ã¿ã¾ãã
ããããç§ã®ç°å¢ã§ã¯ç»åãè¡¨ç¤ºããã¾ããã§ããã
ã¾ããã¦ã£ã³ãã¦ãè¡¨ç¤ºãããã¨åæã«ãD435ã®ãã¡ã¼ã ã¦ã§ã¢ã¢ãããã¼ãã®éç¥ãè¡¨ç¤ºããã¦ãã¾ãã
![](/images/20181104_realsense_ros/1.jpeg)
çµæçã«è¨ãã¨ããã®ãã¡ã¼ã ã¦ã§ã¢ãã¢ãããã¼ãããã¨ç»åãè¡¨ç¤ºãããã®ã§ãã¢ãããã¼ãä½æ¥­ãå¿è¦ããã§ãã
åºæ¬çã«ãã¢ãããã¼ããããã¨ããã¨è©²å½ã®Webãã¼ã¸ã«é£ã¶ã®ã§ãããã®éãã«ããã°ããã§ãã

https://downloadcenter.intel.com/download/28237/Latest-Firmware-for-Intel-RealSense-D400-Product-Family?v=t

https://www.intel.com/content/dam/support/us/en/documents/emerging-technologies/intel-realsense-technology/Linux-RealSense-D400-DFU-Guide.pdf

```sh
#ã¾ãã¯ç¾å¨ã®ãã¡ã¼ã ã¦ã§ã¢ãã¼ã¸ã§ã³ãç¢ºèª
intel-realsense-dfu âp
#D435ãèªè­ããã¦ãããç¢ºèª
lsusb
```

lsusbãå®è¡ããã¨ç§ã®ç°å¢ã§ã¯ä¸è¨ã®ããã«è¡¨ç¤ºããã¾ããã
![](/images/20181104_realsense_ros/2.jpeg)
"Intel Corp"ã¨ããã®ãRealSense D435ã§ãã
ãã®è¡ã®Bus No(ããã§ã¯004)ã¨Device No(005)ã®æ°å­ãæ¬¡ã®ã³ãã³ãã§ä½¿ãã¾ãã
```sh
#ãã¡ã¼ã ã¦ã§ã¢ã®ã¢ãããã¼ã
intel-realsense-dfu -b 004 -d 005 -f -i ./Signed_Image_UVC_5_10_6_0.bin
```

ã¢ãããã¼ãå¾D435ãååº¦æ¿ããªããã¦ãViewerã½ãããããä¸åº¦èµ·åããã¨ãã«ã¡ã©ç»åãè¡¨ç¤ºããã¾ããã
ããã¹ããã¡ãã¨åãã¦ãã¾ãã
![](/images/20181104_realsense_ros/3.jpeg)


# ROSãä½¿ã£ã¦åãã
aptã«ã¯ç»é²ããã¦ããªãã®ã§ãgithubããã½ã¼ã¹ã³ã¼ãããã¦ã³ã­ã¼ããã¦ãã¾ãã
https://github.com/intel-ros/realsense/releases

ä»åã¯version 2.1.3ãä½¿ç¨ãã¾ãã
```sh
sudo apt-get install ros-kinetic-rgbd-launch
catkin_make -DCATKIN_ENABLE_TESTING=False -DCMAKE_BUILD_TYPE=Release
catkin_make install
```

# ã«ã¡ã©ç»åã®è¡¨ç¤º
ä¸è¨ã³ãã³ããããããå¥ç«¯æ«ã§å®è¡ããã°OK
```sh
roslaunch realsense2_camera rs_camera.launch
rosrun image_view image_view  image:=/camera/color/image_raw
```
![](/images/20181104_realsense_ros/4.png)

# 3Dæå ±ã®è¡¨ç¤º
ä¸è¨ã³ãã³ããããããå¥ç«¯æ«ã§å®è¡ããã°OK
```sh
roslaunch realsense2_camera rs_rgbd.launch
rosrun rviz rviz
```
Rvizã®è¨­å®ã§Fixed Frameãcamera_linkã«è¨­å®ã
PointCloud2ãè¿½å ããã¨è¡¨ç¤ºã§ãã¾ãã
![](/images/20181104_realsense_ros/5.png)
