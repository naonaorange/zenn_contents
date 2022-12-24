---
title: "tkinterのGUIデザイナーPAGEを使ってみる"
emoji: "😀"
type: "tech"
topics: ["20220807","python"]
published: true
---
pythonを使ってGUIソフトを作りたいと思う機会は多いのですが、気軽に作ることができないのが難点です。
pythonをインストールすることでデフォルトで入っていることが多いのでライブラリはtkinterを使おうと思うのですが、
画面のモジュール配置やレイアウトをコードで書いていくのは中々辛いです。。。

そこでPAGEというソフトを使ってVisual StudioのようにGUIでマウスを使ってウィンドウのレイアウトを作っていきます。

# インストール
PAGEのインストールは下記サイトからできます。
http://page.sourceforge.net/:embed:cite

# 起動
```sh
python page.py
```
![](/images/20220807_tkinter_gui_page/page_1.png)

# GUI画面の編集
PAGEで画面の編集を行います。
今回はサンプルとして、ボタンを押したら画面上の数字が増えるようなソフトを作成します。
1. LabelとButtonを画面に追加します
1. Aliasに各種モジュールの名前を入力します
1. 名前を入力した後、画像と同じ名前になっていることを確認します
![](/images/20220807_tkinter_gui_page/page_2.png)

画面に追加したLabelを中クリックしてWidget -> Set Textを選択します。
サンプルでは数字を増やしていきたいので初めは0にしておきます。
![](/images/20220807_tkinter_gui_page/page_3.png)
次にButtonも同じようにWidget -> Set Textを選択し、"Increment"とします。

次にButtonを押された時のイベント関数を追加します。
Buttonを中クリックしてWidget -> Set Commandを選択し"IncrementButtonOnClick"とします。
![](/images/20220807_tkinter_gui_page/page_4.png)
これで画面の変更は終了です。
変更したプロジェクトをFile -> Save asで保存します。
名前はpage_testとします。

# GUIコード生成
これまで作成した画面をpythonファイルに出力します。
2種類生成することになりますが、まずは画面の設定等が記載されているファイルを生成します。
Gen_Python -> Generate python GUIを選択します。
ウィンドウが出てきますのでSaveを選択します。

次はコールバック関数等が記載されているサポートファイルを生成します。
Gen_Python -> Generate Support Moduleを選択し、こちらもSaveを選択します。
![](/images/20220807_tkinter_gui_page/page_5.png)

# GUIコードの確認
ここでpythonファイルを確認してみましょう。
画面のレイアウト等の情報はpage_test.pyに記載されていますが、ボタンを押した時のイベント関数はpage_test_support.pyに記載されています。

ここで他のサイトを見てみると処理を変更したい場合はpage_test_support.pyを直接変更しているようです。
しかし、これまでの操作でもわかるようにこれらのコードはPAGEによって生成されているものです。
レイアウトを変更する度に生成し直す必要があるので、手作業で変更を加えてもツールによって上書きされてしまう可能性があります。
(実際に使ってみた感じでは、再生成するときに手作業の部分だけ変更を残してくれる時もあったのですが、時々きちんと残せない場合もあるようです。)

そこで、できるだけ生成されたファイルに変更を加えないようにしてソフトを作っていきます。

