---
title: "mbedでrosserialをつかってみる"
emoji: "😀"
type: "tech"
topics: ["20181222","ros","mbed"]
published: true
---
自分のロボットをROS対応させたいと思いますよね？
通常はPCからUARTで通信させて動かすことが多いと思うので、rosserialを使ってROS対応させてみます。

ネットの記事ではArduinoを使っていることが多いのですが、mbedのほうが好きなのでmbedでrosserialをしてみます。

といっても、もうやっている人がいるのでその方のやり方を真似します。
ros_lib_kineticを使用します。
https://os.mbed.com/users/garyservin/code/ros_lib_kinetic/

まずはmbed側のプログラムを作ります。
といっても、下のサイトからプロジェクト一式をimportするだけでいいです。
コンパイルしてmbedに書き込みます。
https://os.mbed.com/users/garyservin/code/rosserial_mbed_hello_world_publisher_kin/

次にPC側。
```sh
sudo apt install ros-kinetic-rosserial-python
```

次に使い方。まずはroscoreを実行して、
```sh
roscore
```

mbedにアクセス権限がないようなので、権限を追加します。
これはmbedを抜き差しするとリセットされてしまうので、毎度やらないと行けないみたいです。
解決方法があれば後で記載します。
```sh
sudo chmod a+rw /dev/ttyACM0
```
mbedと通信するrosserial pythonを起動します。
```sh
rosrun rosserial_python serial_node.py /dev/ttyACM0
```
これですでに通信ができていますので、rostopicで見てみると確認できます。
```sh
rostopic echo chatter
```

mbed側のプログラムのサンプルを下に載せます。
MultiArrayを使うときにはmallocを使って動的に箱を作ってやらないといけないんですね。。。
```cpp
#include"mbed.h"
#include <ros.h>
#include <geometry_msgs/Point32.h>
#include <std_msgs/Float32MultiArray.h>

ros::NodeHandle  nh;
geometry_msgs::Point32 pos;
std_msgs::Float32MultiArray angles;

ros::Publisher pos_pub("position", &pos);
ros::Publisher angles_pub("angles", &angles);

DigitalOut led = LED1;

int main() {
    nh.initNode();
    nh.advertise(pos_pub);
    nh.advertise(angles_pub);
    
    pos.x = 0;
    pos.y = 0;
    pos.z = 0;
    angles.data_length = 8;
    angles.data = (float *)malloc(sizeof(float)*8);
    for(int i = 0; i < angles.data_length; i++){
        angles.data[i] = 0.0;
    }
    
    while (1) {
        led = !led;
        pos_pub.publish(&pos);
        angles_pub.publish(&angles);
        
        pos.x = pos.x + 1;
        pos.y = pos.y + 1;
        pos.z = pos.z + 1;
        for(int i = 0; i < angles.data_length; i++){
            angles.data[i] = angles.data[i] + 1;
        }
        
        if(pos.x > 1000){
            pos.x = 0;
            pos.y = 0;
            pos.z = 0;
            for(int i = 0; i < angles.data_length; i++){
                angles.data[i] = 0.0;
            }
        }    
        
        nh.spinOnce();
        wait_ms(100);
    }
}
```