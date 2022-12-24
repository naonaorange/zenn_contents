---
title: "ハリーポッター組分け帽子BOTを作って公開してみた"
emoji: "😀"
type: "tech"
topics: ["20191225","sortinghat"]
published: true
---

# はじめに
この記事はLINE Bot Advent Calendar 2019の15日目の記事になります。
空いていたのでここに登録してみました。
https://qiita.com/advent-calendar/2019/linebot


また、このアプリケーションは2020/01/25のSendai Micro Maker Faire 2020に出品予定です。
https://makezine.jp/blog/2019/11/sendaimmf2020_callformakers.html
https://www.facebook.com/events/1089437081263517/



# ハリーポッター組み分け帽子BOTって何？
ハリーポッターの世界に行ってみたいと思ったことはありますか？
ホグワーツには"グリフィンドール"、"レイブンクロー"、"ハッフルパフ"、"スリザリン"という4つの寮があります。映画の作品を見てみると、その寮毎に生徒の顔だったり雰囲気だったりがなんとなく違いがあるなと思うのは私だけではないと思います。

そんなあなたに顔が写っている画像からその人がどの寮の生徒なのかを判断するLINE BOT「ハリーポッター組み分け帽子BOT」を作成しました！
![](/images/20191225_harrypotter_bot/1.png)

youtubeにも紹介動画をアップロードしていますので見てみてください。
https://www.youtube.com/watch?v=iVcMoCO2NOA

LINE BOTは既に公開済みなので、お友達になるだけで使ってみることができます。
https://lin.ee/i8OBQMp
QRコードをLINEで読み込んでも友達になれます。
![](/images/20191225_harrypotter_bot/2.png)

* システム構成
![](/images/20191225_harrypotter_bot/3.jpeg)
利用者が顔画像データをLINEで送信すると、Herokuサーバー上のPythonプログラムがMessaging APIを使って受信します。
次に、受信した画像に対してOpenCVで顔の領域を検出し、予めTensorflowで機械学習したモデルでどの寮の生徒かを判断します。
最後にその結果と処理を行った顔画像を返信します。

機械学習のモデルは事前に学習したものを使用していますので、LINEで送られてきた画像を学習に使うなどはしていません。
ネット上にあるハリーポッターの映画やキャストの画像を集め、各寮の人たちの顔画像から判別機を作成しました。
下の画像はグリフィンドールの寮生の顔画像を集めたものです。
![](/images/20191225_harrypotter_bot/4.png)

# ハリーポッターの寮について
まずは、ハリーポッターのことをあまり知らない方のために、少し説明します。
ハリーポッターの通うホグワーツ魔法学校には4つの寮があり、生徒はその中のどれか1つの寮に所属します。
物語の中では、生徒1人1人が魔法の能力を持つ「組分け帽子」を被り、組分け帽子がその人の潜在能力から寮を判断します。

## グリフィンドール
主人公のハリーポッターのいる寮。勇気・決断力に優れているが、時には無謀な行動に出る傾向がある。
https://harrypotter.fandom.com/ja/wiki/%E3%82%B0%E3%83%AA%E3%83%95%E3%82%A3%E3%83%B3%E3%83%89%E3%83%BC%E3%83%AB

## ハップルパフ
勤勉、忠誠心に優れている。寛容で競争をあまり好まない。
https://harrypotter.fandom.com/ja/wiki/%E3%83%8F%E3%83%83%E3%83%95%E3%83%AB%E3%83%91%E3%83%95

## レイブンクロー
知識、知恵に優れた人が入れる。独創性を重んじるが、一風変わった行動を取ってしまうことがある。
https://harrypotter.fandom.com/ja/wiki/%E3%83%AC%E3%82%A4%E3%83%96%E3%83%B3%E3%82%AF%E3%83%AD%E3%83%BC

## スリザリン
野心、狡猾さに優れている。成果主義の傾向がとても強い。
https://harrypotter.fandom.com/ja/wiki/%E3%82%B9%E3%83%AA%E3%82%B6%E3%83%AA%E3%83%B3

# プログラム
ここですこしだけプログラムの説明を行います。
基本的にはLINE BOT SDK for Pythonのサンプルプログラムを使用しています。
画像のURLが送られてきた時にはTextMessageのEventが上がります。
はじめにURLが画像へのリンクなのかどうかを確認し、一時的なファイルに書き込みます。
画像へのリンクであることを確認出来たら、execute関数を実行します。
この関数の説明は下で行います。
```python
@handler.add(MessageEvent, message=TextMessage)
def handle_text_message(event):
    is_input_message_ok = False
    url = ""
    img_path = ""

    #Check the input message
    if not isinstance(event.message, TextMessage):
        pass
    else:
        url = event.message.text

        #Check the input message
        if len(url) > 8:
            if url[:8] == "https://" or url[:7] == "http://":
                try:
                    res = requests.get(url)
                    res.raise_for_status()
                    content_type = res.headers['content-type']
                    if 'image' in content_type:
                        ext = mimetypes.guess_extension(content_type)
                        if ext == ".bmp" or ext == ".jpe" or ext == ".jpeg" or ext ==".jpg" or ext == ".png" or ext == ".tiff" or ext == ".tif":
                            #Create the temp file to save the input file.
                            with tempfile.NamedTemporaryFile(dir=static_tmp_path, delete=False) as tf:
                                tf.write(res.content)

                            img_name = os.path.basename(tf.name) + ext
                            img_path = os.path.join('static', 'tmp', img_name)
                            os.rename(tf.name, img_path)

                            is_input_message_ok = True
                except Exception as ex:
                    pass

    #Execute sorting
    if is_input_message_ok == True:
        execute(event, img_path)
    
    else:
        line_bot_api.reply_message(
            event.reply_token, [
                TextSendMessage(text='画像のURLを送ってください。')
        ]   )
```


