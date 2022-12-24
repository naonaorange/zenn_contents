---
title: "Pythonからdllを呼び出す"
emoji: "😀"
type: "tech"
topics: ["20191004","python"]
published: true
---
前回、dllの作成方法を書きましたが、次はそれをPythonから呼び出します。

ポイントとしては、

- dllとpythonを同じディレクトリにいれること
- charなどの方はctypesからC言語用の型を取ってきて、それを使う。intとかはそのままでもできることはできる

```python
from ctypes import cdll, c_char

lib = cdll.LoadLibrary('hoge.dll')
char_array = c_char * 2
input_data = char_array()

for i in range(2):
    input_data[i] = i

lib.hoge_func(input_data, 2)
```