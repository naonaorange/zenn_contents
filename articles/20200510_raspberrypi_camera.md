---
title: "Raspberry Camera V2での画像撮影処理時間確認"
emoji: "😀"
type: "tech"
topics: ["20200510","raspberrypi"]
published: true
---
ラズパイのカメラを使っていると、「処理が遅い」と思うところが多々あります。
できるだけ、応答性良く画像を撮影したいと考えています。

まずは、今簡単に画像取得する方法でどのくらい時間がかかるのか確認していきます。

# 確認環境
- RaspberryPi Zero W
- RaspberryPi Camera V2.1
- Rasbian Bustor
- RaspberryPiではGUIを表示せず、SSHで他のPCからリモートで実行
- 取得する画像サイズは基本的に1024*768

# 結果
下記に示すように色々な方法を試していますが、表にしました。

|方法|時間[s]|
|--|--|
|[Python]画像ファイルに保存する|0.62|
|[Python]ストリームでメモリに保存する|0.48|
|[Python]Video Portを有効にしてストリーム取得する|0.11|
|[C++]V4L2経由でデータ取得|0.21|


## [Python]画像ファイルに保存する
まずは一般的に画像をファイルに保存します。
結果は0.62s。果てしなく遅い。。。
```python
#!/usr/bin/env python3

import picamera
import time

with picamera.PiCamera() as camera:
    camera.start_preview()
    time.sleep(2)
    start = time.time()
    for i in range(10):
        camera.capture('test1.jpg')
    processing = time.time() - start
    print('time : ' + str(processing / 10))
```

## [Python]ストリームでメモリに保存する
画像ファイルに保存するとなると、SDカードへの書き込み処理が入るので、内部のメモリーに保存した方が早そうです。
結果は0.48s。まだまだ遅いので、ファイル書き込みが原因ではないですね。
```python
#!/usr/bin/env python3

import io
import time
import picamera

my_stream = io.BytesIO()
with picamera.PiCamera() as camera:
    camera.start_preview()
    time.sleep(2)
    start = time.time()
    for i in range(10):
        camera.capture(my_stream, 'bgr')
    processing = time.time() - start
    print('time : ' + str(processing / 10))
```

## [Python]Video Portを有効にしてストリーム取得する
capture関数の引数にはuse_video_portというものがあり、picameraのソースコードを見ると次のようなコメントがあります。
https://github.com/waveform80/picamera/blob/release-1.10/picamera/camera.py
```
The *use_video_port* parameter controls whether the camera's image or
video port is used to capture images. It defaults to ``False`` which
means that the camera's image port is used. This port is slow but
produces better quality pictures. If you need rapid capture up to the
rate of video frames, set this to ``True``.
```

結果は0.11s。大幅に早くなりましたね。
画像の質が落ちると書いていますが、この差は代えがたい！
```python
#!/usr/bin/env python3

import io
import time
import picamera

my_stream = io.BytesIO()
with picamera.PiCamera() as camera:
    camera.start_preview()
    time.sleep(2)
    start = time.time()
    for i in range(10):
        camera.capture(my_stream, 'bgr', use_video_port=True)
    processing = time.time() - start
    print('time : ' + str(processing / 10))
```

## [C++]V4L2経由でデータ取得
RaspberryPi CameraをV4L2でアクセスできるようにして、画像を取得します。
カメラを接続しただけではV4L2経由でアクセスできないので、次のコマンドをまず実行します。
```
sudo modprobe bcm2835-v4l2
```
実行したコードはほぼこれと同じです。
変更した点は画像サイズを他の方法と合わせたのと、画像取得を10回繰り返した点です。
結果は、0.21s。video_portを有効にした時よりは遅いですが、Pythonで普通にストリーム取得するときよりは早い。
https://qiita.com/iwatake2222/items/d6645880c5bb91ce8a85


