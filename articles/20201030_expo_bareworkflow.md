---
title: "expoのbare workflowでhello worldしてみる"
emoji: "😀"
type: "tech"
topics: ["20201030","expo","reactnative","ios"]
published: true
---
これまでexpoのmanaged workflowでスマホアプリを作成していましたが、managed workflowでは使えない機能を使いたくてbare workflowでアプリを作成したいと思っています。
まずはbare workflowでhello worldまでやっていきたいとおもいます。

# アプリ作成
expoコマンドでアプリの雛型を作成していきます。
```
expo init hello_expo_bare_workflow

? Choose a template: 
  ----- Managed workflow -----
  blank                 a minimal app as clean as an empty canvas 
  blank (TypeScript)    same as blank but with TypeScript configuration 
  tabs (TypeScript)     several example screens and tabs using react-navigation and TypeScript 
  ----- Bare workflow -----
❯ minimal               bare and minimal, just the essentials to get you started 
  minimal (TypeScript)  same as minimal but with TypeScript configuration 
```

# expoを利用してのデバッグ
bare workflowで作成したアプリであっても、expo startで実行できます。
しかし、expo上で動作しない部分に関しては動作するようにダミープログラムを用意しておかなければいけないです。
hello worldをする分にはexpoでも動作します。
```
cd hello_expo_bare_workflow
expo start
```

# XCodeを利用してのデバッグ
ios/helloexpobareworkflow.xcworkspaceをXCodeで開きます。
そのままビルドをすると、下記のようなエラーが出てくるので適宜修正します。
初めから全て修正してからビルドしても良いです。
`
 Error: cannot find the node binary. Try setting the NODE_BINARY variable in the  "Bundle React Native code and images" Build Phase to the absolute path to your node binary.
`
NODE_BINARYにPATHが通っていなく、nodeが見つからない事によるエラーです。
nodeのある場所にPATHを通す必要があります。
nodebrewでnode.jsをインストールした場合は下記PATHを追加すればOKです。
/Users/USER_NAME/.nodebrew/current/bin/node
![](/images/20201030_expo_bareworkflow/1.png)

`
Showing Recent Messages Signing for "helloexpobareworkflow" requires a development team. Select a development team in the Signing & Capabilities editor.
`
実機デバッグを行うとこのエラーが表示されます。
有償のApple Developer Programへの登録が必要です。
![](/images/20201030_expo_bareworkflow/2.png)




