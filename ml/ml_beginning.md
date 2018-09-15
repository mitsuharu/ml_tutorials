# 機械学習とは

- 機械学習（きかいがくしゅう、英: machine learning）とは、人工知能における研究課題の一つで、人間が自然に行っている学習能力と同様の機能をコンピュータで実現しようとする技術・手法のことである。（[機械学習 - Wikipedia](https://ja.wikipedia.org/wiki/%E6%A9%9F%E6%A2%B0%E5%AD%A6%E7%BF%92 "機械学習 - Wikipedia")）
- 既知の情報だけではなく，未知の情報に対しても適切な処理を行う汎用的なシステムを目指す．

実用例

- 画像認識
- 音声認識
- 機械翻訳
- 超解像技術

# 機械学習のアルゴリズム

機械学習一般的に以下のようなアルゴリズムがある．

## 教師あり学習（Supervised Learning）

- ラベル付き（正解）のデータが与えられる
- 学習した結果から，ラベルなしデータを分類する

## 教師なし学習（Unsupervised Learning）

- ラベル付き（正解）のデータはない
- 規則性や相関性を解析して分類する
- データマイニングに似ている

## 半教師あり学習（Semi-Supervised Learning）

- 教師あり学習と教師なし学習の合体
- ラベルありデータで学習した結果を元に，ラベルなしデータで学習する

## 強化学習

- ラベル付き（正解）のデータはないが，報酬が設定される
- 教師あり学習だけでは訓練データが足りないので，教師なし学習を行う
- AlphaGoに使われた


# アプローチ

機械学習の主なアプローチの体系（個別には，遺伝的アルゴリズム，ニューラルネットワーク，ベイジアンネットワーク，..., ggrks）．何を使って解くかは，問題や当人の趣味趣向による．

## 識別モデル

- パターンの境界線を求める
- 解析学や線形代数でゴリゴリ解く
- 流行りのDeep LearingやTensorFlowはこちら

## 生成モデル

- パターンごとの確率分布を求めていく
- 確率統計やベイズ理論でゴリゴリ解く
- プログラミングに関しては，RやStanが有名

## （図解）2状態の分類問題
- 高校（中学？）数学で習った ![image/t01_m_fx0.png](image/t01_m_fx0.png) が識別器になる
- 事後確率が最大になる方に分類される

![ml01.png](image/ml01.png)

## （例題）訓練データありのモデル推定（最小二乗法）

入力を![image/t01_m_x.png](image/t01_m_x.png)，出力を![image/t01_m_y.png](image/t01_m_y.png)として，パラメータ![image/t01_m_theta.png](image/t01_m_theta.png)を用いて，以下のようにモデルを定義する

![image/t01_m_yfx.png](image/t01_m_yfx.png)


以下の訓練データが与えらえたときの問題を考える（パラメータ![image/t01_m_theta.png](image/t01_m_theta.png)を求める）．

![image/t01_m_data.png](image/t01_m_data.png)

訓練データから理論値の誤差![image/t01_m_j.png](image/t01_m_j.png)は，以下のように定義できる．

![image/t01_m_j2.png](image/t01_m_j2.png)

誤差![image/t01_m_j.png](image/t01_m_j.png)を最小にするパラメータ![image/t01_m_theta.png](image/t01_m_theta.png)を求めれば，モデルが求められる．

![image/t01_m_dj.png](image/t01_m_dj.png)

ここで，モデル関数を一次関数と仮定すると，

![image/t01_m_fx.png](image/t01_m_fx.png)

パラメータ![image/t01_m_a.png](image/t01_m_a.png)および![image/t01_m_b.png](image/t01_m_b.png)は以下のように求められる

![image/t01_m_ab.png](image/t01_m_ab.png)

この例題は分類問題ではないが，未知のデータを取得した時，求めた![image/t01_m_fx1.png](image/t01_m_fx1.png)から出力を予測できる（最小二乗法は実験演習などで習う基本的なデータ解析手法の１つ）．

![ml02.png](image/ml02.png)
  
## モデル推定の大まかな手順

1. 誤差関数（損失関数）を定義する
2. 損失関数を最小にするパラメータを微分（偏微分）や近似値計算を使って求める
  

## 一般的な問題でこんな簡単なモデルにはならない

- 複雑すぎて直接パラメータが求められない
- 勾配降下法など反復計算をして近似計算を行う
- 機械学習は計算時間が長いと言われるのはこのため

# 学習の注意点

学習時に以下のことを気をつけないと学習に失敗することがある．

- 訓練データを十分に用意する
- 複数のデータで学習するバッチ処理など
- 最小値問題で確率的ノイズを加える（ライブラリがやってくれる）

## 過学習（over-fitting）

- 訓練データに対して学習されているが、未知データ（テストデータ）に対しては適合できていない、汎化できていない状態（[過剰適合 - Wikipedia](https://ja.wikipedia.org/wiki/%E9%81%8E%E5%89%B0%E9%81%A9%E5%90%88 "過剰適合 - Wikipedia")）

- もはやテーブルルックアップ．通常範囲内のノイズに弱い

- 訓練データの不足や，モデルの不適切（高次元パラメータなど）など


## 局所的な極小点

- 最小値と思っていたが，実は極小値だった



### 参考文献
* [機械学習 - Wikipedia](https://ja.wikipedia.org/wiki/%E6%A9%9F%E6%A2%B0%E5%AD%A6%E7%BF%92 "機械学習 - Wikipedia")
* [Microsoft PowerPoint - H23.11.4_NII土曜懇談会(ueda)新聞記事削除版.pptx - 111104_3rdlecueda.pdf](https://www.nii.ac.jp/userdata/karuizawa/h23/111104_3rdlecueda.pdf "Microsoft PowerPoint - H23.11.4_NII土曜懇談会(ueda)新聞記事削除版.pptx - 111104_3rdlecueda.pdf")
* [過剰適合 - Wikipedia](https://ja.wikipedia.org/wiki/%E9%81%8E%E5%89%B0%E9%81%A9%E5%90%88 "過剰適合 - Wikipedia")
* [ニューラルネットワークの訓練の問題点：過学習と局所的極小点 – 理系トピックス学習会](http://math.course.jp/artifical-intelligence/training_issues_and_overfitting_localminimum/ "ニューラルネットワークの訓練の問題点：過学習と局所的極小点 – 理系トピックス学習会")