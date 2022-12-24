---
title: "自作のMNIST学習モデルを使ってOpenCV for Unityで推論する"
emoji: "😀"
type: "tech"
topics: ["20200427","unity","tensorflow"]
published: true
---
最近Unityを使っていますが、UnityでDeep Learningを使ってみます。

UnityでDeep Learningをするときに色々なライブラリがありますが、今回はOpenCV for Unityを使います。

実際にアプリとかを作る時を考慮して、下のような方針で作っていきます。
+ Deep Learningの学習部分はUnityを使わず、Tensorflowで行い、学習モデルをファイル出力する。
+ OpenCV for Unityが読み込めるモデル形式に変換を行う。
+ OpenCV for Unityで学習モデルを読み込み、推論だけ行う。
+ Windows環境だけではなくAndroid環境でも動作させる(iOSは将来的に対応します。。。)

# 最終的には
事前にファイルに格納されている画像データと推論の結果も表示します。
NEXT IMAGEボタンを押すと、次の画像データに対して表示と推論を行います。
![](/images/20200427_opencv_unity_mnist/1.jpeg)
![](/images/20200427_opencv_unity_mnist/2.jpeg)

最終的なプログラムはgithubにもアップしています。
https://github.com/naonaorange/unity_dnn_samples/tree/master/CvDnnMnist


# 学習モデルを作成する
ここはUnityは関係ないですが、Tensorflowを用いてMNISTの学習モデルを自分で作ります。
ここでは基本的な3層の全結合層を作成しました。

学習についてのコードなどは下記を確認してください。
https://github.com/naonaorange/tensorflow_samples/blob/master/mnist.ipynb

jupyter notebookのコードを自分で実行しても良いですが、学習モデルデータだけ欲しいときは下記のURLからダウンロードしてください。
tensorflowのversionは2.x系を使用してください。
https://github.com/naonaorange/tensorflow_samples/blob/master/models/mnist.h5:title

# 学習モデルの変換を行う
前章で作成した学習モデルの形式はh5でした。
しかし、OpenCV for Unityではこの形式に対応していません。
よって、pb形式に変換します。

h5からpbに変換するpythonツールを作成しましたので、実行するだけで作成できます。
注意点としては、本ツールを使用するときにはtensorflow 1.x系を使っているということです。
(tensorflow 2.xでの動作確認がないだけで、動くかもしれません。)
https://github.com/naonaorange/dnn_model_converter

# Mnist datasetの画像データをファイルに出力する
pythonでtensorflowを使用している分にはネットからMnist datasetから入力する画像データをダウンロードできます。

しかし、Unity環境下でMnist datasetをダウンロードするのは大変なので、
pythonで入力データをcsvファイルに出力し、Unityではこのファイルから画像データを作ります。

csvファイルのフォーマットとしては、1行で1画像分です。
28*28の画像ですので、748列あります。

https://github.com/naonaorange/tensorflow_samples/blob/master/export_mnist_dataset_to_csv.ipynb

こちらも、出力されたcsvファイルだけダウンロードしたい方はこちらから
https://github.com/naonaorange/tensorflow_samples/blob/master/data/mnist_dataset.csv:title


# Unityプロジェクトでの初期設定

## OpenCV for Unityのimportする
新規プロジェクトを作成したら、Asset StoreからOpenCV for Unityをimportしてください。

## UIの作成
RawImage, Text, Butttonを追加します。
![](/images/20200427_opencv_unity_mnist/3.jpeg)

## 必要なファイルを追加する
mnist.pbファイルはAssets/StreamingAssets内に、mnist_dataset.csvファイルはAsset/Resources内に追加します。
![](/images/20200427_opencv_unity_mnist/4.jpeg)

# C# Scriptを作成する
RawImageController.csを作成し、UIのRawImageにアタッチします。

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using System.IO;

using OpenCVForUnity.CoreModule;
using OpenCVForUnity.UnityUtils;
using OpenCVForUnity.DnnModule;

public class RawImageController : MonoBehaviour
{
    private Texture2D texture;
    GameObject answerText;

    private List<List<byte>> mnist_dataset;
    private int mnist_dataset_idx;

    private Net net;
    private const int IMG_WIDTH = 28;
    private const int IMG_HEIGHT = 28;
    private const string MODEL_FILE_PATH = "mnist.pb";

    // Start is called before the first frame update
    void Start()
    {
        answerText = GameObject.Find("Text");
        mnist_dataset_idx = 0;
        mnist_dataset = read_mnist_dataset();

        string model_filepath = Utils.getFilePath(MODEL_FILE_PATH);
        net = Dnn.readNetFromTensorflow(model_filepath);
        if (net.empty()) Debug.LogError("model file is not loaded.");

        show_image();
        predict();
    }

    // Update is called once per frame
    void Update()
    {
    }

    public void go_to_next_image()
    {
        mnist_dataset_idx += 1;
        if (mnist_dataset.Count <= mnist_dataset_idx) mnist_dataset_idx = 0;

        show_image();
        predict();
    }

