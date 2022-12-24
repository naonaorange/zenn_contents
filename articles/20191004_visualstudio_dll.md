---
title: "Visual Studioでdllファイルを作成する"
emoji: "😀"
type: "tech"
topics: ["20191004","visualstudio"]
published: true
---
Visual Studioで作成したC言語のプログラムをPythonなどの他言語で使用したい場合は,
一旦、dllにして呼び出す方法があります。

ここではdll作成する時の方法を記載します。

下記の様にhファイルを記載します。
ポイントはDLL_APIというdefineを切り、関数のプロトタイプ宣言でもDLL_APIを記載することです。
```c
#ifndef HOGE_H
#define HOGE_H

#ifdef Test_EXPORTS
# define DLL_API  __declspec(dllexport)
#else
# define DLL_API  __declspec(dllexport)
#endif

DLL_API int hoge_func(char data[], int size);

#endif
```

cファイルも記載します。
こちらでも関数のプロトタイプ宣言でもDLL_APIを記載します。
```c
#include "hoge.h"

DLL_API int hoge_func(char data[], int size)
{
  return 0;
}
```

ファイルの設定は上記でよいです。
あとはVisual Studio プロジェクトファイルのプロパティでexeからdllに変更すればOKです。
