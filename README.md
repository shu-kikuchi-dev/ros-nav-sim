# ros-nav-sim
**注意: 本プロジェクトで使用を想定しているROS1 NoeticとGazebo Classicはそれぞれ、ROS1 Noeticは2025年5月31日にサービス終了予定、Gazebo Classicは2025年1月にサービス終了済みです。したがって、現在本プロジェクトは動作しません。**

SLAMアルゴリズムに基づいた、自己位置推定・地図作成・経路計画を含むナビゲーションロボットです。複数の既存パッケージを用いており、その結合によって実現しています。

## 開発環境セットアップ
### プロジェクトのインストールと設置
プロジェクトのファイル群を、ローカル環境の任意のワークスペースに展開します。

### コンテナ環境の作成とローカルデスクトップとの紐づけ
本プロジェクトは、WindowsにおけるDockerコンテナ上の仮想環境（Ubuntu20.04）にて、ROS1 Noeticで動作を確認済みです。

事前にDocker Desktopをインストールする必要があります。
- [Docker Desktop](https://docs.docker.com/get-started/get-docker/)

Docker Desktopをインストールが完了したら、Docker Desktopを起動した状態で、コマンドプロンプトにて以下のコマンドを実行します（'<>'内は任意の値に置き換えてください）。
```bash
docker run --name <任意のコンテナ名> -v <マウントしたいローカル環境のパス>:/home/ubuntu/catkin_ws:cached -p <任意のホストポート>:<任意のコンテナポート> --shm-size=<共有メモリディレクトリのサイズ> tiryoh/ros-desktop-vnc:noetic
```
ポート番号は6080:80、共有メモリディレクトリサイズは1024mなどと打ち込むとよいです。

また、マウントとはコンテナ上の任意のディレクトリと、ローカル環境の任意のディレクトリのファイル状況を同期するオプションです。コンテナ初回起動時以外はマウントはできないので注意してください。

コンテナの起動が完了したら、Docker Desktopから設定したポート番号のリンクをクリックし、Webブラウザで仮想環境を開きます。

### 仮想環境のセットアップ
仮想環境上でターミナルから、マウントで同期した仮想空間上のディレクトリに移動します。移動したら、以下のコマンドを実行してワークスペースを初期化します。
```bash
catkin build
source ~/catkin_ws/devel/setup.bash
```

### パッケージのインストール
必要なパッケージをすべてインストールします。
```bash
sudo apt install ros-noetic-teleop-twist-keyboard
sudo apt install ros-noetic-ira-laser-tools
sudo apt install ros-noetic-map-server
sudo apt install ros-noetic-amcl
sudo apt install ros-noetic-move-base
```
必要に応じて以下のコマンドを実行し、パッケージを最新版に更新します。
```bash
sudo apt update
sudo apt upgrade
```

## 実行
仮想空間上でターミナルを起動し、以下のコンマドをすべて個別に実行します。

roscoreを起動します。
```bash
roscore
```

ロボット情報を可視化します。
```bash
roslaunch LidarRobot1_description rviz_LidarRobot1.launch
```

ロボットモデルをGazeboシュミレータ内に出現させます。
```bash
roslaunch LidarRobot1_gazebo gazebo_LidarRobot1.launch
```

Gazeboに出現させたロボットモデルをキーボードで操作可能にします。
```bash
rosrun teleop_twist_keyboard teleop_twist_keyboard.py cmd_vel:=/LidarRobot1/diff_drive_controller/cmd_vel
```

Rvizを起動してナビゲーションを行います。
```bash
roslaunch LidarRobot1_2dnav navigation.launch
```

ナビゲーション項目についてはコマンド以外にも操作が必要です。必要に応じて以下の動画を参考にしてください。
- [ナビゲーションの様子](https://www.youtube.com/watch?v=EorUtEn90ak)
