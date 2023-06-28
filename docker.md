# Docker
---

## Dockerのimage格納先変更

1. docker.serviceの確認
```bash
sudo systemctl status docker
```
1. /lib/systemd/system/docker.serviceの変更

    * 変更前 :
    ```shell
    [service]
    Type=notifycgroup feature set required
    ExecStart=/usr/bin/dockerd -H fd:// -g /opt/docker --containerd=/run/containerd/containerd.sock
    ```
    * 変更後 :
    ```shell
    [Service]
    Type=notify
    EnvironmentFile=/home/kuriatsu/Program/Docker/docker_config
    ExecStart=/usr/bin/dockerd $OPTIONS --containerd=/run/containerd/containerd.sock
    ```

1. docker_cofing作成

    内容 :
    ```shell
    # OPTIONS="-H fd:// -g /home/kuriatsu/Program/Docker/docker"
    OPTIONS="-H fd:// --data-root /home/kuriatsu/Program/Docker/docker"
    ```
1. 変更を反映

    ```shell
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```

## Docker image の肥大化

dockerを上書きしていくと、dockerのレイヤーが蓄積してくためサイズがどんどん大きくなる。dockerのイメージの管理方法の関係で仕方がない。
dockerを起動し、起動中のコンテナをexportしてから再びimportすると、イメージではなくコンテナを保存・取り込むため、レイヤが無視されてイメージのサイズが軽くなる.

```shell
docker export [container name] > [file name].tar
docker import [fine name].tar [image name]:[tag]
```

## Change Docker user from root to arbitrary one

1. In container
    userid and groupid are ordinaly 1000. check all ids by `cat /etc/shadow`
    ```bash
    echo "[use name] ALL=(ALL) ALL" >> /etc/sudoers.d/[user name]
    groupadd -g [groupid] [user name]
    useradd -d /home/[user name] -m -s /bin/bash -g [groupid] -u [userid] [user name]
    passwd [user name]
    # input password
    ```
1. When run or begin

    add option `-u [user name]` or `-u [groupid:userid]`

## Docker with GUI

1. allow docker user to access host devise

    ```bash
    xhost local:[hostname of docker]
    ```

1. Add option when you start to run container

    ```bash
    --hostname [hostname of docker]
    -e DISPLAY=$DISPLAY \
    # -v /run/udev/:/run/udev/ \
    -v /tmp/.X11-unix/:/tmp/.X11-unix/ \
    ```

## subl in docker
```bash
$ wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
$ echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
$ sudo apt-get update
$ sudo apt-get install sublime-text
```
### trouble of terminal view
install from github, not install package
```bash
git clone https://github.com/Wramberg/TerminalView.git $HOME/.config/sublime-text-3/Packages/TerminalView
```

## docker networking


## docker cannot show image to X

If you get following error...

    X Error: BadAccess (attempt to access private resource denied) 10

do
```bash
$ export QT_X11_NO_MITSHM=1
```

## tab completion
```bash
sudo apt install bash-completion
```
