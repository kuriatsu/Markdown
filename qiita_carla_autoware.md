# AutowareとCARLAを使って自動運転をする

## 概要
普段は[Autoware](https://www.autoware.ai/)という自動運転ソフトウェアを使ってロボットを動かしていますが、最近は実験環境としてシミュレータも使おうということで[CARLA](http://carla.org/)を導入しています。[LGSVL](https://www.lgsvlsimulator.com/)もありますが、色々あってCARLAを使っています。[LGとAutowareのチュートリアル記事](https://qiita.com/hakuturu583/items/297adfd8ad0fa54d1a24)は既に投稿されていますが、CARLAの記事はあまり無いようですので、投稿してみることにしました。AutowareもCARLAもLGもオープンソースなのでぜひどちらも試してみてください。

## 環境
* CPU: Intel Core i7
* GPU: Nvidia GTX1080Ti 11GB
* OS: Ubuntu 16.04
* CUDA: 11

## インストールとビルド
### ROS

Rosのインストールは[ROS Wiki](http://wiki.ros.org/ja/kinetic/Installation/Ubuntu)を参照してください。他の記事も充実しています。

### Autoware-1.11.1

[Autoware Wiki](https://gitlab.com/autowarefoundation/autoware.ai/autoware/-/wikis/Source-Build)に従ってインストールしていきます。

1. 依存関係をインストール
```bash
sudo apt-get update
sudo apt-get install -y python-catkin-pkg python-rosdep ros-$ROS_DISTRO-catkin gksu
sudo apt-get install -y python3-pip python3-colcon-common-extensions python3-setuptools python3-vcstool
pip3 install -U setuptools
```

1. Autowareのソースコードをダウンロード  
まだ最新バージョンを使っていないので1つ古いバージョンです。ご容赦ください。
```bash
cd $HOME
# 公式gitからダウンロード
git clone https://gitlab.com/autowarefoundation/autoware.ai/autoware.git
cd autoware
git checkout 1.11.1
# rosdepの依存関係をインストール
cd ros
rosdep update
rosdep install -y --from-paths src --ignore-src --rosdistro $ROS_DISTRO
echo "export /opt/ros/kinetic/setup.bash" > ~/.bashrc
echo "export ~/autoware/ros/devel/setup.bash" > ~/.bashrc
```

1. ビルド
```bash
catkin_make --DCMAKE_BUILD_TYPE=Release
```

### CARLA-0.9.6
今回はバイナリビルド版を使います。最新バージョンはテストしていないので、バージョン0.9.6をダウンロードします。Unreal EngineでCARLAをゴリゴリカスタムしたい人や、今後PythonではなくC++のAPIを使いたい人は[公式Wiki](https://carla.readthedocs.io/en/latest/how_to_build_on_linux/)を参考にUnreal Engineでソースからビルドしてもいいと思います。

```bash
cd $HOME
# 依存関係のインストール
# バイナリビルド版をダウンロード
wget http://carla-assets-internal.s3.amazonaws.com/Releases/Linux/CARLA_0.9.6.tar.gz
tar -xvf CARLA_0.9.6.tar.gz carla
echo "export PYTHONPATH=$PYTHONPATH:~/carla/dist/carla-0.9.6-py2.7-linux-x86_64.egg:~/carla/" > ~/.bashrc
```

### CARLA Autoware Bridge

CARLAでAutowareを使うためのプログラムを用意します。[CARLA公式github](https://github.com/carla-simulator/carla-autoware)からダウンロードしてビルドします。carla-ros-bridgeやAutowareのlaunchファイルなどが入っています。

```bash
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
sudo apt-get install git-lfs
cd $HOME
git lfs clone https://github.com/carla-simulator/carla-autoware.git
cd carla-autoware
git submodule update --init
export CARLA_MAPS_PATH=~/carla-autoware/autoware_data/maps
source ~/carla-autoware/catkin_ws/devel/setup.bash
```

## 自動走行 (CUIで全部起動してみる)

1. CARLA起動

	まずはCARLAのサーバーを立ち上げます。世界を俯瞰したい場合は先頭のオプションは無しでいいですが、GPU食います。WASD等で移動できて楽しいので、試してみてください。俯瞰画面から出られなくなったらAlt+Escで出れます。opengl使いたい人は`--opengl`加えてください。

	```bash
	SDL_VIDEODRIVER=offscreen ./carla/CarlaUE4.sh --opengl
	```

1. carla-ros-bridgeとAutowareの起動

	```bash
	roslaunch $CARLA_AUTOWARE_ROOT/devel.launch
	rviz
	```

1. 位置推定

	自車両がどこにいるのかをRvizで手動で入力します。
	1. Rvizを起動
	1. 表示情報を設定
	1. 初期位置を入力

位置推定が完了したら車両が動き出します。

## CARLAの環境設定
* 他の歩行者や車両を置く(勝手に動き出します)
```bash
# -n: 車両の数 -w: 歩行者の数
python ~/carla/PythonAPI/example/spawn_npc.py -n 50 -w 80
```
任意に移動させるには[scenario-runner](https://github.com/carla-simulator/scenario_runner)を使うか、公式wikiのドキュメントを読んでスクリプトを書いてください。

* [天気](https://carla.readthedocs.io/en/stable/carla_settings/)や地図を変える
```bash
# -m: Town01~05 --weather: ↑リンク参照  
python ~/carla/PythonAPI/util/config.py -m Town05 --weather ClearNoon
```

## まとめ
今回はCARLA内でAutowareを簡単に動かしてみました。ただしAutowareの醍醐味はGUIで車両を動かせるということなので、いいねが45個以上付いたらGUIからセットアップする方法も投稿してみようと思います。

Autowareは基本的に公式GitからのcloneでOKですが、Open Plannerを使ってみたい方は[こちら](https://github.com/hatem-darweesh/autoware/tree/op-x)からcloneしてください。[開発者](https://github.com/hatem-darweesh)がCarla用にチューニングしたOpen Plannerを使うことができます。私はOpen Plannerの方が好きです。素晴らしいプランナーです。使い方についても追い追い投稿してみようと思います。
