## プログラミング環境の構築
まずは、多用するディレクトリへのaliasを設定。
.bashrcに以下を書き込む。  ※`$ . .bashrc`もしくは`$ source .bashrc`を忘れずに！
```
alias program='cd /mnt/c/Users/$Username/Documents/programming/programs/'
alias ai='cd /mnt/c/Users/$Username/Documents/programming/programs/python_deep'
alias git='cd ~/Deden-den-dedeen.github.io'
```
$Usernameは各々書き換える。

CNNのlogを記述するための.mdファイルのエイリアスを設定
```
function CNNlog(){
        cd ~/Deden-den-dedeen.github.io/source
        vi CNN.md
}
```
次に、pipenv仮想環境の構築
まずは、pythonとpip、pipenvのインストール
```
$ sudo apt install python3.9
$ sudo apt install python3-pip
$ pip install pipenv
```

Windowsで作成済みの仮想環境に入ってみる。
```
$ pipenv shell
```

すると下記のようなエラー
```
Creating a virtualenv for this project...
Pipfile: /mnt/c/Users/ic201204/Documents/programming/programs/python_deep/Pipfile
Using /usr/bin/python3.9 (3.9.5) to create virtualenv...
⠹ Creating virtual environment...ModuleNotFoundError: No module named 'virtualenv.seed.via_app_data'

✘ Failed creating virtual environment
Failed to create virtual environment.
```
以下のサイトを参考にしても効果なし。
[`ModuleNotFoundError: No module named 'virtualenv.seed.via_app_data'`](https://qiita.com/youichi_io/items/c1764f720c13ff05823c)

仕方なく一度、pipとpythonをすべて削除。
```
$ python -m pip uninstall pip
$ sudo apt purge pip3
```

その後、再度インストールしなおすと上手くいった。
```
$ sudo apt install python3.9
$ sudo apt install python3-pip
$ pipenv shell
...
Successfully created virtual environment!
...
```

