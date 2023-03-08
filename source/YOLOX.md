# YOLOXの使い方  
[物体検出手法の一つYOLOXのチュートリアル](https://book.st-hakky.com/docs/object-detection-yolox-tutorial/)  
[YOLOX(公式)](https://github.com/Megvii-BaseDetection/YOLOX)  
まずは、Driveのマウント  
以下をGoogle Colabで実行  

```
from google.colab import drive
drive.mount('/content/drive')

# フォルダの移動
%cd ./drive/MyDrive
```

YOLOXをインストール

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




学習用フォルダの作成とデータセットの移動

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


