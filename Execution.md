# 実行方法

## コンテナの起動
rosdevにおいて
```
$ make start
```
とすればDockerネットワークが作成され、Dockerコンテナが起動する。
DockerコンテナとしてはUbuntu18.04の`melodic`用コンテナとUbuntu20.04の`noetic`用コンテナの２つを生成しており、`melodic`側でroscoreを実行するような構成となっている。

なお、外部のPC等からROSで通信するためにはそのPCの`.bashrc`に以下を記述する。
```
export ROS_MASTER_URI=http://192.168.0.51:11311 # melodicでマスターを起動しているので
export ROS_IP=`hostname -I | cut -d' ' -f1`
```

## プログラムの実行

### melodic側のコマンド
```
$ make melodic
```
として`melodic`用のコンテナに入ることができる。
4つ画面を開いてそれぞれで以下を打つ。
```
$ roslaunch myrobot_moveit bringup.launch
$ roslaunch myrobot_moveit right_denso_robot_control.launch
$ roslaunch myrobot_moveit left_denso_robot_control.launch
$ roslaunch cart_control bringup.launch
$ roslaunch myrobot_moveit auto_planning.launch
```

なお、注意点として`bringup.launch`を起動してから`right_denso_robot_control.launch`および`left_denso_robot_control.launch`を起動するまでに時間が大きく空いてしまうとMoveItの方で実機の関節角度を捕捉できない場合がある。5秒以内が目安。

### noetic側のコマンド
```
$ make noetic
```
として`noetic`用のコンテナに入ったあと、どうように以下の2つを実行する。
```
$ roslaunch detect ar_track_alver.launch
$ roslaunch detect bringup.launch
```

なお、こちらの`bringup.launch`はmelodic側の`auto_planning.launch`の実行画面で
```
movegroup load : SUCCESS
```
が表示されたのを確認してから起動する。
