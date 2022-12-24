---
title: "multiprocessingとqueueを使ってマルチプロセスのsocket通信をしてみる"
emoji: "😀"
type: "tech"
topics: ["20181110","python"]
published: true
---
前回、socekt通信のサンプルプログラムを書きましたが、
https://naonaorange.hatenablog.com/entry/2018/11/06/070039

何かsocket通信するときには普通はPublisher-Subscriberのデザインパターンを使って、socket通信部と、データ供給部を別プロセスにしたくなります。
ですので、そのようなサンプルプログラムを記載しました。

下記はsocket送信プログラム
```python
import time
from multiprocessing import Process
from multiprocessing import Queue

import socket

class client_socket:
    def __init__(self):
        self.s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.s.connect(('127.0.0.1', 50008))
    
    def send(self, msg):
        print(msg)
        d = bytearray(4)
        d[0] = 0xAA
        d[1] = 0x01
        d[2] = msg & 0xFF
        d[3] = (msg >> 8) & 0xFF
        self.s.sendall(d)


def data_process_func(q):
    for i in range(10):
        q.put(128)
        time.sleep(1)

def socket_process_func(q):
    s = client_socket()
    while True:
        s.send(q.get())

if __name__ == "__main__":
    q = Queue()
    data_process = Process(target=data_process_func, args=(q,))
    socket_process = Process(target=socket_process_func, args=(q,))

    socket_process.start()
    data_process.start()

    data_process.join()
    socket_process.join()
```

次はsocket受信プログラム
```python
import socket

if __name__ == '__main__':
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

    s.bind(('127.0.0.1', 50008))
    s.listen(1)
    while True:
        conn, addr = s.accept()
        while True:
            data = conn.recv(1024)
            if not data:
                break
            print('data : {}'.format(data))
        conn.close()
    s.close()
```