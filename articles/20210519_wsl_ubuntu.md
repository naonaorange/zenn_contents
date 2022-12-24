---
title: "WSL2でUbuntuを使ってみる"
emoji: "😀"
type: "tech"
topics: ["20210519","wsl","ubuntu"]
published: true
---
以前、WSLのインストールについて記事を書きました。
https://naonaorange.hatenablog.com/entry/2018/11/05/200715

時は流れていつの間にかWSL2になっていたので、今更ですが使ってみます。

# 前提条件

- Windows 10 64bit

- Ubuntu18.04をインストールする

# 初期設定
PowerShellを管理者権限で開きWSL2の機能を有効にする。
```
#WSLを有効にする
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

#仮想マシン機能を有効にする
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
実行後には再起動する。

次にWSL2用Linuxカーネル更新プログラムパッケージのインストールを行う。
https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi


# WSLのデフォルトバージョンをWSL2にする
PowerShellを起動し実行する
```
wsl --set-default-version 2
```

# Windows StoreでUbuntuをインストールする
今回はUbuntu 18.04をインストールします。
![](/images/20210519_wsl_ubuntu/1.png)
インストール後は起動します。初回起動時はユーザーネームとパスワードの設定を促されるため行います。

インストール後はPowerShellでWSLバージョンを確認します。もし1であれば変更してください。
```
#確認方法
wsl --list --verbose

#変更方法
wsl --set-version <distribution name> <versionNumber>
```

# X ServerとしてVcXsrvをインストール
下記サイトよりダウンロードしてインストールします。
https://sourceforge.net/projects/vcxsrv/


# VcXsrvの初期設定
XLaunchを起動します。(名前がVcXsrvではないのでわかりにくいですが、、、)
基本的にデフォルト設定でよいですが、"Additional parameters for VcXsrc"には下記のコマンドを記載します。
![](/images/20210519_wsl_ubuntu/2.png)

```
#-ac: disable access control restrictions
#-nowgl: disable the GLX extension to use the native Windows WGL interface for hardware-accelerated
-ac -nowgl
```

# WSL2 Ubuntu側でのX Server設定
Ubuntuの~/.bashrcに下記設定を追加します。
```sh
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0
```

# FirewallでX Serverの通信を許可する
WindowsのデフォルトではX Serverのポートが塞がれているため、Firewallの設定で許可をします。
この変更はセキュリティーにも影響を及ぼしますので個人の責任でお願いします。
システムとセキュリティー→Windows Defender ファイアーウォール→詳細設定に進みます。
受信の規則で新しい規則を作成します。(他の記事では受信規則の一覧にVcSrvの行がありその設定を変更していましたが、私の環境ではVcSrvの行自体がありませんでしたので、新たに作成します。
設定項目はポートを選択します。
![](/images/20210519_wsl_ubuntu/3.png)

ポートは6000を指定します。
![](/images/20210519_wsl_ubuntu/4.png)

規則の名前はわかりやすい名前にしてください。
![](/images/20210519_wsl_ubuntu/5.png)

さて、これで規則自体は出来上がりました。
しかし、現段階ではどのPCからの通信も許可する内容になっています。
これではセキュリティー的に脆弱なため、せめてプライぺーとアドレスからの通信に限定します。
先ほど作成した規則を右クリックしてプロパティーを開きます。
スコープのリモートIPアドレスを"172.16.0.0/12"に限定します。
![](/images/20210519_wsl_ubuntu/6.png)

* 動作確認
Ubuntu上で実行します。
```sh
xeyes
```
下図のようなウィンドウが表示されればOKです！
![](/images/20210519_wsl_ubuntu/7.png)


* 参考文献

https://docs.microsoft.com/ja-jp/windows/wsl/install-win10

https://skeptric.com/wsl2-xserver/

https://blog-jp.richardimaoka.net/20210411