    void show_image()
    {
        List<byte> mnist_data = mnist_dataset[mnist_dataset_idx];
        Mat disp_img = new Mat(IMG_HEIGHT, IMG_WIDTH, CvType.CV_8UC3);
        for(int y = 0; y < IMG_HEIGHT; y++)
        {
            for(int x = 0; x < IMG_WIDTH; x++)
            {
                byte p = mnist_data[y * IMG_HEIGHT + x];
                disp_img.put(y, x, new byte[3] { p, p, p });
            }
        }
        texture = new Texture2D(IMG_WIDTH, IMG_HEIGHT);
        Utils.matToTexture2D(disp_img, texture);
        GetComponent<RawImage>().texture = texture;

        /*
        //Matを使用せずtextureに画素を格納する
        texture = new Texture2D(IMG_WIDTH, IMG_HEIGHT);
        List<float> data = mnist_data[1];
        List<List<float>> d = convert_data(data, true);
        Debug.Log(d.Count + " , " + d[0].Count);

        for(int y = 0; y < IMG_HEIGHT; y++)
        {
            for(int x = 0; x < IMG_WIDTH; x++)
            {
                texture.SetPixel(x, y, new Color(d[y][x], d[y][x], d[y][x]));
            }
        }
        texture.Apply(false);
        GetComponent<RawImage>().texture = texture;
        */
    }

    void predict()
    {
        List<byte> mnist_data = mnist_dataset[mnist_dataset_idx];

        Mat input_img = new Mat(1, IMG_HEIGHT * IMG_WIDTH, CvType.CV_32FC1);
        for(int i = 0; i < mnist_data.Count; i++)
        {
            float p = (float)mnist_data[i] / 255.0f;
            input_img.put(0, i, p);
        }

        Mat blob = Dnn.blobFromImage(input_img);
        net.setInput(blob);
        Mat prob = net.forward();

        (int max_idx, float max_value) = get_max_idx(prob);
        answerText.GetComponent<Text>().text = "idx : " + max_idx + " , value : " + max_value.ToString("F2");
    }

    List<List<byte>> read_mnist_dataset()
    {
        TextAsset csv = Resources.Load("mnist_dataset") as TextAsset;
        StringReader reader = new StringReader(csv.text);
        List<List<byte>> data = new List<List<byte>>();

        while(reader.Peek() != -1)
        {
            string[] str_line = reader.ReadLine().Split(',');
            List<byte> line = new List<byte>();
            foreach(string str in str_line)
            {
                line.Add(byte.Parse(str));
            }
            data.Add(line);
        }
        return data;
    }

    List<List<float>> convert_data( List<float> data_array,
                                    bool is_texture_axis=false)
    {
        List<List<float>> d = new List<List<float>>();
        for (int i = 0; i < IMG_HEIGHT; i++) d.Add(new List<float>());
        
        for(int y = 0; y < IMG_HEIGHT; y++)
        {
            for(int x = 0; x < IMG_WIDTH; x++)
            {
                if (is_texture_axis)
                {
                    d[IMG_HEIGHT - y -1].Add(data_array[y * IMG_HEIGHT + x]);
                }
                else
                {
                    d[y].Add(data_array[y * IMG_HEIGHT + x]);
                }
            }
        }
        return d;
    }

    (int idx, float value) get_max_idx(Mat prob)
    {
        int max_idx = 0;
        float max_value = 0.0f;

        for(int i = 0; i < prob.width(); i++)
        {
            float tmp = (float)prob.get(0, i)[0];
            if(max_value < tmp)
            {
                max_value = tmp;
                max_idx = i;
            }
        }
        return (max_idx, max_value);
    }
}
```

ここでのポイントとしては、

### 表示用の画像データ作成について
RawImageへの表示データをmnist datasetから作成しています。
手順としては、mnist datasetのデータをMatに格納し、texuture2dに変換して表示します。
今回のサンプルではわざわざMatを経由する必要はないですが、追加の画像処理が必要であれば、Mat形式で行うと簡単です。

ちなみにMat形式を介さないで、texture2dに直接格納することもできます。その際にはコメントアウトしているコードを使ってください。
この際にはMatとtexture2dでは画素の配列順番が異なりますので、注意してください。
配列順番については下記の記事が詳しいです。
https://qiita.com/utibenkei/items/d1050253fc586f17f0ec

```cs
        Mat disp_img = new Mat(IMG_HEIGHT, IMG_WIDTH, CvType.CV_8UC3);
        for(int y = 0; y < IMG_HEIGHT; y++)
        {
            for(int x = 0; x < IMG_WIDTH; x++)
            {
                byte p = mnist_data[y * IMG_HEIGHT + x];
                disp_img.put(y, x, new byte[3] { p, p, p });
            }
        }
        texture = new Texture2D(IMG_WIDTH, IMG_HEIGHT);
        Utils.matToTexture2D(disp_img, texture);
```

### 推論の入力データについて
入力データを作成する際には、学習時の入力データと合わせることが必要だと思います。
学習の時には、入力データが728(画像データ28*28を1次元配列にしたもの)で、0から1に正規化しました。
それに合わせて、CV_32F1を使い、255で割って正規化した入力データを作成しています。
```cs
        Mat input_img = new Mat(1, IMG_HEIGHT * IMG_WIDTH, CvType.CV_32FC1);
        for(int i = 0; i < mnist_data.Count; i++)
        {
            float p = (float)mnist_data[i] / 255.0f;
            input_img.put(0, i, p);
        }
```

## ButtonのonClickイベントを登録する
ButtonのonClickイベントにgo_to_next_image関数を登録します。

# 参考
https://www.slideshare.net/takmin/run-keras-model-on-opencv

https://qiita.com/yanosen_jp/items/4a35f31831b7f7b5d0cd

https://qiita.com/yoda/items/05b58a8f439f478a6210