こちらは画像データが直接送られてきた場合です。
こちらも一時的なファイルに画像を書き込み、execute関数を実行します。
```python
@handler.add(MessageEvent, message=ImageMessage)
def handle_content_message(event):
    is_input_message_ok = False
    img_path = ''

    #Check the input message
    if not isinstance(event.message, ImageMessage):
        pass
    else:
        message_content = line_bot_api.get_message_content(event.message.id)

        #Create the temp file to save the input file.
        with tempfile.NamedTemporaryFile(dir=static_tmp_path, delete=False) as tf:
            for chunk in message_content.iter_content():
                tf.write(chunk)
        
            img_name = os.path.basename(tf.name) + '.jpg'
            img_path = os.path.join('static', 'tmp', img_name)
            os.rename(tf.name, img_path)

        is_input_message_ok = True

    #Execute sorting
    if is_input_message_ok == True:     
        execute(event, img_path)
    
    else:
        line_bot_api.reply_message(
            event.reply_token, [
                TextSendMessage(text='別の画像を送ってください。')
        ]   )
```
こちらは上のプログラムで使用していたexecute関数です。
ここでは画像処理と機械学習の部分を行う自作のsorting_deep_hatモジュールのインスタンスであるsdhに対して処理を行っています。
診断が正常にできたときには次を行います。
・寮の名前を画像に描画し、imagemessageとして利用者に返信
・寮の名前をhouse_namesに格納し、textmessageとして利用者に返信

sorting_deep_hatモジュールの詳細については機械学習の話になり、長くなってしまうので、最後にGithubのリポジトリを確認してください。
```python
def execute(event, img_path):
    sdh.estimate(img_path)

    #Can not detect the face
    if len(sdh.result_data) == 0:
        line_bot_api.reply_message(
            event.reply_token, [
                TextSendMessage(text='別の画像を送ってください。\n例えば、写っている顔が小さい場合は判断できない場合があります。')
        ]   )
    else:
        sdh.draw(img_path)

        house_names = ''
        i = 0
        for (x, y, w, h, hn) in sdh.result_data:
            house_names += sdh.get_house_name_in_japanese(hn)
            house_names += ' !'
            if i < len(sdh.result_data) - 1:
                house_names += '\n'
            i += 1

        img_url = request.host_url + img_path
        img_url = 'https' + img_url[4:] # http -> https

        line_bot_api.reply_message(
            event.reply_token, [
            TextSendMessage(text=house_names),
            ImageSendMessage(original_content_url=img_url, preview_image_url=img_url)
        ])
        
    sdh.release_internal_data()

if __name__ == "__main__":
    port = int(os.getenv("PORT", 5000))
    app.run(host="0.0.0.0", port=port)
#    app.run(host="127.0.0.1", port=port)
```

# 実際に使ってみよう
実際にやってみましょう。

まずはハリーポッターの画像を送ってみました。結果としてはきちんとグリフィンドールと判断されています。
![](/images/20191225_harrypotter_bot/5.jpeg)
石原さとみさんはレイブンクロー、頭がよくてかわいいとか最強ですね！
![](/images/20191225_harrypotter_bot/6.jpeg)

トランプ大統領はスリザリン、うん。なんか分かる(笑)
![](/images/20191225_harrypotter_bot/7.jpeg)

マツコ・デラックスさんはレイブンクロー！人とは違う道を歩む傾向があるのはレイブンクロー気質なのかも。
![](/images/20191225_harrypotter_bot/8.jpeg)

次は嵐のメンバーです。きちんと顔が認識されれば複数人でも同時に診断できます。
ただ、顔が小さくなるので認識されるのが難しくなる傾向にあります。
嵐のメンバーはグリフィンドールとハッフルパフから成り立っているんですね！
![](/images/20191225_harrypotter_bot/9.jpeg)

アインシュタインはレイブンクロー！知識・知恵に優れたこの寮にぴったりの人ですね
![](/images/20191225_harrypotter_bot/10.jpeg)

ガッキーはグリフィンドール！かわいいからどの寮でもOK(笑)
![](/images/20191225_harrypotter_bot/11.jpeg)

# 最後に
どうでしたでしょうか？
LINEで友達になればすぐに試せますので、良ければ皆さんのスマホでもやってみてはどうでしょうか？

今回はアプリケーションの説明を中心に行い、詳細のプログラムの内容は省きました。
このアプリケーションは少し前からちょくちょく改良を行っていますので、プログラムの詳細については過去の記事を見ていただけたらと思います。
https://naonaorange.hatenablog.com/entry/2019/12/17/200119

Sendai Micro Maker Faire 2020に出品した際のポスターを置いておきます。
![](/images/20191225_harrypotter_bot/12.png)

また、プログラム自体はGithubに公開しています。
https://github.com/naonaorange/sorting-deep-hat-bot

こちらは機械学習で判別のモデル作成のためのリポジトリです。
https://github.com/naonaorange/sorting_deep_hat



