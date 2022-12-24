---
title: "rosbridgeとroslibjsを使ってJavascriptでROSのTopicを操作してみる"
emoji: "😀"
type: "tech"
topics: ["20190212","ros","javascript"]
published: true
---
Javascript(Node.js)でROSのTopicをpublish, subscribeしてみます。

ROSはKinetic、Node.jsはversion8.15.0を使っていきます。

まずは、インストールします。
```sh
sudo apt install ros-kinetic-roswww ros-kinetic-rosbridge-server
```
次にNode.jsでroslibをインストールします。
```sh
sudo npm install -g roslib
```
それではrosbridgeを起動します。
```sh
roslaunch rosbridge_server rosbridge_websocket.launch
```

## Topicをpublishする
下記のファイルをhoge.jsとして保存します。
```javascript
#!/usr/bin/env node

// Connecting to ROS 
var ROSLIB = require('roslib');

var ros = new ROSLIB.Ros({
    url : 'ws://localhost:9090'
});

ros.on('connection', function() {
console.log('Connected to websocket server.');
});

ros.on('error', function(error) {
console.log('Error connecting to websocket server: ', error);
});

ros.on('close', function() {
console.log('Connection to websocket server closed.');
});

const cmdVel = new ROSLIB.Topic({
    ros : ros,
    name : '/turtle1/cmd_vel',
    messageType : 'geometry_msgs/Twist'
});
const twist = new ROSLIB.Message({
    linear : {
        x : 2.0,
        y : 0,
        z : 0
    },
    angular : {
        x : 0,
        y : 0,
        z : 0
    }
});

cmdVel.publish(twist);
```

turtlesimを起動してから、hoge.jsを実行すると、亀が移動するのがわかります。
```sh
rosrun turtlesim turtlesim_node
node hoge.js
```

## Topicをsubscribeする
次に下のコードをfuga.jsとして保存します。

```javascript
#!/usr/bin/env node

// Connecting to ROS 
var ROSLIB = require('roslib');

var ros = new ROSLIB.Ros({
    url : 'ws://localhost:9090'
});

ros.on('connection', function() {
console.log('Connected to websocket server.');
});

ros.on('error', function(error) {
console.log('Error connecting to websocket server: ', error);
});

ros.on('close', function() {
console.log('Connection to websocket server closed.');
});

// Subscribing a Topic
// ------------------
const listener = new ROSLIB.Topic({
   ros : ros,
   name : '/listener',
   messageType : 'std_msgs/Float64'
});

listener.subscribe(message => {
    console.log('Received message on ' + listener.name + ': ' + message.data);
});
```

fuga.jsを起動したあとに、rostopicで/std_msgs/Float64を投げると、ログが表示されることがわかります。
```sh
node fuga.js
rostopic pub -1 /listener /std_msgs/Float64 0.1
```

## 参考にしたサイト
https://qiita.com/kouhei-k/items/bb44c070d4304caa4bf1

https://qiita.com/yoneken/items/7e45ad3fcf8010ed98ed

https://symfoware.blog.fc2.com/blog-entry-2292.html

http://wiki.ros.org/roslibjs/Tutorials/BasicRosFunctionality




