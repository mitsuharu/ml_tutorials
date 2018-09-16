# fastText

- w2v作者がGoogleからFacebookへ異動したのちに開発
- [fastText](https://fasttext.cc/ "fastText")
- [GitHub (facebookresearch/fastText)](https://github.com/facebookresearch/fastText "facebookresearch/fastText: Library for fast text representation and classification.")

## Word2Vecと比べて
- 分類精度がよい 
- 学習時間が早い
- 学習データにないvocab（語彙）でも推論される

## word2vecとの互換性

- fastTextの学習済みモデルは ```gemsim.word2vec``` で使用できるが，機能がw2vになるので，意味はあまりない

```
model = gensim.models.KeyedVectors.load_word2vec_format('model.vec', binary=False)
```

# インストール（コマンドライン）

- ダウンロードしてビルド

```
$ git clone https://github.com/facebookresearch/fastText.git
$ cd fastText
$ make
```

- 前述のディレクトリで実行する（パス設定までされない）．

```
$ cd fastText
$ ./fasttext
```

- パス設定されないので，必要ならパスを設定する

```
# fasttext
export PATH="~/fastText:$PATH"
```

# 学習精度に関して

- 日本語は元々あまり精度は高くない（自然言語処理）

## 精度をあげるために学習時に工夫する

- 句読点や括弧などを削除（空白）する
- epoch数を変更する (using the option `-epoch`, standard range `[5 - 50]`) 
- 学習レートを変更する (using the option `-lr`, standard range `[0.1 - 1.0]`)
- ngramを設定する (using the option `-wordNgrams`, standard range `[1 - 5]`)

##　その他

- [Full documentation](https://github.com/facebookresearch/fastText#full-documentation)
- Wikipedia日本語の全データで5時間ぐらい
- `-lr 0.5` にしたら，out of memory で落ちた@MacBook Air(RAM8GB)

```
$ ./fasttext skipgram -input input_namet -output output_name -epoch 25 -lr 0.1  -thread 4
```

---

# 1. supervised

- 教師つき（ラベル付き）で学習・分類する場合

## 学習

- ```__label__ラベル名```を文章の頭に付ける
- ラベル用接頭語 ```__label__``` はコマンドオプションで変更可能
- 形態素解析で空白区切りの文章



```
__label__sauce __label__cheese how much does potato starch affect a cheese sauce recipe?
__label__food-safety __label__acidity dangerous pathogens capable of growing in acidic environments
__label__cast-iron __label__stove how do i cover up the white spots on my cast iron stove?
```

- コマンド例
```
$ ./fasttext supervised -input input_name -output output_name -lr 0.5 -epoch 25
```

## 予測
- コマンド例
```
$ fasttext predict output_name.bin -
egg   
__label__eggs
```

---

# 2. unsupervised

- ラベル無しの場合
- 文字や文字列をベクトル化したNNなどに利用する
- 近傍する語彙を求める



## 学習

- 形態素解析で空白区切りの文章

```
アンパサンド & と は と を 意味 する 記号 で ある ラテン語 の の 合字 で Trebuchet MS フォント で は と 表示 さ れ et の 合字 で ある こと が 容易 に わかる ampersa すなわち and per se and その 意味 は and the symbol which by itself is and で ある
その 使用 は 1世紀 に 遡る こと が でき 1 5世紀 中葉 2 3 から 現代 4 6 に 至る まで の 変遷 が わかる
Z に 続く ラテン文字 アルファベット の 27 字 目 と さ れ た 時期 も ある
```

- コマンド例

```
$ fasttext skipgram -input input_namet -output output_name -epoch 10 -lr 0.1  -thread 4
```

## 推論

### 単語のベクトル化

```
$ echo "asparagus pidgey yellow" | ./fasttext print-word-vectors model.bin
asparagus -0.41398 0.12321 ...
pidgey 0.63133 -0.52277 ...
yellow 0.021811 0.74532 ...
```

### 文章のベクトル化

```
$ echo "asparagus pidgey yellow" | ./fasttext print-sentence-vectors model.bin
asparagus pidgey yellow 0.022513 0.01984 0.0093021 ...
```

### 近傍（類似度）

- コマンド後，読み込みに時間が多少かかる
- `Query word?`の後，単語を入力する

```
$ ./fasttext nn wiki_mecab_dim100.bin
Pre-computing word vectors... done.
Query word? ザク  
Vガンダム 0.887644
ザクI 0.874333
ザクII 0.873271
V2ガンダム 0.871762
Ξガンダム 0.871233
ガンダム 0.870597
ΖΖガンダム 0.870185
Zガンダム 0.868579
ガンキャノン 0.868262
νガンダム 0.866771
```

---

# PythonでfastTextを使う

- （おそらく）本家と下記はバージョンが異なる精度が異なる
- 本家で作成したモデルは，それぞれのPython版では使えない（読み込まない，精度が変わる）
- 本家のコマンドライン版でスクリプトを書くのが良いかと
- [fastText: Cannot load ../fasttext/wiki.es/wiki.es.bin due to C++ extension failed to allocate the memory · Issue #115 · salestock/fastText.py](https://github.com/salestock/fastText.py/issues/115 "fastText: Cannot load ../fasttext/wiki.es/wiki.es.bin due to C++ extension failed to allocate the memory · Issue #115 · salestock/fastText.py")

---

使えるフレームワークを pyfasttext を見つけたので，Pythonで使用する場合は，[自然言語処理 入門５：Python で fastText を使う](nl_pyfasttext) を参考してください．

---

## A Python interface for Facebook fastText library

- ```pip``` でインストールされる```fasttext```は古いバージョンらしくコマンドライン版で作成した学習済みモデルが読み込めない（2017/9）[(参照)](https://boomin.yokohama/archives/619 "fasttextとMecabとNeologd辞書でテキストマイニングを行うための環境構築手順 | | ITに頼って生きていく")

```
$ pip install cython
$ pip install fasttext
```

## gensim で fastText のラッパーを使用する

- ```gensim``` に含まれるラッパー関数を使用する [(models.wrappers.fasttext)](https://radimrehurek.com/gensim/models/wrappers/fasttext.html "gensim: models.wrappers.fasttext – FastText Word Embeddings")
- ```gensim``` は ```word2vec``` のときにインストール
- 学習はコマンドラインで行う前提です
- コマンドラインのfastTextと結果が異なる

### 読み込み

- ```model.bin``` と ```model.vec``` の二つを読み込む
- 拡張子は不要

```
from gensim.models.wrappers.fasttext import FastText
model_name = './model'
model = FastText.load_fasttext_format(model_name)
print(model.most_similar(positive=["ザク", "赤い"]))
```

### 推論

- ベクトル化

```
>>> model["今日はiPhoneの発売日です"]
array([ 0.42044701,  ....
```

- 類似する語彙

```
>>> model.most_similar(positive=["今日はiPhoneの発売日です"])
[('iPhone', 0.9461849331855774), ('判物', 0.8663767576217651), ('IPhone', 0.8083559274673462), ('iP', 0.7976995706558228), ('iPad', 0.7913923263549805), ('iOS', 0.7855527400970459), ('Phone', 0.77374267578125), ('iPod', 0.762588381767273), ('iOS版', 0.7456312775611877), ('パンチライン', 0.7384482622146606)]
>>> model.most_similar(positive=["ザク"])
[('ザクサ', 0.6570719480514526), ('Publisher', 0.6212935447692871), ('eBooks', 0.6192256212234497), ('Publish', 0.6120572090148926), ('オリエンタルコンサルタンツ', 0.6094743609428406), ('アデロバシレウス', 0.607463002204895), ('岩村駅', 0.6032367944717407), ('Publishing', 0.6020932197570801), ('AbeBooks', 0.6014156341552734), ('33日目', 0.5945165157318115)]
```



#参考

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
- [テキスト分類器fastTextを用いた文章の感情極性判定 - Qiita](http://qiita.com/takumi_TKHS/items/d5131e08f0b4e36eed13 "テキスト分類器fastTextを用いた文章の感情極性判定 - Qiita")
- [機械学習でニュースからカテゴリを推定するサイトを作った話 - Qiita](http://qiita.com/thr3a/items/7c3d660960d6d3dc4006 "機械学習でニュースからカテゴリを推定するサイトを作った話 - Qiita")

