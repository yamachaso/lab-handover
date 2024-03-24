# RealSense

## librealsenseのインストール
[こちら](https://github.com/IntelRealSense/librealsense/blob/development/doc/distribution_linux.md)のGithubのページの[**Installing the packages**](https://github.com/IntelRealSense/librealsense/blob/development/doc/distribution_linux.md#installing-the-packages)を実施する。

ターミナルを開き、
```
$ realsense-viewer
```
としてIntel RealSense Viewerが起動すればOK。



なお、RealSenseの機能をPythonから利用できるようにしたラッパーライブラリPyRealsenseを利用する場合は[ソースからビルド](https://github.com/IntelRealSense/librealsense/blob/development/doc/installation.md#install-librealsense2)する必要があることに注意。(ROSを通じてしかRealSenseを利用する予定がない場合は特に気にする必要はない)  
参考 : [Pythonではじめる3Dセンシング!!](https://mirai-tec.hatenablog.com/entry/2018/07/29/150902#:~:text=Python%E3%81%8B%E3%82%89%E4%BD%BF%E7%94%A8%E3%81%99%E3%82%8B%E3%81%AB%E3%81%AF%2C%20%E3%82%BD%E3%83%BC%E3%82%B9%E3%81%8B%E3%82%89%E3%81%AE%E3%83%93%E3%83%AB%E3%83%89%E3%81%8C%E5%BF%85%E8%A6%81.)


## realsense-rosのインストール
ROSを通じてRealSenseを利用できるようにしたラッパーが[こちら](https://github.com/IntelRealSense/realsense-ros/tree/ros1-legacy?tab=readme-ov-file)のGithubに公開されている。(ブランチに**ros1-legacy**が選択されているか注意)

まず依存パッケージのインストール。
```
$ sudo apt install ros-${ROS_DISTRO}-ddynamic-reconfigure
```

そしてROSの作業用のディレクトリを作成して移動し、以下を実行。
```
$ git clone https://github.com/IntelRealSense/realsense-ros.git
$ cd realsense-ros/
$ git checkout `git tag | sort -V | grep -P "^2.\d+\.\d+" | tail -1`
$ cd ..
```
ビルドしたら、PCにRealSenseを接続して以下によりノードを立ち上げる。
```
$ roslaunch realsense2_camera rs_camera.launch
```
以下でビューアーを立ち上げ、RealSenseから取得される情報を確認する。
```
$ rosrun rqt_image_view rqt_image_view
```