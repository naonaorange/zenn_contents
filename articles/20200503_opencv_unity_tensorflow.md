---
title: "OpenCV for UnityでTensorflowのmodelを読み込む"
emoji: "😀"
type: "tech"
topics: ["20200503","unity","opencv","tensorflow"]
published: true
---
OpenCVにはDNNモジュールがあり、Tensorflowの学習済みmodelを読み込むことができます。
OpenCV for Unityを使う場合は、下記のようにできます。
```cs
using OpenCVForUnity.UnityUtils;

string MODEL_FILENAME = "model.pb";
string model_filepath = Utils.getFilePath(MODEL_FILENAME);
Net net = Dnn.readNetFromTensorflow(model_filepath);
```

getFilePath関数のコメントを見ると、"StreamingAssets"フォルダを作成してそこからの相対パスを入力すると、絶対パスを出力するみたいですね。
絶対パスは実行するプラットフォームによって変わってしまうので、この関数でその違いを吸収しているようです。

StreamingAssetsフォルダはOpenCV for Unityに限らず、UnityとしてPATHが決まっているようで、下記で確認することができます。
```cs
Debug.Log(Application.streamingAssetsPath);
```
私のWindows環境下では下記のように出力されました。
ResourceTest2というのはプロジェクト名なので、Assetsフォルダ直下にStreamingAssetsフォルダを作成しなければいけないみたいですね。
```
C:/Users/naolu/Documents/Unity/ResourceTest2/Assets/StreamingAssets
```
実際にその他の場所にStreamingAssetsを置いても、Utils.getFilePathからは空文字しか返らず、モデルのPATHを取得することができませんでした。


