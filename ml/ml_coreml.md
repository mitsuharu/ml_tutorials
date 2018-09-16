# CoreML

- iOS11からの機械学習フレームワーク
- Kerasのh5ファイルを変換して読込できる

# 1. mlmodelファイルを作成する

- Kerasで学習したh5ファイルを変換することができる
- 変換ツールは公式に配布されている
  - [coremltools 0.5.1 : Python Package Index](https://pypi.python.org/pypi/coremltools "coremltools 0.5.1 : Python Package Index")
  - ただし，Python2系のみ対応（2017/8）


## 1-1. Python2系の仮想環境を作成する

- ```pyenv```で2系をインストールする

```
$ pyenv install 2.7.13
$ pyenv rehash
```

- 2系のPythonを設定する

```
$ pyenv versions
  system
  2.7.13
* 3.6.1 (set by /Users/mitsuharu/.pyenv/version)
$ pyenv global 2.7.13
```

- 仮想空間を作成する

```
$ mkdir ~/python2.7.13
$ pip install virtualenv
$ virtualenv --system-site-packages ~/python2.7.13/
```

- 元の3系に戻す
 
```
$ pyenv global 3.6.1
```

- coremltoolsをインストールする
- TensorFlow/Kerasをすでに3系で構築していても，2系の仮想環境でも設定する
- 今後変換するときは，python2系の仮想環境で行う



```
$ source python2.7.13/bin/activate
$ pip install -U coremltools
$ pip install --upgrade tensorflow
$ pip install keras
$ pip install h5py
$ deactivate
```

## 1-2. mlmodelファイルに変換する

- クラス名の記したラベルファイルを用意する
  - 例えば，```labels.txt``` とする

```
クラス名0
クラス名1
クラス名2
```

- ソース例
  - ```is_bgr``` : カラーコードの順番
  - ```image_scale``` : Kerasが1/255してるのでスケーリング



```
import coremltools

path = './hogehoge.h5'
coreml_model = coremltools.converters.keras.convert(path,
        input_names = 'image',
        is_bgr = True,
        image_scale = 0.00392156863,
        image_input_names = 'image',
        class_labels = 'labels.txt')
coreml_model.save('hogehoge.mlmodel')
```


# 2. iOSへ実装

- CoreMLを使った実装例です
- 例は組込ですが，外部からmlmodelファイルは追加可能になるもよう

## 2-0. mlmodelファイルのプロジェクト追加

- ```CoreML```, ```Vision```のフレームワークを追加

```
import Vision
```


## 2-1. mlmodelファイルのプロジェクト追加

- ```Hogehoge.mldodel```というファイルを追加すると，クラス```Hogehoge```が自動生成される

```
self.hogehoge = Hogehoge()
```

## 2-2. 予測メソッドの作成

- ```VNCoreMLRequest```を生成して，リクエストを行う
- 完了はhandlerを設定し，それで受け取る

```
func predicate(cgImage: CGImage) {
  let image = CIImage(cgImage: cgImage)
  let handler = VNImageRequestHandler(ciImage: image)
  do {
    let model = try VNCoreMLModel(for: self.hogehoge.model)
    let req = VNCoreMLRequest(model: model,
                completionHandler: self.handleClassification)
    try handler.perform([req])
  } catch {
    print(error)
  }
}
```

## 2-3. 予測完了

- 完了のhandler
- 推定確率の順にソートされる

```
private func handleClassification(request: VNRequest, error: Error?) {
  guard let observations = request.results as? [VNClassificationObservation] else { fatalError() }
  guard let best = observations.first else { fatalError() }
  
  DispatchQueue.main.async {
    print("best identifier: " + best.identifier + ", prob: " + String(best.confidence*100))
    
    for ob in observations {
      print("identifier: " + ob.identifier + ", prob: " + String(ob.confidence*100))
    }
  }
}
```
    
# まとめ

- 計算時間は簡単な9クラスの画像分類で
  - 1推定あたりiPhone 6 Plus, iOS11 betaで，``` 1.08 sec ```程度
  - ちょっと時間かかってる
- マルチプラットホームで場合は，実装コストからサーバーで計算？
  - ネットワークの状況も関連するけど




# 参考

- [coremltools 0.5.1 : Python Package Index](https://pypi.python.org/pypi/coremltools "coremltools 0.5.1 : Python Package Index")
- [Keras + iOS11 CoreML + Vision Framework による、ももクロ顔識別アプリの開発 - Qiita](http://qiita.com/kenmaz/items/d416b191f79f60e07752 "Keras + iOS11 CoreML + Vision Framework による、ももクロ顔識別アプリの開発 - Qiita")
- [【iOS 11】【Core ML】pip install coremltools でエラーになった場合の対処法 - Qiita](http://qiita.com/shu223/items/d99e3ad7ec377aa7aeaa "【iOS 11】【Core ML】pip install coremltools でエラーになった場合の対処法 - Qiita")
- [【iOS 11】Core ML＋Visionを用いた物体認識の最小実装 - Qiita](http://qiita.com/shu223/items/e200e379e34629018dca "【iOS 11】Core ML＋Visionを用いた物体認識の最小実装 - Qiita")



