---
title: "Xamarin.Formsで画像を表示する"
emoji: "😀"
type: "tech"
topics: ["20180503","xamarin","prism"]
published: true
---

Xamarin.Formsで画像を表示する方法はググるとたくさん出てきますが、なぜか自分の環境だと上手く表示されません。
https://qiita.com/suzu-king/items/a0fe98e7b371e13e49b5

ちなみに自分の環境は下記です。
- Windows10 64bit
- Visual Studio Community 2017
- Xamarin.Forms(UWP + Android + iOS)
- Prism Template Packでひな形を作成

画像が表示されなかったり、"System.ArgumentNullException: 'Value cannot be null.'とエラーが出てしまいます。
(おそらく画像へのPATHが当たっていないと思うのですが、なかなか解決できません。)
![](/images/20180503_xamarin_forms_image/1.png)

よって、ちょっと違う方法で画像を表示させることができたのでその方法を書きます。
まずはXamlのコードを張ります。

Prism Templateをベースとしているので、余計なコードも入っていますが、重要なのはImageタグでSourceをImgにバインディングしていることだけです。
次はViewModelのコードです。
```cs
namespace ShowImage.ViewModels
{
    public class MainPageViewModel : ViewModelBase
    {
        private ImageSource img;
        public ImageSource Img
        {
            get { return img; }
            set { SetProperty(ref img, value); }
        }

        public MainPageViewModel(INavigationService navigationService) 
            : base (navigationService)
        {
            Title = "Main Page";
            Img = ImageSource.FromFile("lena.png");
            
        }
    }
}
```

重要な点は、ImageSource.FromFileを使用している点です。
他の記事ではFromResourceを使用しているものが多いですが、Resourceとしてではなく、ただのファイルとして指定します。
Prismを用いているので、ImgはSetProertyで変更通知を行っています。

次に画像ファイルをプロジェクトに登録します。
![](/images/20180503_xamarin_forms_image/2.png)
![](/images/20180503_xamarin_forms_image/3.png)

ここで1つ目のポイントはForms用の画像ファイルについて
- ビルドアクションは"コンテンツ"(なしでもいいと思います)
- 出力ディレクトリにコピーは"新しい場合はコピーする”
![](/images/20180503_xamarin_forms_image/4.png)


2つ目のポイントはAndroid, iOS, UWPのプロジェクトに画像ファイルを追加するときには、Formsに追加した画像ファイルを"リンクとして追加"することです。
![](/images/20180503_xamarin_forms_image/5.png)

これで各プロジェクトに画像ファイルが登録されますが、実体はFormsに登録してある1つのみにすることができます。
このようにすることでFormsに登録してある画像1つを変更すればすべてのPlatformにおいて変更されます。

初めの方で紹介したURLのやり方がもしできるのであればそれの方がシンプルでよいと思いますが、もしできない場合はこちらの方法も試してもらえればと思います。

参考にしたサイト

http://matatabi-ux.hateblo.jp/entry/2015/05/18/120000
