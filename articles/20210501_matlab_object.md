---
title: "MATLAB Scriptでオブジェクト指向プログラミング"
emoji: "😀"
type: "tech"
topics: ["20210501","matlab"]
published: true
---
MATLAB Scriptでオブジェクト指向プログラミング書く時に
よくある機能の搭載有無や書き方を簡単に確認したい時が多くあります。

この記事ではオブジェクト指向の記法を使った簡単なサンプルプログラムを書いておくことで、
忘れた時に参照しやすくしています。

# 基本的なオブジェクト指向プログラミング

まず、MATLAB Scriptは同じ名前の関数オーバーロードをサポートしていません。
また、関数のデフォルト引き数はそれ自体ではサポートしていませんが、narggin等を使用することで同機能を搭載可能です。

下はclass_sample.mの内容です。
```matlab
classdef class_sample 
    properties
        name
    end
    
    methods
%       コンストラクタ
        function obj = class_sample(str)
            obj.name = str;
            disp("Constructor");
            disp("Constructor input value : " + str);
        end
        
%       デストラクタ
        function delete(obj)
            disp("Destractor");
        end

%         デフォルト引数は基本サポートしてない　nargginを使用すると同機能を搭載可能
%         function obj = class_sample(str="default str")
%         end

%         同じ名前の関数のオーバーロードはサポートしていない
%         function obj = class_sample(str)
%         end
        
%       SETアクセサ
        function obj = set.name(obj, str)
            disp("Set accesser");
            if strcmp(obj.name, str)
                obj.name = str;
            end
        end

%       GETアクセサ
        function n = get.name(obj)
            disp("Get accesser");
            n = obj.name;
        end        
        

        function function1(obj)
            disp("This is function 1");
            disp("name property is " + obj.name);
        end
    end

%       静的メゾット
    methods(Static)
        function staticFunction()
            disp("This is static function");
        end
    end
end
```

class_sample.mを利用するmain.mは下記です。

```matlab
c = class_sample("hoge");
c.function1();
c.name = "fuga";
c.function1();
class_sample.staticFunction();
```

# 継承を用いたオブジェクト指向プログラミング
MATLABはクラスの継承もサポートしています。
オーバーライドなど基本的な機能はきちんと対応しているみたいです。

下は親クラスの実装です。

```matlab
classdef root_class_sample  
    properties
        rootName
    end
    
%   抽象プロパティの定義が可能
    properties (Abstract)
        abstractName
    end
    
    methods
        function obj = root_class_sample(str)
            obj.rootName = str;
            disp("Root constructor");
            disp("Root constructor value : " + obj.rootName);
        end
        
        function rootFunction1(obj)
            disp("This is root function 1");
            disp("Root name property is " + obj.rootName);
        end
    end

%   抽象メゾットの定義が可能
    methods (Abstract)
        abstractFunction1(obj)
    end
end
```

次に子クラスの実装です。

```matlab
%クラスの継承が可能
%多重継承も可能
%classdef class_sample < root_class_sample1 & root_class_sample2
classdef class_sample < root_class_sample
    properties
        name
%       抽象プロパティの実装が可能
        abstractName
    end
    
    methods
        function obj = class_sample(str)
%           親クラスのコンストラクタを実行
            obj@root_class_sample("root" + str);
            obj.name = str;
            disp("Constructor");
            disp("Constructor input value : " + str);
        end

        function function1(obj)
            disp("This is function 1");
            disp("name property is " + obj.name);
        end

%       メゾットのオーバーライドが可能
        function rootFunction1(obj)
            disp("This is root function 1 under class sample override");
            rootFunction1@root_class_sample(obj);
        end
        
%       親クラスのメゾットを別名メゾットで実行が可能
%       (子クラスでオーバーライドしている関数のみ)
        function callRootFunction1(obj)
            disp("This is root function 1 for class sample");
            %子クラスでオーバーライドしていない場合は下記エラーになる
            obj.rootFunction1();
        end

%       親クラスのプロパティーにアクセス可能
        function GetRootProperty(obj)
            disp("Root name property is " + obj.rootName); 
        end
        
%       抽象メゾットの実装が可能
        function abstractFunction1(obj)
            disp("This is abstract function 1 for class sample");
        end
    end
end
```

最後にメインプログラムです。

```matlab
c = class_sample("hoge");
c.function1();
c.name = "fuga";
c.function1();
c.rootFunction1();
c.rootName = "hogehoge";
c.rootFunction1();
c.callRootFunction1();
c.GetRootProperty();
c.abstractFunction1();
```
下記の様に実行されます。
```
>> main
Root constructor
Root constructor value : roothoge
Constructor
Constructor input value : hoge
This is function 1
name property is hoge
This is function 1
name property is fuga
This is root function 1 under class sample override
This is root function 1
Root name property is roothoge
This is root function 1 under class sample override
This is root function 1
Root name property is hogehoge
This is root function 1 for class sample
This is root function 1 under class sample override
This is root function 1
Root name property is hogehoge
Root name property is hogehoge
This is abstract function 1 for class sample
```


# 参考

https://jp.mathworks.com/help/matlab/matlab_oop/matlab-vs-other-oo-languages.html

https://jp.mathworks.com/help/matlab/matlab_oop/abstract-classes-and-interfaces.html



