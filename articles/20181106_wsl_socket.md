---
title: "WindowsとWSL(Windows Subsystem for Linux)間でSoket通信をする"
emoji: "😀"
type: "tech"
topics: ["20181106","wsl"]
published: true
---
WindowsとWSL上のUbuntuで通信をしたいと思ったので、
まずは一番簡単そうなSocket通信をしてみました。

WindowsからUbuntuへSocket通信をするプログラムです。
プロトコルはIPv4のTCPを利用しています。

Client.py(Windows側のプログラム)
```python
#!/usr/bin/env python

import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect(('127.0.0.1', 50008))
    s.sendall(b"hello")
```

Server.py(Ubuntu側のプログラム)
```python
#!/usr/bin/env python

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


