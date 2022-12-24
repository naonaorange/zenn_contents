---
title: "wxpythonを使ったGUIアプリをpyinstallerでexeファイルにしたらエラーで動かない"
emoji: "😀"
type: "tech"
topics: ["20190817","python"]
published: true
---
最近はpythonでGUIアプリを作るときにはwxpythonを使っています。
pyinstallerでexeファイル作成まではうまくいくのですが、実行すると下記エラーで動かないです。
```
ModuleNotFoundError: No module named 'wx._adv'
[10536] Failed to execute script main
```
その対処法として、下記ライブラリをmainでインクルードすると動きます。
```python
#import wx._adv
#import wx._html
```
下記サイトを参考にしました。
このサイトでは上記2つのほかにwx._xmlをインクルードしろと書いていますが、
自分の環境では上記2つのみでうまくいきました。
https://github.com/wxWidgets/Phoenix/issues/289

