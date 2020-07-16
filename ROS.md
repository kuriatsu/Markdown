ROS
---

## catkin_make

```bash
###force build, ignore errors
catkin_make -i

###ignore specific packages
catkin_make -DCATKIN_BLACKLIST_PACKAGES="package1;package2..."
###restore ignoring
catkin_make -DCATKIN_BLACKLIST_PACKAGES=""

```

## create original msg
1. create msg directory under the package
1. create folder .msg
1. In package.xml, uncomment below
```
    <build_depend>message_generation</build_depend>
    <exec_depend>message_runtime</exec_depend>
```

1. In CmakeList.txt, add below
```
    find_package(catkin REQUIRED COMPONENTS ...
        ...
        ...
        message_generation <-
    )

    catkin_package(
        ...
        CATKIN_DEPENDS
        message_runtime <-
        ...
    )

    add_message_file(
        FILES
        ...
        <your_msg>.msg <-
    )

    generate_message(
        DEPENDENCIES
        ...
        <other msg (std_msgs etc.) which your msg depends >
    )    
```

## read options from roalaunch file

## create dynamic reconfigure


# Connect PCs with Ethernet cable

|PC|Address
|:-:|:--
|Master|192.168.2.1
|Sub PC|192.168.2.2

## Master PC

`ネットワーク設定`からErthanetの固定IPアドレスの設定を行なう


<image src="Picture/master_network.png">
1. `Edit Connection`を選択

2. `Add` 又は `Ethernet`のリストにあるネットワークを`Edit`


<image src="Picture/master_ip.png">
3. `IPv4`の`Address`を適当な値に設定

4. `NetMask`は適切な値に設定されているか確認(大抵24)

    255.255.255.0 -> 1110 ->

    =>デバイスのIPアドレス上3セクションが同じだったらそれらは皆同じネットワークに属しているとみなす

5. `Gateway`は外部のネットワークと通信する際に，経由するルータのAddressを設定する場所．今回はケーブル間通信なので設定する必要は無い


6. ROS通信のためにIP_addressの宣言

```bash
export ROS_IP=192.168.2.1
roscore
```
7. Sub PCの設定へ

8. 新しく作った自分と相手側の Address と hostname を加える

<image src="Picture/master_host.png">


## Sub PC

1. 1~5は一緒

2. ROS通信のためにIP_addressの宣言

```bash
#roscoreのある場所を設定

export ROS_MASTER_URI=http://192.168.2.1:11311
export ROS_IP=192.168.2.2
```
3. 新しく作った自分と相手側の Address と hostname を加える

## データ通信量の確認

```bash
sudo apt install htop rqt
```
### htop
データの処理量や通信速度を計測する

### rqt
ROSの便利なアプリケーション
ROSBAGの再生やトピックの確認ができる(Autowareの元的なやつ)

# Autoware

## Simulation
1. Setup
1. Map
1. Computing
    1. waypoint_loader
    1. lane_rule
    1. lane_select
    1. waypoint_loader
    1. wf_simulator -> set initial pose
    1. obstacle_avoid
    1. velocity_set
    1. vel_pose_connect <- simulation mode
    1. pure_pursuit
    1. twist_filter


# image compress -> raw
```bash

$ sudo apt install ros-melodic-image-transport-plugins
$ rosrun image_transport republish compressed in:=/camera_out/image_raw raw out:=/image_raw
```
