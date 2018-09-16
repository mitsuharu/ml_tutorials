# 自然言語のデータ化

- 最も簡単なのは，頻度由来などで ```(1,0,0, ...)``` と定義する
- n単語あれば，n次元のベクトルになり，計算負荷は大きい
- 頻度由来なので単語感の距離は意味を持たない


# word2vec

- 意味を考慮した数百次元のベクトル（デフォルトは100次元．目安は100~300次元）に変換する
  - 例えば, ```w(king) - w(man) +  w(women) = w(queen)```
- NNの入力などに応用できる
- 文章を対象にした ```doc2vec``` などもある
- 学習データにない語彙はベクトル化できない

## インストール

```
pip install --upgrade gensim
```

## 学習データ

- 文字の意味を考慮するため，学習データが必要
- 日本語の場合は，形態素解析などで品詞でスペース区切りしておく
- w2vでは，学習データにないvocab（語彙）の推論はできない

```
# 公式のサンプル
curl -O  http://mattmahoney.net/dc/text8.zip
```

## 学習

- 学習データからモデルを作成する
- 一部で学習済みモデルは公開されている

```
# -*- coding: utf-8 -*-
from gensim.models import word2vec
import logging

model_name = "sample.model"

logging.basicConfig(format='%(asctime)s : %(levelname)s', level=logging.INFO)

sentences = word2vec.Text8Corpus('text8')
model = word2vec.Word2Vec(sentences, size=200, min_count=20, window=15)

model.save(model_name)
```

## ベクトル化

- 語彙のベクトルは ```model.wv['hogehoge']``` で取得できる
- 類似度は ```model.most_similar(positive = ['hogehoge'], negative = ['piyopiyo'], topn = 10)``` で求められる 

```
# -*- coding: utf-8 -*-
import gensim
from gensim.models import word2vec
import logging
import sys
import os

model_name = "sample.model"
model_name = "latest-ja-word2vec-gensim-model/word2vec.gensim.model"
model = word2vec.Word2Vec.load(model_name)

# model = gensim.models.KeyedVectors.load_word2vec_format('./fastText/wiki.ja.vec', binary=False)

def neighbor_word(posi, nega=[], n=10):
    count = 1
    result = model.most_similar(positive = posi, negative = nega, topn = n)
    for r in result:
        print(str(count)+" "+str(r[0])+" "+str(r[1]))
        count += 1

def calc(equation):
    if "+" in equation or "-" in equation:
        posi,nega = [],[]
        positives = equation.split("+")

        for positive in positives:
            negatives = positive.split("-")
            if negatives is not None or negatives.count > 0:
                posi.append(negatives[0])
                nega = nega + negatives[1:]
        
        print("posi:{}, nega:{}".format(posi, nega))
        neighbor_word(posi = posi, nega = nega)
    else:
        neighbor_word([equation])

if __name__=="__main__":
    if len(sys.argv) > 1:
        equation = sys.argv[1]
        calc(equation)
    else:
        calc("ザク")
        calc("ザク+赤")
```


## 特徴

- 学習データにないvocab（語彙）はだめ
- 後述の ```fastText``` を使用するのが良いと思う




# 参考

