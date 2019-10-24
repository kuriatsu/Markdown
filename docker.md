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
    ExecStart=/usr/bin/docker daemon -H fd:// -g /opt/docker
    ```
    * 変更後 :
    ```shell
    [Service]
    Type=notify
    EnvironmentFile=/home/kuriatsu/Program/Docker/docker_config
    ExecStart=/usr/bin/docker daemon $OPTIONS
    ```

1. docker_cofing作成

    内容 :
    ```shell
    OPTIONS="-H fd:// -g /home/kuriatsu/Program/Docker/docker"
    ```

1. 変更を反映

    ```shell
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```

## Dockerのimage格納先変更(2019/10/8 update~)

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
    OPTIONS="-H fd:// -g /home/kuriatsu/Program/Docker/docker"
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
