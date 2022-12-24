---
title: "ARCore for Unityでカメラ画像を取得する方法"
emoji: "😀"
type: "tech"
topics: ["20200503","arcore","unity","opencv"]
published: true
---
ARCoreを使ってアプリを作っていると、カメラ画像を取得したくなります。
サンプルを見てもその方法が分からなかったので、確認してみました。

ググってみると下記の良い記事がヒットします。
私の場合もこれと同じ方法でできました。
https://qiita.com/nshinya/items/e5d0cf3c4baf90452ef5
https://kgarage.hatenablog.com/entry/2019/10/22/234802

基本的にこの方法でOKです。
しかし所持するGalaxyS8ではこの方法をそのまましてしまうと、画像が上下逆転してしまいました。
参考にした記事では、TextureReaderで取得したTexture2Dの画像が270度回転していると書いてあり、ソフト処理で元に戻しています。
しかし、GalaxyS8では270度ではなく、90度回転していました。
(もしかすると、機種で違いがあるのかもしれません。)

よって、Texture2Dで画像を取得した後に、処理を変更しています。
この方法ではOpenCV for Unityを使用して画像処理をしています。
```cs
using OpenCVForUnity.CoreModule;
using OpenCVForUnity.UnityUtils;


Texture2D texture = this.textureRender.FrameTexture;
if (texture == null) return false;

Mat srcImg = new Mat(texture.height, texture.width, CvType.CV_8UC3);
Utils.texture2DToMat(texture, srcImg);

Core.rotate(srcImg, srcImg, Core.ROTATE_90_COUNTERCLOCKWISE);
```


