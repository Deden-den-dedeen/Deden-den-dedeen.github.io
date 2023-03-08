# YOLOXの使い方  
[物体検出手法の一つYOLOXのチュートリアル](https://book.st-hakky.com/docs/object-detection-yolox-tutorial/)  
[YOLOX(公式)](https://github.com/Megvii-BaseDetection/YOLOX)  
まずは、YOLOXをインストール

```
!git clone git@github.com:Megvii-BaseDetection/YOLOX.git
!cd YOLOX
!pip3 install -v -e .  # or  python3 setup.py develop
```

-nや-fを用いて、検出器を指定する  
参考：

```
python tools/demo.py image -n yolox-s -c /path/to/your/yolox_s.pth --path assets/dog.jpg --conf 0.25 --nms 0.45 --tsize 640 --save_result --device [cpu/gpu]
```

もしくは、

```
python tools/demo.py image -f exps/default/yolox_s.py -c /path/to/your/yolox_s.pth --path assets/dog.jpg --conf 0.25 --nms 0.45 --tsize 640 --save_result --device [cpu/gpu]
```

ビデオの場合

```
python tools/demo.py video -n yolox-s -c /path/to/your/yolox_s.pth --path /path/to/your/video --conf 0.25 --nms 0.45 --tsize 640 --save_result --device [cpu/gpu]
```

# COCOでの再現
COCOデータセットの準備

```
cd <YOLOX_HOME>
ln -s /path/to/your/COCO ./datasets/COCO
```

-nを指定して、結果を再現

```
python -m yolox.tools.train -n yolox-s -d 8 -b 64 --fp16 -o [--cache]
                               yolox-m
                               yolox-l
                               yolox-x
```

-fを使用する場合

```
python -m yolox.tools.train -f exps/default/yolox_s.py -d 8 -b 64 --fp16 -o [--cache]
                               exps/default/yolox_m.py
                               exps/default/yolox_l.py
                               exps/default/yolox_x.py
```



# Google Colab で実行する場合(COCO,FiftyOne)
まずは、Driveのマウント
以下をGoogle Colabで実行

```
from google.colab import drive
drive.mount('/content/drive')

# フォルダの移動
%cd ./drive/MyDrive
```

### FiftyOneのインストール
opencv-python-headlessの4.3以降のバージョンでは下位互換性が失われているためバージョンをダウングレード

```
!pip install fiftyone
!pip install "opencv-python-headless<4.3"
```

### データセットのダウンロード

```
import fiftyone.zoo as foz

dataset = foz.load_zoo_dataset(
   ""coco-2017"",
   split="validation",
   label_types=["detections"],
   classes=["banana", "apple", "orange"],
   max_samples=100,
   only_matching=True,
)
dataset.name = "coco2017-fruit"
dataset.persistent = True
```

パラメータは以下の通り  
> - `split` : train, validation, testの中から選択  
> - `label_types` : 物体検出なのでdetectionsであるが他にもsegmentationsなどのデータの種類がある  
> - `classes` : 物体検出を行う際のバウンディングボックスに付与するクラスを指定
> - `max_samples` : 取得する画像の枚数を指定
> - `only_matching` : Trueにすることで画像内に他のラベルが含まれている画像を取得しないようにする


### データセットをtrainとvalidationに分割

```
train_data = dataset[:80]
val_data = dataset[80:100]
```

### データセットをYOLOXの対応しているcocoに変換

```
classes = ["banana", "apple", "orange"]

train_data.export(
    export_dir=f"./dataset/train_data",
    dataset_type=fo.types.COCODetectionDataset,
    classes=classes,
)

val_data.export(
    export_dir=f"./dataset/val_data",
    dataset_type=fo.types.COCODetectionDataset,
    classes=classes,
)
```

> - `export_dirs` ： 変換後に保存するディレクトリを指定する
> - `dataset_type` ： coco以外にもvocなども指定できる
> - `classes` : バウンディングボックスに付与するクラスを指定


### YOLOXのインストール
下記コマンドでYOLOXと必要なパッケージをインストールできる

```
!git clone https://github.com/Megvii-BaseDetection/YOLOX
%cd YOLOX
!pip install -qr ./requirements.txt 
!pip install -v -e .
```


### 学習用フォルダの作成とデータセットの移動

```
# フォルダの作成
%cd ../
%mkdir -p ./YOLOX/datasets/dataset/train2017/
%mkdir -p ./YOLOX/datasets/dataset/val2017/
%mkdir -p ./YOLOX/datasets/dataset/annotations

# datasetフォルダをYOLOX内のdatasetsへ移動させます
!cp -rf dataset/train_data/data/*.jpg YOLOX/datasets/dataset/train2017/
!cp -rf dataset/val_data/data/*.jpg YOLOX/datasets/dataset/val2017/
!cp -rf dataset/train_data/labels.json YOLOX/datasets/dataset/annotations/train.json
!cp -rf dataset/val_data/labels.json YOLOX/datasets/dataset/annotations/val.json
```


## YOLOXの学習
### tiny.pyの編集

```
%cd YOLOX
!cp -rf exps/default/yolox_tiny.py ./
```

次に、YOLOXフォルダ内の`yolox_tiny.py`のファイルを編集する。`yolox_tiny.py`に画像、アノテーションファイルを指定するためパスを追記

```
  self.data_dir = "./datasets/dataset"
  self.train_ann = "train.json"
  self.val_ann = "val.json"
  # エポック回数の指定
  self.max_epoch = 100
  # クラス数の指定
  self.num_classes = 3
```


### 重みのダウンロード
`YOLOX_tiny`の重みをダウンロード

```
!wget https://github.com/Megvii-BaseDetection/YOLOX/releases/download/0.1.1rc0/yolox_tiny.pth
```


### 学習

```
!python tools/train.py -f yolox_tiny.py -b 16 -d 1 --fp16 -o -c ./yolox_tiny.pth
```


### 結果
検証を行いたい画像ファイルをYOLOX直下に置く  
先ほどの学習で保存されたモデルはYOLOX内の`YOLOX_outputs/yolox_tiny`に`best_ckpt.pth`という名前で保存されている  
toolsの中にあるdemo.pyのimport文の真下に以下のコードを追記

```
from yolox.data.data_augment import ValTransform
from yolox.data.datasets import COCO_CLASSES
from yolox.exp import get_exp
from yolox.utils import fuse_model, get_model_info, postprocess, vis

# 以下を追記
COCO_CLASSES = ("クラス名1", "クラス名2", "クラス名3")
```

以下のコマンドで検証
```
!python tools/demo.py image -f yolox_tiny.py -c {モデルのパス} --path {画像ファイルのパス} --save_result --device gpu
```

