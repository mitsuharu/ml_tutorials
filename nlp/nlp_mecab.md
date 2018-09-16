# 形態素解析

- 文章を名詞や動詞などの品詞に分類する
- 日本語は英文のようなスペース混じりではないので，必須な事前処理

# 1. MeCab

- たぶん国内で最も有名な形態素解析
- [MeCab: Yet Another Part-of-Speech and Morphological Analyzer](http://taku910.github.io/mecab/#download "MeCab: Yet Another Part-of-Speech and Morphological Analyzer")


## 本体をインストールする

```
$ cd mecab-0.996
$ ./configure --enable-utf8-only --prefix=/opt/local/
$ make
$ sudo make install
$ sudo chown -R $(whoami):admin /opt/local/
```

## 辞書をインストールする

```
$ cd mecab-ipadic-2.7.0-20070801
$ ./configure --with-charset=utf8 --prefix=/opt/local/
$ make
$ sudo make install
$ sudo chown -R $(whoami):admin /opt/local/
```

## 追加辞書をインストールする

- [mecab-ipadic-neologd](https://github.com/neologd/mecab-ipadic-neologd/blob/master/README.ja.md "mecab-ipadic-neologd/README.ja.md at master · neologd/mecab-ipadic-neologd")
- 問題なければ基本的にこの辞書を使用する
- [xz](http://macpkg.sourceforge.net/ "Mac OS X Packages")が無ければ，インストールする

```
$ git clone --depth 1 https://github.com/neologd/mecab-ipadic-neologd.git
$ cd mecab-ipadic-neologd
$ ./bin/install-mecab-ipadic-neologd -n
```

## 実行例

```
$ mecab
お家に帰りたい
お家	名詞,一般,*,*,*,*,お家,オイエ,オイエ
に	助詞,格助詞,一般,*,*,*,に,ニ,ニ
帰り	動詞,自立,*,*,五段・ラ行,連用形,帰る,カエリ,カエリ
たい	助動詞,*,*,*,特殊・タイ,基本形,たい,タイ,タイ
EOS
```

```
$ mecab -O wakati
お家に帰りたい
お家 に 帰り たい 
```


```
$ mecab -O wakati
やっと見つけたよ！私の戦車道！
やっと 見つけ た よ ！ 私 の 戦車 道 ！ 

$ mecab -d /opt/local/lib/mecab/dic/mecab-ipadic-neologd -O wakati
やっと見つけたよ！私の戦車道！
やっと 見つけ た よ ！ 私 の 戦車道 ！ 
```

## PythonでMeCabを動かす

- まず上記のコマンドライン版を入れる

```
$ pip install mecab-python3
```

- 例

```
# -*- coding: utf-8 -*-
import MeCab
m = MeCab.Tagger ("-Owakati")
print(m.parse ("今日もしないとね"))
```

```
# -*- coding: utf-8 -*-
import MeCab
m = MeCab.Tagger(' -d /opt/local/lib/mecab/dic/mecab-ipadic-neologd')

text = '''
『アイドルマスター シンデレラガールズ』（THE IDOLM@STER CINDERELLA GIRLS）は、バンダイナムコエンターテインメント（旧バンダイナムコゲームス）とCygamesが開発・運営する『THE IDOLM@STER』の世界観をモチーフとする携帯端末専用のソーシャルゲーム。
'''
print(m.parse(text))
```

```
$ mecab -d /opt/local/lib/mecab/dic/mecab-ipadic-neologd -O wakati -o output.txt
$ mecab -d /opt/local/lib/mecab/dic/mecab-ipadic-neologd -O wakati input.txt > output.txt
$ mecab -d /opt/local/lib/mecab/dic/mecab-ipadic-neologd -O wakati wiki20170912.xml > wiki20170912_mecab_wakati.txt
```

### リンク

- [形態素解析エンジンMeCabをPython3でも使えるようにする（Macの場合） - StatsBeginner: 初学者の統計学習ノート](http://www.statsbeginner.net/entry/2016/02/05/020027 "形態素解析エンジンMeCabをPython3でも使えるようにする（Macの場合） - StatsBeginner: 初学者の統計学習ノート")
- [Macに形態素解析エンジンMeCabをインストール - Qiita](http://qiita.com/ichiroex/items/c330413d5bc286df4801 "Macに形態素解析エンジンMeCabをインストール - Qiita")
- [新し目の辞書を使ってMeCabをPythonから利用する - Qiita](http://qiita.com/yuichy/items/5c8178e5cc3711386b77 "新し目の辞書を使ってMeCabをPythonから利用する - Qiita")
- [Mac OS X Packages](http://macpkg.sourceforge.net/ "Mac OS X Packages")
- [Pythonからmecab-ipadic-neologdを使う - Qiita](http://qiita.com/satzz/items/fec3292a9b552a693728 "Pythonからmecab-ipadic-neologdを使う - Qiita")

--- 

## 2. JUMAN++

- 形態素解析でMecabより精度が良いらしい
- ただし，Mecabより遅い

## Homebrewに対応したもよう

```
$ brew install jumanpp
```

## （無ければ入れる）boost

```
$ brew update
$ brew install boost
```

## インストール


```
$ wget http://lotus.kuee.kyoto-u.ac.jp/nl-resource/jumanpp/jumanpp-1.02.tar.xz
$ tar xJvf jumanpp-1.02.tar.xz
$ cd jumanpp-1.02
$ ./configure --prefix=/opt/local/
$ make
$ sudo make install
```

## 実行

```
$ jumanpp 
今日は雨です
今日 こんにち 今日 名詞 6 時相名詞 10 * 0 * 0 "代表表記:今日/こんにち カテゴリ:時間"
@ 今日 きょう 今日 名詞 6 時相名詞 10 * 0 * 0 "代表表記:今日/きょう カテゴリ:時間"
は は は 助詞 9 副助詞 2 * 0 * 0 NIL
雨 う 雨 名詞 6 普通名詞 1 * 0 * 0 "代表表記:雨/う 漢字読み:音 カテゴリ:抽象物"
@ 雨 あめ 雨 名詞 6 普通名詞 1 * 0 * 0 "代表表記:雨/あめ 漢字読み:訓 カテゴリ:抽象物"
です です だ 判定詞 4 * 0 判定詞 25 デス列基本形 27 NIL
EOS
```

## PythonからJUMAN++を使う

-  ```pyknp-0.3``` を入れる

```
$ wget http://nlp.ist.i.kyoto-u.ac.jp/nl-resource/knp/pyknp-0.3.tar.gz
$ tar xvf pyknp-0.3.tar.gz
$ cd pyknp-0.3/
$ sudo python3 setup.py install
```

- [要検証] なぜか ```pyknp-0.3``` 内で ```$ python``` のインタープリターでしか動かない
-  すごく遅い

```
#coding:utf-8
from pyknp import Jumanpp
 
in_file_name = "../wikiextractor-master/wiki20170912.xml"
out_file_name = "../wikiextractor-master/wiki20170912_jumanpp.txt"

in_file = open(in_file_name)
out_file = open(out_file_name, 'w')

juman = Jumanpp()
 
for line in in_file:
    text = ''
    result = juman.analysis(line)

    for m in result.mrph_list():
        text += str(m.midasi) + " "
    if text is not '':
        out_file.write(text + "\n")
```



# 参考

- [形態素解析エンジンMeCabをPython3でも使えるようにする（Macの場合） - StatsBeginner: 初学者の統計学習ノート](http://www.statsbeginner.net/entry/2016/02/05/020027 "形態素解析エンジンMeCabをPython3でも使えるようにする（Macの場合） - StatsBeginner: 初学者の統計学習ノート")
- [Macに形態素解析エンジンMeCabをインストール - Qiita](http://qiita.com/ichiroex/items/c330413d5bc286df4801 "Macに形態素解析エンジンMeCabをインストール - Qiita")
- [新し目の辞書を使ってMeCabをPythonから利用する - Qiita](http://qiita.com/yuichy/items/5c8178e5cc3711386b77 "新し目の辞書を使ってMeCabをPythonから利用する - Qiita")
- [Mac OS X Packages](http://macpkg.sourceforge.net/ "Mac OS X Packages")
- [Pythonからmecab-ipadic-neologdを使う - Qiita](http://qiita.com/satzz/items/fec3292a9b552a693728 "Pythonからmecab-ipadic-neologdを使う - Qiita")
- [http://lotus.kuee.kyoto-u.ac.jp/nl-resource/jumanpp](https://foolean.net/p/499 "http://lotus.kuee.kyoto-u.ac.jp/nl-resource/jumanpp")
- [【C++】Mac に Boost を インストールした - ちょまど帳](https://chomado.com/programming/c-plus-plus/cpp-boost-install-on-mac/ "【C++】Mac に Boost を インストールした - ちょまど帳")
- [MeCabよりも高精度なJUMAN++をUbuntuにインストールしたよ | Foolean – 備忘録風雑記ブログ](https://foolean.net/p/499 "MeCabよりも高精度なJUMAN++をUbuntuにインストールしたよ | Foolean – 備忘録風雑記ブログ")
- [PythonとJuman++で分かち書きをするよ | Foolean – 備忘録風雑記ブログ](https://foolean.net/p/512 "PythonとJuman++で分かち書きをするよ | Foolean – 備忘録風雑記ブログ")


