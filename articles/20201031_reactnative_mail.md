---
title: "React Nativeでインストール済のメールアプリのメールボックスを開く"
emoji: "😀"
type: "tech"
topics: ["20201031","reactnative"]
published: true
---
スマホ内に既にインストールされているメールアプリのメールボックスを開きたいと思ったのでいろいろ調べてみたのですが、なかなか思い通りに動くものがありません。
下記の動作で良いのであれば、Expoでも作成できるのですが、任意のメールアプリのメールボックスを開くものってなかなかないんですね。
- メール作成画面を直接開くもの
- iOSの場合は各種メールアプリを開くのではなくAppleのデフォルトメールアプリを開くもの
いろいろ調べた結果、やっと見つけたので実際にAndroid, iOSで使ってみた内容を記載してみます。

# 前提条件
Expoのbare workflowを使っていきます。
React Nativeそのものでも動作すると思います。

# ライブラリのインストール
react-native-email-linkというライブラリを使っていきます。
使い方はreadmeに全て記載されており、この記事はそれを実際に行なってみたものになります。
https://github.com/flexible-agency/react-native-email-link
まずはインストールします。
```sh
npm install react-native-email-link
```

# コード編集
## App.jsの編集
テストとしてメールボックスを開くだけのボタンを作ります。
```javascript
import { openInbox } from 'react-native-email-link';

<Button title='open mailbox' onPress={() => {openInbox()}}></Button>
```

## info.plistの編集
プロジェクト内のinfo.plistをSource Codeとして開きます。
![](/images/20201031_reactnative_mail/1.png)

色々な設定が辞書型で定義されています。
その中にLSApplicationQueriesSchemesを追加します。
react-native-email-linkで起動できるアプリの一覧になります。
```xml
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>message</string>
    <string>readdle-spark</string>
    <string>airmail</string>
    <string>ms-outlook</string>
    <string>googlegmail</string>
    <string>inbox-gmail</string>
    <string>ymail</string>
    <string>superhuman</string>
</array>
```
![](/images/20201031_reactnative_mail/2.png)
コード編集はこれでOKです。

# デバッグ
実際に動かしてみます。
Androidの場合はシミュレータと実機、iOSでは実機で動作を確認しました。
iOSのシミュレータではデフォルトではメールアプリが何もインストールされていないので動かないようです。




