# catkinについて
## catkin build  
`catkin build`とはROSのパッケージをビルドするための新しいコマンド。`catkin_make`と異なり並列ビルドが可能で、またどのディレクトリにいてもビルドすることが可能。  
ROSにはデフォルトで含まれていないため、インストールする必要がある。

```
$ sudo apt install python-catkin-tools # melodic以前
```

```
$ sudo apt-get install python3-catkin-tools # noetic
```

パッケージごとにビルドすることも可能で、以下のようにすると現在いるパッケージのビルドを行うことができる。
```
$ catkin build --this
```

## ビルド高速化
`ccache`を利用すると、ビルド時に変更されていないファイルのコンパイルが省略されるため、何度もビルドを実行する際に非常に便利。
```
$ sudo apt install ccache
```


## 時間測定
ビルドにかかる時間を測定する方法。
```
$ time catkin build
```

