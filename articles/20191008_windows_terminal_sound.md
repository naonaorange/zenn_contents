---
title: "Windows10でターミナルを使っているときに警告音が鳴るのを停止する"
emoji: "😀"
type: "tech"
topics: ["20191008","windows"]
published: true
---
Windows10で下記のようなターミナル系のソフトを使用した時、タイプミス等で警告音が出ます。
特にVimはEscを押しただけで警告音が出るので、煩わしいです。
それを消す方法を書いておきます。
- WSL

- Vim

- Teraterm

- PowerShell

設定からサウンドを選択し、サウンドコントロールパネルを開きます。
![](/images/20191008_windows_terminal_sound/1.png)

「一般的な警告」のサウンドをOFFにします。
![](/images/20191008_windows_terminal_sound/2.png)

これでOKです。
もしこれでできないようであれば、「一般的な警告」ではなく、他の警告をOFFにしていけばよいと思います。
音はテストボタンで確認できるので、煩わしい音を探してOFFにしていけばOK。
