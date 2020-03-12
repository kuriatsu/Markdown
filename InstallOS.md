hp ZBook studio g4 ubuntu18.04インストール (Windows とデュアルブート)
===

パーティション割り当て [windows]
--
1. Windowsボタン右クリック
1. [ディスクの管理]
1. C:ドライブ部分を右クリックしてボリュームの縮小
⁂ もっと減らしたかったらhttp://matasuke.asablo.jp/blog/2015/12/18/7952311


Ubuntu16.04.5 Live media 作成　(できればUSB3.0が欲しい 2GBくらい)
--
### windows

1. universal-usb-installer のインストール
1. bittorrent downloader のインストール
1. ubuntu-16.04.5-desktop-amd64.iso.torrent のダウンロード　https://www.ubuntu.com/download/alternative-downloads　
1. Livemedia作成　https://kledgeb.blogspot.com/2014/04/ubuntu-1404-4-windowsusb.html　

### ubuntu
1. Disksのアプリケーションでフォーマット(MBRで)
1. ubuntu-16.04.5-desktop-amd64.iso.torrent のダウンロード　https://www.ubuntu.com/download/alternative-downloads　
1. ubuntu-xx.xx.x-desktop-amd64.iso.torrent をダブルクリック
1. ダイアログで "I agree" を押して.iso イメージダウンロード
1. usb挿し込む
1. usbのパーティション */dev/xxx* を確認　例: */dev/sda*
```bash
$ sudo fdisk -l
```
1. ダウンロード
```bash
$ dd if=/path/to/.isofile of=/dev/xxx bs=4M
```

BIOS設定 (作成したUSBを挿す)  
--
1. リブート
1. Boot画面でF10
1. セキュアブート/CSMサポート設定 (どこかにあるはず)  

|セキュアブート|CSMサポート||
|-:-|-:-|-:-|
|有効|有効|💀  死ぬ|
|無効|有効|👼 おすすめ|
|無効|無効|👼 大丈夫|

1. レガシーサポート有効化 (どっちでもいい)
1. 高速起動　無効化  
    [詳細設定]タブ -> [ブートオプション] -> 高速起動
1. ブート順序設定  
    [詳細設定]タブ -> [UFEIブート順序] -> USBを最上位に持ってくる
1. 設定を保存して終了



Ubuntu　インストール
--
1. usbを差し込んで、**try ubuintu without install**
1. 起動したらubuntu installを選択  
1. 指示に従ってインストール

    1. 言語は英語をおすすめする
    1. (ディスクの準備)Windowsとは別のものとしてインストールし，起動時に選べるようにしておく
    1. 手動でパーティションを設定  
    ext4 30GB / と ext4 好きなだけGB /home を作る
    1. その他の設定は自由 (最小構成でダウンロードOK, ドライバ関連はインストール)


nvidia-driver インストール
--
### nouveau 停止

1. nouveau グラフィックドライバを無視する設定ファイルを作成
  ```bash
  $ sudo nano /etc/modprove.d/blacklist-nouveau.conf
  ```  
  下を書き込む
  ```
  blacklist nouveau
  blacklist lbm-nouveau
  options nouveau modeset=0
  alias nouveau off
  alias lbm-nouveau off
  ```

1. 設定を反映

  ```bash
  $ sudo update-initramfs –u
  $ sudo reboot
  ```

### nvidia driver へ切り替え

1. **Software & Updates** 起動
1. [Additional Drivers] -> nvidia-driver xxx の中から *proprietary, tested* のドライバを選択して、[apply]

