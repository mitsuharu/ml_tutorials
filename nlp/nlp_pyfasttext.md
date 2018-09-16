以下の理由から，fastTextをPythonで使用したい

- fastTextは C++ で作成されいる
- 機械学習は主にPythonで計算される

## ダメだった候補ライブラリ

[fasttext 0.8.3](https://pypi.python.org/pypi/fasttext "fasttext 0.8.3")

- おそらく最も有名だが，更新が止まっている
- 本家fastTextで学習したモデルの読み込みもできない

[gensim: models.wrappers.fasttext](https://radimrehurek.com/gensim/models/wrappers/fasttext.html "gensim: models.wrappers.fasttext – FastText Word Embeddings")

- 本家で学習したモデルを読み込める
- ベクトル値や類似語彙の出力結果が異なる


---

# pyfasttext

- [pyfasttext 0.4.0](https://pypi.python.org/pypi/pyfasttext "pyfasttext 0.4.0")
- 本家で学習したモデルが読み込み可能
- 本家と同じベクトル値や類似語彙の出力結果


# インストール

- `pip` でインストールできるとあるが，私の環境（macOS Sierra 10.12.6）では無理だった

```
pip install -U pyfasttext
```

# インストール手順

## 1. 関連するフレームワークを入れる

```
pip install -U cython
pip install -U cysignals
```

## 2. `gcc` のバージョンを上げる [注意]

- `pip install pyfasttext` でgccのエラーがでなければスキップしてよい
- macOSの`gcc 4.2.1` ではビルドできないのでアップデートする

```
$ brew install gcc
```

- `gcc` のシンボリックを変更する．ただし，他への影響が考えられるので，**専用マシンでやる**のが好ましい
- El Capitan以上だとSIPで変更できないのでリカバリーモードでSIPを解除する [(参考1)](http://berukann.hatenablog.jp/entry/2015/12/30/123020 "EI Capitanでsudo付けているOperation not permittedが出た時の対処法 - いつかエンジニアになりたい")    [(参考2)](http://www.ketsuago.com/entry/2016/07/07/003437 "OS X El Capitanにgccを入れてシンボリックリンクを作成【なれない日記20160706】 - けつあご日記")

```
# リカバリーモードのターミナルにて
$ csrutil disable
$ reboot
```

- 元の `gcc` が消えるので `gcc_old` などで退避しておく

```
$ sudo cp /usr/bin/gcc /usr/bin/gcc_old
$ sudo cp /usr/bin/g++ /usr/bin/g++_old
$ sudo ln -sf /usr/local/Cellar/gcc/7.2.0/bin/gcc-7 /usr/bin/gcc
$ sudo ln -sf /usr/local/Cellar/gcc/7.2.0/bin/g++-7 /usr/bin/g++
```

## 3. pyfasttextを入れる

- `pip install pyfasttext` で下記のようなエラーが起こったら，ソースをcloneして修正して手元でインストールする
- CythonがNumpyの非公式APIを使っているとwarningが出るが無視して良い

```
src/fasttext_access.cpp:34:68: error: 'find' is a private member of 'fasttext::Dictionary'
```

- cloneする

```
git clone --recursive https://github.com/vrasneur/pyfasttext.git
cd pyfasttext
```

- `src/fastText/src/dictionary.h` の36行目あたりの`private` を `public` に書き換える
- 特別他で使うわけでもないのでまあ大丈夫だろう，たぶん

```
// before
class Dictionary {
  private:
  
// after
class Dictionary {
  public:
```

- `pip` か `setup.py` で手元の編集済みソースコードからインストールする

```
$ python setup.py install
$ pip install ./pyfasttext
```


# ソース例

- 簡単なソース例
- どのフレームワーク共通して，`bin`ファイルの読み込みに時間が多少かかる

```
from pyfasttext import FastText
model = FastText('./wiki20170912_mecab_prep_ep10_lr01.bin')

# ベクトル値
print(model["ザク"])

# 語彙の類似度と語彙の加算
zuku = model.get_numpy_vector('ザク')
akai = model.get_numpy_vector('赤い')
nn = model.words_for_vector(zuku + akai, k=10)
print(nn)
```


# 公式対応

- 気づいたら公式 [facebookresearch/fastText
](https://github.com/facebookresearch/fastText) で対応してた



# 参考

- [pyfasttext 0.4.0 : Python Package Index](https://pypi.python.org/pypi/pyfasttext "pyfasttext 0.4.0 : Python Package Index")
- [EI Capitanでsudo付けているOperation not permittedが出た時の対処法 - いつかエンジニアになりたい](http://berukann.hatenablog.jp/entry/2015/12/30/123020 "EI Capitanでsudo付けているOperation not permittedが出た時の対処法 - いつかエンジニアになりたい")
- [OS X El Capitanにgccを入れてシンボリックリンクを作成【なれない日記20160706】 - けつあご日記](http://www.ketsuago.com/entry/2016/07/07/003437 "OS X El Capitanにgccを入れてシンボリックリンクを作成【なれない日記20160706】 - けつあご日記")