**page_test.py**
```python
import sys
import tkinter as tk
import tkinter.ttk as ttk
from tkinter.constants import *
import os.path

_script = sys.argv[0]
_location = os.path.dirname(_script)

import page_test_support

_bgcolor = '#d9d9d9'  # X11 color: 'gray85'
_fgcolor = '#000000'  # X11 color: 'black'
_compcolor = 'gray40' # X11 color: #666666
_ana1color = '#c3c3c3' # Closest X11 color: 'gray76'
_ana2color = 'beige' # X11 color: #f5f5dc
_tabfg1 = 'black' 
_tabfg2 = 'black' 
_tabbg1 = 'grey75' 
_tabbg2 = 'grey89' 
_bgmode = 'light' 

class Toplevel1:
    def __init__(self, top=None):
        '''This class configures and populates the toplevel window.
           top is the toplevel containing window.'''

        top.geometry("600x450+316+1096")
        top.minsize(72, 15)
        top.maxsize(3008, 1554)
        top.resizable(1,  1)
        top.title("Toplevel 0")
        top.configure(background="#d9d9d9")

        self.top = top

        self.CountLabel = tk.Label(self.top)
        self.CountLabel.place(relx=0.0, rely=0.0, height=22, width=39)
        self.CountLabel.configure(anchor='w')
        self.CountLabel.configure(background="#d9d9d9")
        self.CountLabel.configure(compound='left')
        self.CountLabel.configure(foreground="#000000")
        self.CountLabel.configure(text='''0''')

        self.IncrementButton = tk.Button(self.top)
        self.IncrementButton.place(relx=0.0, rely=0.067, height=28, width=77)
        self.IncrementButton.configure(activebackground="beige")
        self.IncrementButton.configure(activeforeground="black")
        self.IncrementButton.configure(background="#d9d9d9")
        self.IncrementButton.configure(command=page_test_support.IncrementButtonOnClick)
        self.IncrementButton.configure(compound='left')
        self.IncrementButton.configure(foreground="#000000")
        self.IncrementButton.configure(highlightbackground="#d9d9d9")
        self.IncrementButton.configure(highlightcolor="black")
        self.IncrementButton.configure(text='''Increment''')

def start_up():
    page_test_support.main()

if __name__ == '__main__':
    page_test_support.main()
```

**page_test_support.py**
```python
import sys
import tkinter as tk
import tkinter.ttk as ttk
from tkinter.constants import *

import page_test

def main(*args):
    '''Main entry point for the application.'''
    global root
    root = tk.Tk()
    root.protocol( 'WM_DELETE_WINDOW' , root.destroy)
    # Creates a toplevel widget.
    global _top1, _w1
    _top1 = root
    _w1 = page_test.Toplevel1(_top1)
    root.mainloop()

def IncrementButtonOnClick(*args):
    print('page_test_support.IncrementButtonOnClick')
    for arg in args:
        print ('    another arg:', arg)
    sys.stdout.flush()

if __name__ == '__main__':
    page_test.start_up()
```

# GUIコードの編集
## GUIコード
page_test.pyの変更はありません。

## GUIサポートコード
page_test_support.pyは下記のadd部分を追加します。
これから新規追加するpage_test_modを呼び出します。
**page_test_support.py**
```python
from operator import mod
import sys
import tkinter as tk
import tkinter.ttk as ttk
from tkinter.constants import *

import page_test
import page_test_mod

def main(*args):
    '''Main entry point for the application.'''
    global root
    root = tk.Tk()
    root.protocol( 'WM_DELETE_WINDOW' , root.destroy)
    # Creates a toplevel widget.
    global _top1, _w2
    _top1 = root
    _w1 = page_test.Toplevel1(_top1)

    #add
    global mod
    mod = page_test_mod.page_test_mod(_w1)

    root.mainloop()

def IncrementButtonOnClick(*args):
    print('page_test_support.IncrementButtonOnClick')
    for arg in args:
        print ('    another arg:', arg)
    sys.stdout.flush()

    #add
    mod.IncrementButtonOnClick()

if __name__ == '__main__':
    page_test.start_up()
```

## 新規追加コード
最後に新規追加でpage_test_mod.pyを作成します。
イベント関数の実行処理はこのファイルに記載します。
**page_test_mod.py**
```python
#! /usr/bin/env python

class page_test_mod:
    def __init__(self, w):
        self.w = w
        pass

    def IncrementButtonOnClick(self):
        s = self.w.CountLabel["text"]
        i = int(s) + 1
        self.w.CountLabel["text"] = str(i)


if __name__ == '__main__':
    pass
```


