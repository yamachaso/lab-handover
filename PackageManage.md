# vsctoolとrosdepについて

## vsctool
ROSパッケージ管理に使うバージョン管理ツールに関して、`vcstools`, `wstool`, `rosinstall`, `vcstool` などがあるが、基本的に`vcstool`を使っておけば良い。  
これらは要するにgithubやbitbucketのようなバージョン管理システム上で公開されている依存パッケージを自動でダウンロードできるツールである。  
`vcstool`はREP-115やREP-126の.rosinstall形式と`vcstool`のYAML形式どちらにも対応している。 

- インストール
```
$ sudo apt install python3-vcstool
```
- 依存パッケージのダウンロード
```
$ cd ~/catkin_ws/src
$ vcs import < ./<package_name>/.rosinstall
```
- アップデート
```
$ vcs pull src
```

## rosdep
ROSではパッケージの依存関係がpackage.xmlに記述されている。その記述を元に依存関係を自動的に解決してくれるツールが`rosdep`。
なお、依存パッケージはrosdepが参照する`rosdistro`リポジトリにリストされている必要があるため、自前のパッケージやgithubにのみ公開されているパッケージ等への依存関係の解決には先述の`vsctool`を用いる。


- 注意点  
sudo rosdep init はROSインストール時に一度だけ実行すればよく、それ以外の操作をするときには sudo をつけない。もしつけてしまった場合には、 sudo rosdep fix-permissions で修正する。


## 使い分け
野良のパッケージ(`rosdistro`リポジトリにないパッケージ)はpackage.xmlには書くことができないので、それらは.rosinstallに書いて`vcstool`で依存関係を解消し、その後公式リポジトリにあるパッケージに関する依存関係を解決するために`rosdep`を利用する。