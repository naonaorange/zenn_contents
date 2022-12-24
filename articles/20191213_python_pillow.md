---
title: "pillowで塗りつぶしのない四角形を描画する"
emoji: "😀"
type: "tech"
topics: ["20191213","python"]
published: true
---
今回はtipsですが、pillowでの描画を取り上げます。
中の塗りつぶしのない四角形を書きたかったのですが、ネットでサンプルがなかったので実際にやってみたことを載せておきます。

四角形はrectangle関数で書けるのですが、引数のfillに何も入れないと塗りつぶしがなくなるようです。

```python
from PIL import Image, ImageDraw

img = Image.new('RGB', (300, 300), 'gray')
d = ImageDraw.Draw(img)
d.rectangle([(50, 50), (150, 150)], outline='green',  width=3)
img.show()
```

<figure class="figure-image figure-image-fotolife" title="rectangle">[f:id:naonaorange:20191213064146j:plain]<figcaption>rectangle</figcaption></figure>
![](/images/20191213_python_pillow/1.jpeg)

下記のプログラムをベースに変更しただけです。
https://hibiki-press.tech/learn_prog/python/pillow-imagedraw/2531

