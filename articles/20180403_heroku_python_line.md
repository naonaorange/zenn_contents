---
title: "Heroku, PythonでLine Botを作る"
emoji: "😀"
type: "tech"
topics: [Python,ハリーポッター組み分け帽子BOT]
published: true
---
ここでは送信したメッセージをオウム返しするLine Botを作ります。
基本的に他のブログの引用ですが、体系的にまとめました。

## Lineアカウント作成
Botを作るためには、下記の2アカウントを作る必要があります。
- Line Business
- Messasing API

ここは下記URLを参考に
https://qiita.com/yoshizaki_kkgk/items/bd4277d3943200beab26

## Herokuアカウント作成
下記URLを参考に
https://j-hack.gitbooks.io/deploy-meteor-app-to-heroku/content/step1.html

## Line Bot作成
基本的には下記URLと同じです。
https://qiita.com/akabei/items/38f974716f194afea4a5

しかし、この説明ではHeroku Gitのみを使用していますが、ここではGithubとHerokuを連携させて使用します。

## Githubレポジトリ作成・実装
まずは、Githubで連携したいレポジトリを新規作成します。
そのレポジトリ内に上記URLの「実装」章を参考にして、ファイルの追加・編集を行い、レポジトリにpushしてください。

次にブラウザでHerokuのアカウントにログインをして、"Create new app"等を押して、アプリケーションを作成します。
設定画面の"Deploy"タブ内のDevelopment methodでGithubを選び、先ほど作成したレポジトリを設定します。

## 初期設定・デプロイ

まずは初期設定を行います。下記コマンドでHerokuにログインします。
参考にしているサイトではheroku createでアプリケーションを作成していますが、先ほどブラウザ上で作成しましたので不要です。
```sh
heroku login
```
次に同じようにLINEのアカウント設定を環境変数に追加する必要があるのですが、、、
- LINE_CHANNEL_SECRET
- LINE_CHANNEL_ACCESS_TOKEN

参考にしたサイトと同じようにconfig:setのコマンドを打ってもエラーになってしまいます。。。
これは、heroku createをCLIで行っていないため、config:setコマンドがどのHerokuアプリケーションを対象にしているのかわからないためだと思います。

よって、下記のように変更して実行します。
```sh
heroku config:set LINE_CHANNEL_SECRET="チャンネルシークレット" -app アプリケーション名
heroku config:set LINE_CHANNEL_ACCESS_TOKEN="アクセストークン" -app アプリケーション名
```

その後、ブラウザ上で手動でデプロイします。

最後に、同じようにLine DevelopperサイトのChannel基本設定画面で、Webhook URLを登録します。
ここで注意するのは、URLは下記のように最後に"callback"がつきます。
(自分はこれを追加しないで長い間悩んでしまいました。。。)

`https://アプリケーション名.herokuapp.com/callback`

これで自分のスマホでBotを友達にして、メッセージを投げるとオウム返しします。