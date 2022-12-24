---
title: "OpenCV for Unityで画像を表示する"
emoji: "😀"
type: "tech"
topics: ["20200425","unity"]
published: true
---
Unityで画像処理を行いたい時にOpenCV for Unityが良く使われるそうです。
使い方を確認するために、Unity上のtextueをOpenCVのMat形式に直して、
それをtexture形式に戻し、表示してみました。
Mat形式に直した後に画像処理を行えば、処理後の画像を表示することができます。

# 前提条件
Unity 2018.4.21f1
プロジェクトタイプ 2D

# インストール
- Asset StoreからOpenCV for Unityを購入する。(今なら半額で＄50くらいで購入できます。)
- Asset StoreからOpenCV for Unityをimportする

* 手順
Hierarchyは下のようにRawImageを追加します。
![](/images/20200425_opencv_unity_image/1.jpeg)

C# ScriptでRawImageController.csを作成します。

```cs
using UnityEngine;
using UnityEngine.UI;
using OpenCVForUnity.CoreModule;
using OpenCVForUnity.UnityUtils;
using OpenCVForUnity.ImgprocModule;

public class RawImageController : MonoBehaviour
{
    void Start()
    {
        Texture2D srcTexture = Resources.Load("lena") as Texture2D;
        Mat srcMat = new Mat(srcTexture.height, srcTexture.width, CvType.CV_8UC3);
        Utils.texture2DToMat(srcTexture, srcMat);

        Imgproc.putText(srcMat, "W:" + srcMat.width() + " H:" + srcMat.height(),
                        new Point(5, srcMat.rows() - 10),
                        Imgproc.FONT_HERSHEY_SIMPLEX, 1.0, new Scalar(255, 255, 255, 255), 2, Imgproc.LINE_AA, false);

        Texture2D texture = new Texture2D(srcMat.cols(), srcMat.rows(), TextureFormat.RGBA32, false);
        Utils.matToTexture2D(srcMat, texture);
        GetComponent<RawImage>().texture = texture;
    }

    void Update()
    {
        
    }
}
```

最後にRawImageController.csをRawImageにアタッチすればOKです。
![](/images/20200425_opencv_unity_image/2.jpeg)

https://scrapbox.io/ConsoleSoup/OpenCV_for_Unity%E3%81%A7%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E7%94%BB%E5%83%8F%E8%AA%AD%E3%81%BF%E8%BE%BC%E3%81%BF