- [gensim: models.word2vec – Deep learning with word2vec](https://radimrehurek.com/gensim/models/word2vec.html "gensim: models.word2vec – Deep learning with word2vec")
- [word2vecで「単語の足し算引き算」をしてみる - 技術メモ](http://swdrsker.hatenablog.com/entry/2017/02/23/193137 "word2vecで「単語の足し算引き算」をしてみる - 技術メモ")
- [Word2Vecとは？ - Deeplearning4j: Open-source, Distributed Deep Learning for the JVM](https://deeplearning4j.org/ja/word2vec "Word2Vecとは？ - Deeplearning4j: Open-source, Distributed Deep Learning for the JVM")
- [さくらVPSのcentos6にfasttextをインストールする - 自然言語処理ブログ](http://hashtake.hatenablog.com/entry/2016/09/20/142932 "さくらVPSのcentos6にfasttextをインストールする - 自然言語処理ブログ")
- [fastTextの学習済みモデルを公開しました - Qiita](http://qiita.com/Hironsan/items/513b9f93752ecee9e670 "fastTextの学習済みモデルを公開しました - Qiita")
- [fastTextでwikipediaを学習する - TadaoYamaokaの日記](http://tadaoyamaoka.hatenablog.com/entry/2017/04/30/225642 "fastTextでwikipediaを学習する - TadaoYamaokaの日記")
- [fastTextの学習済みモデルを公開しました - Qiita](http://qiita.com/Hironsan/items/513b9f93752ecee9e670 "fastTextの学習済みモデルを公開しました - Qiita")
- [fastText/pretrained-vectors.md at master · facebookresearch/fastText](https://github.com/facebookresearch/fastText/blob/master/pretrained-vectors.md "fastText/pretrained-vectors.md at master · facebookresearch/fastText")
- [Word2Vec で見つけられなかった自分らしさに fastText で速攻出会えた話 | GMOインターネット 次世代システム研究室](http://recruit.gmo.jp/engineer/jisedai/blog/word2vec-fasttext-me/ "Word2Vec で見つけられなかった自分らしさに fastText で速攻出会えた話 | GMOインターネット 次世代システム研究室")
- [fastTextで音声入力をネガポジ判定する - Qiita](http://qiita.com/PonDad/items/48d4e425fbf507cb2b8f "fastTextで音声入力をネガポジ判定する - Qiita")
- [テキスト分類器fastTextを用いた文章の感情極性判定 - Qiita](http://qiita.com/takumi_TKHS/items/d5131e08f0b4e36eed13 "テキスト分類器fastTextを用いた文章の感情極性判定 - Qiita")
- [fastText/pretrained-vectors.md at master · facebookresearch/fastText](https://github.com/facebookresearch/fastText/blob/master/pretrained-vectors.md "fastText/pretrained-vectors.md at master · facebookresearch/fastText")
- [fasttextとMecabとNeologd辞書でテキストマイニングを行うための環境構築手順 | | ITに頼って生きていく](https://boomin.yokohama/archives/619 "fasttextとMecabとNeologd辞書でテキストマイニングを行うための環境構築手順 | | ITに頼って生きていく")
- [Windows(Bash on Windows)でfastTextを使う - TadaoYamaokaの日記](http://tadaoyamaoka.hatenablog.com/entry/2017/04/19/000832 "Windows(Bash on Windows)でfastTextを使う - TadaoYamaokaの日記")
- [FacebookのfastTextでFastに単語の分散表現を獲得する - Qiita](http://qiita.com/icoxfog417/items/42a95b279c0b7ad26589 "FacebookのfastTextでFastに単語の分散表現を獲得する - Qiita")
- [fastTextの実装を見てみた](https://www.slideshare.net/shirakiya/fasttext-71760059 "fastTextの実装を見てみた")
- [fastTextのsubword(部分語)の弊害 - studylog/北の雲](http://studylog.hateblo.jp/entry/2016/09/20/103724 "fastTextのsubword(部分語)の弊害 - studylog/北の雲")
- [Facebookが公開した10億語を数分で学習するfastTextで一体何ができるのか - DeepAge](https://deepage.net/bigdata/machine_learning/2016/08/28/fast_text_facebook.html "Facebookが公開した10億語を数分で学習するfastTextで一体何ができるのか - DeepAge")
- [Facebookが公開した10億語を数分で学習するfastTextで一体何ができるのか - DeepAge](https://deepage.net/bigdata/machine_learning/2016/08/28/fast_text_facebook.html "Facebookが公開した10億語を数分で学習するfastTextで一体何ができるのか - DeepAge")
- [Word2Vec：発明した本人も驚く単語ベクトルの驚異的な力 - DeepAge](https://deepage.net/bigdata/machine_learning/2016/09/02/word2vec_power_of_word_vector.html "Word2Vec：発明した本人も驚く単語ベクトルの驚異的な力 - DeepAge")
- [word2vecで「単語の足し算引き算」をしてみる - 技術メモ](http://swdrsker.hatenablog.com/entry/2017/02/23/193137 "word2vecで「単語の足し算引き算」をしてみる - 技術メモ")
- [word2vecの学習済み日本語モデルを公開します | カメリオ開発者ブログ](http://aial.shiroyagi.co.jp/2017/02/japanese-word2vec-model-builder/ "word2vecの学習済み日本語モデルを公開します | カメリオ開発者ブログ")
- [自然言語処理 Word2vec](https://www.slideshare.net/naotomoriyama/word2vec-65668036 "自然言語処理 Word2vec")
- [機械学習初心者向け、Word2VecとDoc2Vecでディープラーニングやってみた - paiza開発日誌](http://paiza.hatenablog.com/entry/2017/03/16/%E6%A9%9F%E6%A2%B0%E5%AD%A6%E7%BF%92%E5%88%9D%E5%BF%83%E8%80%85%E5%90%91%E3%81%91%E3%80%81Word2Vec%E3%81%A8Doc2Vec%E3%81%A7%E3%83%87%E3%82%A3%E3%83%BC%E3%83%97%E3%83%A9%E3%83%BC%E3%83%8B%E3%83%B3%E3%82%B0 "機械学習初心者向け、Word2VecとDoc2Vecでディープラーニングやってみた - paiza開発日誌")
- [Word2Vec のニューラルネットワーク学習過程を理解する · けんごのお屋敷](http://tkengo.github.io/blog/2016/05/09/understand-how-to-learn-word2vec/ "Word2Vec のニューラルネットワーク学習過程を理解する · けんごのお屋敷")
- [pixiv小説で機械学習したらどうなるのっと【学習済みモデルデータ配布あり】 - pixiv inside](http://inside.pixiv.net/entry/2016/09/13/161454 "pixiv小説で機械学習したらどうなるのっと【学習済みモデルデータ配布あり】 - pixiv inside")
- [word2vec, fasttextの差と実践的な使い方 - にほんごのれんしゅう](http://catindog.hatenablog.com/entry/2017/03/31/221644 "word2vec, fasttextの差と実践的な使い方 - にほんごのれんしゅう")


