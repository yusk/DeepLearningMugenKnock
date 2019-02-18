# Q. 有名モデル実装編

ここではCNNの有名モデルを自分の手で実装していく。フレームワークは自由だが、**とりあえずPyTorch, Tensorflow, Keras, Chainer全部で実装せよ。**
ネットワークを作ったら、学習率やイテレーションを変えて、テストデータセット *../Dataset/test/images* でテストせよ。

## Q. LeNet

論文 >> http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf

これが原初のモデル。MNISTと呼ばれる0から9までの手書き数字の判別で使われたCNNモデル。これを実装せよ。LeNetはMNIST用に入力サイズが32x32となっているが、ここで用意しているデータセットは128x128サイズである。**よって学習時のデータの入力サイズを32x32にリサイズする必要がある。**

構造は以下の様になる。

| Layer | カーネルサイズ | フィルタ数 |  ストライド| パディング |  活性化関数 | 
|:---:|:---:|:---:|:---:|:---:|:---:|
| Input | 32 x 32 x 3(入力サイズ) |
| Convolution | 5 x 5 |  6 | 1 | 0 | - |
| MaxPooling | 2 x 2 | - | 2 | 0 | sigmoid |
| Convolution | 5 x 5 | 16 | 1 | 0 | - |
| MaxPooling | 2 x 2 | - | 2 | 0 | sigmoid |
| MultiLayerPerceptron | 120 | - | - | - | - | - |
| MultiLayerPerceptron |  64 | - | - | - | - | - |
| MultiLayerPerceptron | 2 (クラス) | - | - | - | - | Softmax|



答え
- Pytorch [answers/lenet_pytorch.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/lenet_pytorch.py)
- Tensorflow [answers/lenet_tensorflow_layers.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/lenet_tensorflow_layers.py)
- Keras [answers/lenet_keras.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/lenet_keras.py)
- chainer [answers/lenet_chainer.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/lenet_chainer.py)

## Q. AlexNet

論文 >> https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks

ディープラーニングを流行らせた張本人モデル。ImageNetという画像認識のコンペILSVRC2012で圧倒的一位で優勝したことから現在のディープラーニングブームが起こった。これを実装せよ。
AlexNetでは*Local Response Normalization* という特別な正規化Layerがある。
pytorchでは *torch.nn.modules.normalization.LocalResponseNorm()*、chainerでは*chainer.functions.local_response_normalization()* Tensorflowでは*tf.nn.local_response_normalization()* で実装がある。Kerasにはないので実装しなくてもよい。
LRNは効果が薄いことから最近ではほとんど使われない。こういうのもあったんだなあ程度に覚えておくといいと思う。

ただし学習データの枚数が少ないので学習が進まないので、精度を上げたいときは自分で学習データを増やすか、パラメータ数を変えるなどの工夫が必要なので注意。

| Layer | カーネルサイズ | フィルタ数 | ストライド| パディング | 活性化関数 | 
|:---:|:---:|:---:|:---:|:---:|:---:|
| Input | 227 x 227 x 3 (入力サイズ) |
| Convolution | 11 x 11 | 96 | 4 | 0 | ReLU |
| LocalResponseNormalization | - | - | - | - | - |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 5 x 5 | 256 | 1 | 1 | ReLU |
| LocalResponseNormalization | - | - | - | - | - |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 3 x 3 | 384 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 384 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 256 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| MultiLayerPerceptron | 4096 | - | - | - | - | ReLU + Dropout |
| MultiLayerPerceptron | 4096 | - | - | - | - | ReLU + Dropout |
| MultiLayerPerceptron | 2 (クラス) | - | - | - | - | Softmax|


答え
- Pytorch [answers/alexnet_pytorch.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/alexnet_pytorch.py)
- Tensorflow [answers/alexnet_tensorflow_layers.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/alexnet_tensorflow_layers.py)
- Keras [answers/alexnet_keras.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/alexnet_keras.py)
- chainer [answers/alexnet_chainer.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/alexnet_chainer.py)


## Q. ZFNet

論文 >> https://arxiv.org/abs/1311.2901

ILSVRC2013で一位をとったモデル。AlexNetと構造が似ている。
Alexnetの最初のconvlutionを7x7のカーネルにして、ストライドを2に変更している。そのかわりに２つ目のconvolutionのストライドを2にしている。こうすることで、大きなカーネルサイズによる画像の周波数取得を変えている。論文ではCNNが画像認識を行うまでの解析を主張している。

ここらへんから計算時間がめちゃくちゃ増えるので、GPU使用をおすすめ。もしくは入力サイズを112x112とか小さくすることを推奨。

