# 環境構築

# Docker周りの環境構築
ros_devでは必要に応じて環境をDockerコンテナに切り分け、相互通信させるという構成を用いている。  
Docker内でGPUプログラミングを行うが、その際の留意点が多いためここにおいてまとめる。
まず用語の整理として

- NVIDIAドライバ：Nvidia製のGPUを制御するためのドライバ
- CUDA：Nvidiaが開発しているGPUアクセラレーションアプリケーションを作成するための開発環境
- NVIDIA Container Toolkit：コンテナで NVIDIA GPU が使えるように、マウントなどの準備を自動で行ってくれるツール

Dockerコンテナ上でGPUプログラミングを実行するには上の3つが必要となるが、そのインストールを以下の表にしたがって実行することではじめて利用可能となる。

| ツール名 | インストール先 | インストール方法|
| :--- | :---: | :--- |
| NVIDIAドライバ | ホスト上 | $ sudo apt install nvidia-driver-\<version> |
| CUDA | Dockerコンテナ上 | $ sudo apt install cuda-toolkit-\<version> |
| NVIDIA Container Toolkit | ホスト上 | $ sudo apt install nvidia-container-toolkit

CUDA関連のメタパッケージには用途によって様々なものが用意されている。[こちら](https://qiita.com/hoge256/items/9255d7a4813a09f31d4a)を参照。



# ネットワーク周りの環境構築
Docker内から外のネットワークにアクセスするのはデフォルトのままで問題ないが、外のネットワークからDocker内にアクセスするのは追加で様々な設定を要する。ros_devにおける設定方法を以下にまとめる。

- イーサネットNICでネットワーク ブリッジを作る。
```
$ sudo vim /etc/netplan/99_config.yaml
```
で以下を書き込む。
```
network:
  version: 2
  renderer: networkd
  ethernets:
    enx3495db2e7cac: # 編集
      dhcp4: no
      dhcp6: no
  bridges:
    br0:
      interfaces: [enx3495db2e7cac] # 編集
      dhcp4: no
      dhcp6: yes
      addresses: [192.168.11.50/24] # 編集
      routes:
        - to: 192.168.11.0/24
          via: 192.168.11.1 # 編集←★
      nameservers:
        addresses: 
          - 192.168.11.1 # 編集←★
```

br0に「192.168.11.50」というアドレスを割り当てたが、これは他の機器とかぶらないようにすればなんでもOK。
ちなみに
```
$ ip route show
```
でデフォルトのデフォルトゲートウェイが確認できる。これを←★の部分に適用すれば良いと思われる。
以下で適用を行う。
```
$ sudo netplan apply
```
ここで
```
$ ip a
```
などとすると、enxa0cec8e74146にipアドレスがなくなっていてbr0にIPアドレスが割り振られているのが確認できる。
そしてブリッジの確認。
```
$ brctl show
```

-  Dockerネットワーク環境構築
Dockerネットワークを作成する。

```
$ docker network create \
  --driver=bridge \
  --subnet 192.168.11.0/24 \
  --gateway 192.168.11.50 \
  --opt "com.docker.network.bridge.name"="br0" \
  ros_dev_network
```

なお、ros_devにおいては`Makefile`にて上のコマンドを用意しており
```
$ make init
```
とすれば良い。

作成したDockerネットワークは以下で確認する。
```
$ docker inspect ros_dev_network
```

docker-compose.ymlで以下のようにipアドレスをコンテナごとに設定。
```
services:
  ros_melodic:
    ...
    networks:
      ros_dev_network: 
        ipv4_address: 192.168.0.51
    ...

  ros_noetic:
    ...
    networks:
      ros_dev_network: 
        ipv4_address: 192.168.0.52
    ...

networks:
  ros_dev_network:
    external: true
```
なお、ここではDockerコンテナのIPアドレスとして「192.168.11.51」「192.168.11.52」を用いた。