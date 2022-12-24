---
title: "pyinstallerでexeファイルを作成する"
emoji: "😀"
type: "tech"
topics: ["20190817","python"]
published: true
---
pythonでプログラムを書く機会が多いですが、
pythonの問題点として、実行するPCにpythonがインストールされていないといけないということがあります。
また、pythonのバージョンが変わると動作が異なることもあるため、
他の環境ではエラーになってしまう可能性もあります。
(Visual Studioでアプリを作るのに比べると面倒なことが多いですね。)

そこで、pyinstallerというソフトを使用して、pythonファイルをexeファイルに変換します。
pyinstallerはpipでインストールできます。
```sh
pip install pyinstaller
```
簡単な使い方としては、メインのpythonファイルを引数に置くだけです。
この場合、dist\PYTHON_FILE_NAME\PYTHON_FILE_NAME.exeというexeファイルが作成されます。
```sh
pyinstaller PYTHON_FILE_NAME
```
オプションなどを駆使する場合は、下記がおすすめです。
```sh
pyinstaller PYTHON_FILE_NAME -n EXE_FILE_NAME --onefile --noconsole --clean
```
オプションの説明は
- n： exeファイルのファイル名
- onefile： 実行ファイルを1つにまとめる。実行ファイルを他の環境に移す際にファイル1つの方がやりやすいです。しかし、ファイル容量は大きくなります。
- noconsole： exeファイルを実行時、コンソール画面を非表示にします。
- clean： exeファイル作成を行う前に作業フォルダをcleanしてから作成します。
