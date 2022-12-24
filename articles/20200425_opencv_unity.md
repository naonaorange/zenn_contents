---
title: "OpenCV for Unityでカメラ画像を表示する"
emoji: "😀"
type: "tech"
topics: [Unity]
published: true
---
画像処理を行う前段階として、カメラ画像を取得します。

# 前提条件
- Unity 2018.4.21f1
- プロジェクトタイプ 2D

# 手順
Hierachy内は下図のようにRawImageとButtonを追加します。
![](/images/20200425_opencv_unity/1.jpeg)

次にC# ScriptとしてRawImageController.csを作成します。
カメラ画像表示のほかにChangeCamera関数を呼ぶとカメラデバイスの切り替えを行います。
スマホで実行した際には内側/外側のカメラを切り替えることができます。

```cs
using UnityEngine;
using UnityEngine.UI;

public class RawImageController: MonoBehaviour
{
    WebCamTexture webCamTexture;
    WebCamDevice[] webCamDevice;
    int selectCamera = 0;

    void Start()
    {
        webCamTexture = new WebCamTexture();
        webCamDevice = WebCamTexture.devices;

        webCamTexture = new WebCamTexture(webCamDevice[selectCamera].name);
        GetComponent<RawImage>().texture = webCamTexture;
        webCamTexture.Play();

        ChangeCamera();
    }

    public void ChangeCamera()
    {
        int cameras = webCamDevice.Length;
        if (cameras < 1) return;

        webCamTexture.Stop();
        webCamTexture = new WebCamTexture(webCamDevice[selectCamera].name);
        GetComponent<RawImage>().texture = webCamTexture;
        webCamTexture.Play();

        selectCamera++;
        if (selectCamera >= cameras) selectCamera = 0;
    }
}
```

RawImageController.csをRawImageにアタッチします。

また、ButtonをクリックしたときにChangeCamera関数を呼ぶようにしたいので、
ButtonのInspector内で設定します。
On Click()にRawImageを登録し、関数にChangeCameraを指定してください。
![](/images/20200425_opencv_unity/2.jpeg)

https://scrapbox.io/ConsoleSoup/OpenCV_for_Unity%E3%81%A7%E3%82%AB%E3%83%A1%E3%83%A9%E6%98%A0%E5%83%8F%E3%82%92%E6%8F%8F%E7%94%BB


