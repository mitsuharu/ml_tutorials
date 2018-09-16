# 1. Pythonをインストールする

- Yosemiteからの/usr/local問題に対応するため，別のPythonをインストールする
- Homebrewからpyenvをインストールできるが，私の環境ではHomebrewのインストールが上手く動かない
- ```anaconda``` でも大丈夫です

## 環境管理 [pyenv](https://github.com/pyenv/pyenv) をインストールする

- インストール

```
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

- 環境設定ファイルでパスを設定する
上手くパス設定できない場合は先に export.. だけを先に設定し読み込む

```
export PYENV_ROOT=$HOME/.pyenv
export PATH=$PYENV_ROOT/bin:$PATH
eval "$(pyenv init -)"
```

## Pythonのインストール

- インストール可能なバージョンを取得する

```
pyenv install -l
```

- インストール（比較的時間かかる）

```
pyenv install 3.6.1
pyenv rehash
```

- うまく所有権の設定が動かないので設定する

```
sudo chown -R $(whoami):admin ~/.pyenv
```

- 使用するpythonを設定する
 
```
pyenv global 3.6.1
```

- 確認

```
python --version
```


## 仮想環境管理 pyenv-virtualenv

- インストールする

```
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```

- 環境設定ファイルに追加する

```
eval "$(pyenv virtualenv-init -)"
```

## ライブラリのインストール

- ライブラリをインストールするため

```
sudo easy_install pip
```

- 数値計算

```
pip install numpy
```

- 便利なやつ

```
pip install pillow lxml python-gflags
```

---

# 2. TensorFlowをインストールする

- TensorFlow用の仮想環境を作成する
- [Installing TensorFlow on Mac OS X](https://www.tensorflow.org/install/install_mac)の Installing with virtualenv に従う  

## 仮想環境の作成

- 仮想環境管理 virtualenv をインストールする

```
sudo pip install --upgrade virtualenv
```

- 仮想環境を tensorflow に作る
 
```
mkdir ~/tensorflow
virtualenv --system-site-packages -p python3 ~/tensorflow/
```

- 専用の仮想環境でインストールする

```
source ~/tensorflow/bin/activate 
sudo pip3 install --upgrade tensorflow
```

- 仮想環境から抜ける

```
deactivate
```

- 仮想環境を削除する（アンインストールするとき）

```
rm -r ~/tensorflow 
```

## 確認

- 仮想環境 tensorflow にて行う

```
# helloworld.py 
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```

```
python helloworld.py 
```

``The TensorFlow library wasn't compiled to use SSE4.2``とかのエラーが出たら，マシンとバイナリーが適用されていないので，ソースからビルドするらしい．
- [How to compile tensorflow using SSE4.1, SSE4.2, and AVX. · Issue #8037 · tensorflow/tensorflow](https://github.com/tensorflow/tensorflow/issues/8037#issuecomment-283831398 "How to compile tensorflow using SSE4.1, SSE4.2, and AVX. · Issue #8037 · tensorflow/tensorflow")
- [Installing TensorFlow from Sources| TensorFlow](https://www.tensorflow.org/install/install_sources "Installing TensorFlow from Sources | TensorFlow")

# Keras
- TensorFlow（もしくはTheano）のラッパーライブラリ

```
pip install keras 
```

- KerasのバックエンドをTensorFlowに変更する

```
# ~/.keras/keras.jsonというファイルを確認する
{"backend": "tensorflow"}
```
```~/.keras/keras.json```に記述してあるか確認する．なければ追加する．ファイル自体なければ，ファイルを生成する





# PyCharm

- ジェットブレインのPython向けIDE
- プロジェクトのpython設定（Configure Python Interpreter?）で```~/tensorflow/bin/python3.6```を選ぶ


# 参考URL

- [Installing TensorFlow on Mac OS X](https://www.tensorflow.org/install/install_mac) 
- [pyenvとvirtualenvで環境構築 - Qiita](http://qiita.com/Kodaira_/items/feadfef9add468e3a85b "pyenvとvirtualenvで環境構築 - Qiita")
- [TensorFlow(1.0.0)の開発環境構築方法(Mac) - Qiita](http://qiita.com/uhooi/items/b2f3a121b2e9dac6a256 "TensorFlow(1.0.0)の開発環境構築方法(Mac) - Qiita")
- [Mac OS XにTensorflowをインストールして、Hello worldまでやってみる - ワタナベ書店](http://senyoltw.hatenablog.jp/entry/2016/05/07/231041 "Mac OS XにTensorflowをインストールして、Hello worldまでやってみる - ワタナベ書店")


