# gitの使い方
SSH接続のための鍵生成

```
$ ssh-keygen rsa
```

生成された`id_rsa.pub`をgithubの設定ページにコピペしてテスト

```
$ ssh -T git@github.com
```

クローンする時

```
$ git clone $repositoryURL
```

変更結果を公開するとき

```
$ git add
$ git commit -m "commit massage"
$ git push
```

変更したファイルをすべてアップロード

```
$ git add .
```
