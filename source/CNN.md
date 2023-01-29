## NumpyだけでCNNを作ってみる。
# Numpyの復習から
## 参考文献URL
[NumPy 入門](https://tutorials.chainer.org/ja/08_Introduction_to_NumPy.html)

3×5の行列を出力するだけのプログラムで検証してみたところ、MNISTを読み込めない。
```
$  python sample_cnn.py
Traceback (most recent call last):
  File "/mnt/c/Users/ic201204/Documents/programming/programs/python_deep/programs/sample_cnn.py", line 7, in <module>
    from dataset.mnist import load_mnist
ModuleNotFoundError: No module named 'dataset.mnist'
```

どうやら、Pathが参照できていないと思われる。
そこで、cdでPathをカレントディレクトリを変更したところ上手くいった。
```
$ python sample_cnn.py
(3, 5)
```
