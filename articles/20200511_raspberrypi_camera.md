---
title: "RaspberryPi Camera V2でISO・シャッタースピードを変更する"
emoji: "😀"
type: "tech"
topics: ["20200511","raspberrypi"]
published: true
---
ラズパイカメラで画像を撮るときにAEに任せるのではなく、手動で露出を変更したいです。
picameraを使うとpythonでもできるようです。

下記プログラムはISO 100、シャッタースピード1/60(16666us)で設定できます。
start_preview関数の後に設定できるのがいいですね。
```python
import time
import picamera

with picamera.PiCamera() as camera:
    camera.start_preview()
    time.sleep(2)
    camera.resolution = (3280, 2464)
    camera.iso = 100
    camera.shutter_speed = 16666
    camera.capture('foo.jpg')
```

できた画像は下記コマンドでデータを確認できます。
jheadコマンドがない場合はaptコマンドでgetできます。
```
jhead foo.jpg
```
