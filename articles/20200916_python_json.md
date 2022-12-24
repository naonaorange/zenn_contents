---
title: "Pythonでjsonファイルを読み書きする"
emoji: "😀"
type: "tech"
topics: ["20200916","python"]
published: true
---
Pythonでソフトを作るとき、設定値を保存したい時が多くあります。
そんな時に使うのがjsonファイルですよね。
Pythonでjsonファイルを扱うのはとても簡単です。

サンプルプログラムを載せます。
```python
import json

def write():
    with open('sample.json', 'w') as f:
        w_data = {}
        w_data['num'] = 0
        w_data['str'] = 'Hello'
        w_data['array'] = []
        w_data['array'].append(1)
        w_data['array'].append(2)
        w_data['array'].append(3)

        json.dump(w_data, f, indent=2)
        print('WRITE:')
        print(w_data)

def read():
    with open('sample.json', 'r') as f:
        r_data = json.load(f)
        print('READ')
        print(r_data)

if __name__ == '__main__':
    write()
    read()

#WRITE:
#{'num': 0, 'str': 'Hello', 'array': [1, 2, 3]}
#READ
#{'num': 0, 'str': 'Hello', 'array': [1, 2, 3]}
```