| Layer | カーネルサイズ | フィルタ数 | ストライド| パディング | 活性化関数 | 
|:---:|:---:|:---:|:---:|:---:|:---:|
| Input | 224 x 224 x 3 (入力サイズ) |
| Convolution | 7 x 7 | 96 | 2 | 0 | ReLU |
| LocalResponseNormalization | - | - | - | - | - |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 5 x 5 | 256 | 2 | 1 | ReLU |
| LocalResponseNormalization | - | - | - | - | - |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 3 x 3 | 384 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 384 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 256 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| MultiLayerPerceptron | 4096 | - | - | - | - | ReLU + Dropout |
| MultiLayerPerceptron | 4096 | - | - | - | - | ReLU + Dropout |
| MultiLayerPerceptron | 2 (クラス) | - | - | - | - | Softmax|

答え
- Pytorch [answers/zfnet_pytorch.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/zfnet_pytorch.py)
- Tensorflow [answers/zfnet_tensorflow_layers.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/zfnet_tensorflow_layers.py)
- Keras [answers/zfnet_keras.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/zfnet_keras.py)
- chainer [answers/zfnet_chainer.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/zfnet_chainer.py)

## Q. Global Average Pooling

ここではGlobal average poolingを実装せよ。これはMLPを使わないでConvolutionaだけのモデル(**FCN: Fully Convolutional Network**)でクラス分類を行うために考案された。通常クラス分類はMLPをクラスの数だけ用意する必要があるが、これではネットワークへの入力サイズが固定化されてしまう。これはMLPの性質による。しかしGAPによりこれは解決される。

GAPはConvolutionによる生成される特徴マップの内の１チャネルの全部の値のaverage値を取る操作を行う。そうすると、チャネルの数だけの値が取れる。これにSoftmax関数を適用することでクラス分類が行われる。

アルゴリズムは、
1. ネットワークのMLPを消す。
2. クラス数の**カーネル数**(カーネルサイズでないので注意カーネルサイズはよく1x1が使われる。)を持ったConvolutionを最後につける。
3. GAPを適用する。
4. Softmaxを適用する。

これでFCNが完成する。人によってはGAP後にMLPを入れる時もあるが、どちらにしても入力画像サイズが自由なモデルが作成できる。GAPはNetwork in networkの論文で提唱された手法である。

今回はZFネットにGAPを適用せよ。

pytorchでは*torch.nn.AdaptiveAvgPooling2d()*(この後view()を使ってreshapeの必要がある。)、tensorflowでは*tf.reduce_mean()*を２回適用して、kerasでは*keras.layers.GlobalAveragePooling2D()*、chainerでは*chainer.functions.average()*、で実装できる。

答え
- Pytorch [answers/gap_pytorch.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/gap_pytorch.py)
- Tensorflow [answers/gap_tensorflow_layers.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/gap_tensorflow_layers.py)
- Keras [answers/gap_keras.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/gap_keras.py)
- chainer [answers/gap_chainer.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/gap_chainer.py)


## Q. Network in network

## Q. VGG16

VGG16とはOxfort大学の研究グループが提案したモデルであり、けっこう色んな手法のベースに使われているモデルである。VGG16では3x3のカーネルを持ったConvoutionを重ねることでモデルが取得する特徴の非線形性を増大させている。16というのはconvolutionとMLPを合わせて16という意味らしい。

| Layer | カーネルサイズ | フィルタ数 | ストライド| パディング | 活性化関数 | 
|:---:|:---:|:---:|:---:|:---:|:---:|
| Input | 224 x 224 x 3 (入力サイズ) |
| Convolution | 3 x 3 | 64 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 64 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 3 x 3 | 128 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 128 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 3 x 3 | 256 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 256 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 256 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| MultiLayerPerceptron | 4096 | - | - | - | - | ReLU + Dropout |
| MultiLayerPerceptron | 4096 | - | - | - | - | ReLU + Dropout |
| MultiLayerPerceptron | 2 (クラス) | - | - | - | - | Softmax|

答え
- Pytorch [answers/vgg16_pytorch.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/vgg16_pytorch.py)
- Tensorflow [answers/vgg16_tensorflow_layers.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/vgg16_tensorflow_layers.py)
- Keras [answers/vgg16_keras.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/vgg16_keras.py)
- chainer [answers/vgg16_chainer.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/vgg16_chainer.py)

## Q. VGG19

VGG19はVGG16にConvolutionが3つ増えたバージョン。こっちよりもVGG16のほうがよく使われている。多分認識精度とパラメータ数が割に合わないんだと思う。とりあえずモデル理解のために実装してみましょう。