CUDA10.0 cudnn7 tensorflow インストール
--
[ここでバージョン確認](https://www.tensorflow.org/install/source#common_installation_problems)

### モノの入手
[cuda10.0](https://developer.nvidia.com/cuda-10.0-download-archive)
[linux]->[x86_64]->[Ubuntu]->[18.04]->[dev[local]]

### インストール
```bash
sudo dpkg -i ダウンロードしたやつ.deb
sudo apt install cuda-toolkit-10-0
rm ダウンロードしたやつ.deb
```
### cudnn7のインストール
1. libcudnn7 libcudnn7-dev の適切なバージョンを
[ここから探す](https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64/)

  ```bash
  echo "deb https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1804/x86_64 /" | sudo tee /etc/apt/sources.list.d/nvidia-ml.list
  sudo apt update
  sudo apt install libcudnn7-dev=7.4.2.24-1+cuda10.0 libcudnn7=7.4.2.24-1+cuda10.0
  ```

1. 環境変数の追加

  /home/user/.bashrc 最下行に以下を追加
  ```
  export PATH="/usr/local/cuda/bin:$PATH"
  export "LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH"
  ```
1. reboot
1. NVIDIAドライバの確認

  ```bash
  $ nvidia-smi
  $ nvcc -V
  ```
1. CUDA サンプル実行

  ```bash
  $ cuda-install-samples-9.0.sh ~/path/to/anywhere
  $ cd ~/path/to/anywhere/NVIDIA_CUDA-9.0_Samples/
  $ sudo make
  $ cd 2_Graphics/volumeRender
  $ 2_Graphics/volumeRender
  ```

### Tensorflow

1. インストール

    ```bash
    $ pip –V
    $ pip3 -V
    $ pip install tensorflow-gpu=1.14.0
    $ pip3 install tensorflow-gpu=1.14.0
    ```
1. 動作確認

    ```bash
    $ python3
    >>> import tensorflow as tf
    >>> print(tf.__version__)
    1.14.0
    >>> tf.test.is_gpu_available(cuda_only=False, min_cuda_compute_capability=None)
    True
    ```

### 参考文献
[cuda cudnn インストール](https://qiita.com/yukoba/items/4733e8602fa4acabcc35)



Ros
--
http://wiki.ros.org/ja/kinetic/Installation/Ubuntu  

## Autoware

https://github.com/CPFL/Autoware/wiki/Source-Build  

## Opencv (やらなくていい。rosとは別でopenCVを使いたい場合のみ)

一応Ros インストール時にopencvもインストールされるが，python3系では使えないので，使えるように設定も含めてインストール

http://cyaninfinite.com/tutorials/installing-opencv-in-ubuntu-for-python-3/  

ここでRosインストール時に$PATHをいじっているので，python3系ではimportできない

.bashrcでPYTHONPASSにpython3.5のdist-packageのpassを，Rosインストール時に追加したPASSより下に追加して使えるようにしたい．

    $ Vi ~/.bashrc

で$PASSの編集（これやるとcatkin_init_workspaceでROSの環境構築できなくなります）vi使い方


## ROS(kinetic)の環境構築

1. yp-spur と　yp-spur-rosのクローン

    ```bash
    # ROSとは独立した，機械側の制御パッケージ
    $ git clone https://github.com/openspur/yp-spur
    # ROSと機械をつなぐパッケージ
    $ git clone https://github.com/openspur/ypspur_ros
    ```

1. catkinで workspaceの作成

    ```bash
    $ mkdir –p ~/catkin_ws/src
    $ cd ~/catkin_ws/src
    $ catkin_init_workspace
    $ cd ..
    $ catkin_make
    ```
　
1. PATHの設定

    ```bash
    $ echo “source ~/catkin_ws/devel/setup.bash” >>  ~/.bashrc
    # ROSのPATHが追加されているか確認
    $ echo $ROS_PACKAGE_PATH
    ```
動作確認

    ```bash
    $ roscore
    ```

## install WPS softs

1. サイトからamd64 linux版ダウンロード

    ```bash
    cd /path/to/wps_deb_file.deb
    sudo dpkg -i pkg.deb
    ```
1. フォント設定

    ```bash
    mkdir /path/to/fonts/
    git clone https://github.com/iamdh4/ttf-wps-fonts.git
    sudo fc-cache -vfs ttf-wps-fonts
    ```

## Atom setup for latex

1. latex ビルドパッケージインストール
    ```bash
    sudo apt install latexmk　texlive-latex-extra texlive-lang-japanese
    ```
1. atom　のパッケージインストールにて以下をインストール

    1. language-latex
    2. latex

        セットアップ
        * Engine - uplatex
        * Enable - SyncTex
        * Enable - Extended Build Mode
        * Output Format - pdf
        * PDF Producer - dvipdfmx

    3. latexer
    4. pdf-view


## Google drive

google-drive-ocamlfuse を使う。

```bash
# install
sudo add-apt-repository ppa:alessandro-strada/ppa
sudo apt-get update
sudo apt-get install google-drive-ocamlfuse

# create mount point
mkdir /home/user/mount/point
google-drive-ocamlfuse /home/user/mount/point

# use multiple account
google-drive-ocamlfuse -label [user2] /home/user/mount/point

# unmount
fusermount -u /home/user/mount/point
```

## その他
1. 日本語入力

        sudo apt install ibus-mozc

1. caps 殺す

        sudo nano /etc/default/keyboad

    書き込む

        XKBOBTIONS="ctrl:nocaps"
