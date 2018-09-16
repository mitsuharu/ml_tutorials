# GCP環境設定

- 原則 /usr/local に入れて，どのアカウントからログインしても使えるようにする
- そのため ```sudo hogehoge``` をよく使います

# Pythonをインストールする

基本的なツールをインストール

```
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install git gcc make openssl libssl-dev libbz2-dev libreadline-dev libsqlite3-dev
```

Pyenvを /usr/local に入れる

```
$ cd /usr/local/
$ sudo git clone https://github.com/pyenv/pyenv.git ./pyenv
$ sudo mkdir -p ./pyenv/versions ./pyenv/shims
$ echo 'export PYENV_ROOT="/usr/local/pyenv"' | sudo tee -a /etc/profile.d/pyenv.sh
$ echo 'export PATH="${PYENV_ROOT}/shims:${PYENV_ROOT}/bin:${PATH}"' | sudo tee -a /etc/profile.d/pyenv.sh
$ echo 'eval "$(pyenv init -)"' | sudo tee -a /etc/profile.d/pyenv.sh
$ source /etc/profile.d/pyenv.sh
```

sudo の設定

```
$ sudo visudo
```

```
# Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin
Defaults    env_keep += "PATH"
Defaults    env_keep += "PYENV_ROOT"
```

pythonのインストール

```
$ sudo pyenv install 3.6.4
$ sudo pyenv rehash
$ sudo pyenv global 3.6.4
```

使用するライブラリ入れておく（tensorflowは後）

```
$ sudo pip install numpy scikit-learn pandas gensim pillow lxml python-gflags
```

# s3にマウント

Goのインストール

```
$ wget https://storage.googleapis.com/golang/go1.9.3.linux-amd64.tar.gz
$ sudo tar -C /usr/local -xzf go1.9.3.linux-amd64.tar.gz
$ echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee -a /etc/profile.d/go.sh
$ source /etc/profile.d/go.sh
$ go version
```

GoのPATH設定

- /opt/goに置いてシステムワイドにしたかったができなかった

```
$ [ ! -e ~/go/ ] && sudo mkdir ~/go
$ echo 'export GOROOT=/usr/local/go' | sudo tee -a /etc/profile.d/go.sh
$ echo 'export GOPATH=$HOME/go' | sudo tee -a /etc/profile.d/go.sh
$ echo 'export PATH="$PATH:$GOPATH:$GOPATH/bin:$GOROOT/bin"' | sudo tee -a /etc/profile.d/go.sh
```

goofys

```
$ sudo go get github.com/kahing/goofys
$ sudo go install github.com/kahing/goofys
$ goofys -h # 確認
```

aws 

```
$ sudo apt-get install awscli
$ aws --version
```
 
```
 $ aws configure
 WS Access Key ID [None]: ********
 AWS Secret Access Key [None]: ********
```

```
$ sudo mkdir /mnt/s3
$ goofys mobsol-machine-learning /mnt/s3
$ sudo umount /mnt/s3
```

自動マウント

- goofys を /opt/bin にコピーして，シェルを追加する

```
$ sudo cp ./go/bin/goofys /opt/go/bin/
$ sudo tee -a /etc/profile.d/mount-s3.sh
[ctrl-c]
```

```
#!/bin/sh

MNT=/mnt/s3
BUCKET=mobsol-machine-learning

mountpoint -q ${MNT}
if [ $? != 0 ]; then
  /opt/go/bin/goofys ${BUCKET} ${MNT}
fi
```
 
# Tensorflow-gpuの設定

古いドライバの削除

```
$ dpkg -l | grep nvidia
$ dpkg -l | grep cuda
$ sudo apt-get --purge remove nvidia-*
$ sudo apt-get --purge remove cuda-*
```

NVIDIAドライバーをインストール

```
$ sudo add-apt-repository ppa:graphics-drivers/ppa
$ sudo apt-get update
$ apt-cache search 'nvidia-[0-9]+$'
$ sudo apt install nvidia-384
$ reboot
```

CUDA 8.0を入れる

- CUDA Toolkit Archive から CUDA Toolkit 8.0 をダウンロードする
- install では cuda-8.0 を指定する
- s3内にファイルあるのでコピーする ```cp /mnt/s3/gpu/hogehoge.deb ~/```

```
$ sudo dpkg -i cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64.deb
$ sudo apt-get update
$ sudo apt-get install cuda-8-0
```

cuDNN v6.0を入れる

- cuDNN Download から Download cuDNN v6.0 (April 27, 2017), for CUDA 8.0 をダウンロードする
- s3内にファイルあるのでコピーする ```cp /mnt/s3/gpu/hogehoge.deb ~/```

```
$ tar xfvz cudnn-8.0-linux-x64-v6.0.tgz
$ sudo cp cuda/include/cudnn.h /usr/local/cuda-8.0/include/
$ sudo cp cuda/lib64/libcudnn* /usr/local/cuda-8.0/lib64/
$ sudo chmod a+r /usr/local/cuda-8.0/lib64/libcudnn*
```

パスの設定を行う

```
echo 'export PATH="/usr/local/cuda/bin:$PATH"' | sudo tee -a /etc/profile.d/gpu.sh
echo 'export LD_LIBRARY_PATH="/usr/local/cuda/lib64:$LD_LIBRARY_PATH"' | sudo tee -a /etc/profile.d/gpu.sh
source /etc/profile.d/gpu.sh
```

TensorFlow GPUを入れる

```
$ sudo apt-get install libcupti-dev
$ sudo pip install tensorflow-gpu # 動かない？
```

tensorflow 1.4.1（2018/01/25時点で最新）がPython3.6に完全対応してないため，1.3.0 をインストールする

```
$ sudo pip install tensorflow-gpu==1.3.0
```

確認

```
$ python -c "import tensorflow"
```


# mecab

mecabのインストールする

```
$ sudo apt-get install mecab libmecab-dev mecab-ipadic mecab-ipadic-utf8
```

辞書 mecab-ipadic-NEologd を追加する

```
$ git clone --depth 1 https://github.com/neologd/mecab-ipadic-neologd.git
$ cd mecab-ipadic-neologd
$ ./bin/install-mecab-ipadic-neologd -n -a
```

辞書のパスを変更する

```
$ sudo nano /etc/mecabrc
```
```
dicdir = /usr/lib/mecab/dic/mecab-ipadic-neologd
```
 
python
 
```
$ sudo pip install mecab-python3
```

# fastText

- [facebookresearch/fastText: Library for fast text representation and classification.](https://github.com/facebookresearch/fastText)

コマンド版のインストール

```
$ wget https://github.com/facebookresearch/fastText/archive/v0.1.0.zip
$ unzip v0.1.0.zip
$ cd fastText-0.1.0
$ make
```

Python 

- gitを見る限り，2017/11 に公式対応したもよう

```
$ git clone https://github.com/facebookresearch/fastText.git
$ cd fastText
$ sudo pip install .
```




---

# 参考

- [Amazon S3をUbuntuにマウントする - みらいテックラボ](http://mirai-tec.hatenablog.com/entry/2017/07/09/170137)
- [Python3.6 で TensorFlow （2017年11月25日時点） - 達人ドヤリストへの道](http://tatsudoya.blog.fc2.com/blog-entry-171.html)
- [Creating a specific 3.6 binary for Linux · Issue #14182 · tensorflow/tensorflow](https://github.com/tensorflow/tensorflow/issues/14182#issuecomment-346132338)