| Layer | カーネルサイズ | フィルタ数 | ストライド| パディング | 活性化関数 | 
|:---:|:---:|:---:|:---:|:---:|:---:|
| Input | 224 x 224 x 3 (入力サイズ) |
| Convolution | 3 x 3 | 64 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 64 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 3 x 3 | 128 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 128 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 3 x 3 | 256 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 256 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 256 | 1 | 1 | ReLU |
| **Convolution** | 3 x 3 | 256 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| **Convolution** | 3 x 3 | 512 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| Convolution | 3 x 3 | 512 | 1 | 1 | ReLU |
| **Convolution** | 3 x 3 | 512 | 1 | 1 | ReLU |
| MaxPooling | 3 x 3 | 2 | 0 | - | - |
| MultiLayerPerceptron | 4096 | - | - | - | - | ReLU + Dropout |
| MultiLayerPerceptron | 4096 | - | - | - | - | ReLU + Dropout |
| MultiLayerPerceptron | 2 (クラス) | - | - | - | - | Softmax|

答え
- Pytorch [answers/vgg19_pytorch.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/vgg19_pytorch.py)
- Tensorflow [answers/vgg19_tensorflow_layers.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/vgg19_tensorflow_layers.py)
- Keras [answers/vgg19_keras.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/vgg19_keras.py)
- chainer [answers/vgg19_chainer.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/vgg19_chainer.py)

## Q. モデルの書き方の簡潔化

そろそろネットワークを書くのがだるくなってきたころ。16層ならギリギリ書けるけど、2019年2月現在では200層以上とかが世界を獲っている。こんなの真面目に書いたら発狂してしまうので、フレームワーク毎にVGG16モデルを簡略化して書く方法を紹介していきます。これ以外にも書き方はいろいろあるので知ってる方はIssuesとかで教えてください！

### PyTorch

convolutionのブロック毎にfor分を回せばおｋです。ただしlayerのnameには注意。
initではこんな感じ。

```python
self.conv3 = []
for i in range(3):
    f = 128 if i == 0 else 256
    self.conv3.append(torch.nn.Conv2d(f, 256, kernel_size=3, padding=1, stride=1))
```

callではこんな感じ。

```python
for layer in self.conv3:
    x = F.relu(layer(x))
x = F.max_pool2d(x, 2, stride=2, padding=0)
```

[answers/easy_pytorch.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/easy_pytorch.py)

### Tensorflow

convolutionのブロック毎にfor分を回せばおｋです。ただしlayerのnameには注意。

```python
for i in range(3):
    x = tf.layers.conv2d(inputs=x, filters=256, kernel_size=[3, 3], strides=1, padding='same', activation=tf.nn.relu, name='conv3_{}'.format(i+1))
x = tf.layers.max_pooling2d(inputs=x, pool_size=[2, 2], strides=2)
```

[answers/easy_tensorflow_layers.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/easy_tensorflow_layers.py)

### Keras

convolutionのブロック毎にfor分を回せばおｋです。ただしlayerのnameには注意。

```python
for i in range(3):
    x = Conv2D(256, (3, 3), padding='same', strides=1, activation='relu', name='conv3_{}'.format(i+1))(x)
x = MaxPooling2D((2, 2), strides=2,  padding='same')(x)
```

[answers/easy_keras.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/easy_keras.py)

### Chainer

convolutionのブロック毎にfor分を回せばおｋです。ただしlayerのnameには注意。
initではこんな感じ。

```python
self.conv3 = []
for i in range(3):
    self.conv3.append(L.Convolution2D(None, 256, ksize=3, pad=1, stride=1, nobias=False))
```

callではこんな感じ。

```python
for layer in self.conv3:
    x = F.relu(layer(x))
x = F.max_pooling_2d(x, ksize=2, stride=2)
```

[answers/easy_chaienr.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/easy_chainer.py)

## Q. GoogLeNet

##  Q. Batch Normalization

論文 >> https://arxiv.org/abs/1502.03167

Batch normalizationとは学習をめちゃくちゃ効率化するための手法です。今ではどのディープラーニングでもBNはかかせない存在となっています。

論文中では学習がうまくいかないのは、そもそもconvolutionしまくると値がシフトしてしまうことが原因だといってます。これが共分散シフトです。なのでBNはlayerの出力に対して正規化を行うことでこのシフトをなくすのが目的となっています。

ここではVGG16のconvの後にBNを実装してみましょう。

pytorchでは*torch.nn.BatchNorm2d()*, tensorflowでは*tf.layers.batch_normalization(), tf.layers.BatchNormalization()*, Kerasでは*keras.layers.BatchNormalization()* ,chainerでは*chainer.links.BatchNormalization()* で実装できる。

答え
- Pytorch [answers/bn_pytorch.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/bn_pytorch.py)
- Tensorflow [answers/bn_tensorflow_layers.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/bn_tensorflow_layers.py)
- Keras [answers/bn_keras.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/bn_keras.py)
- chainer [answers/bn_chainer.py](https://github.com/yoyoyo-yo/DeepLearningMugenKnock/blob/master/Question_model/answers/bn_chainer.py)
