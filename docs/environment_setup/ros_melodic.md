## ROS MelodicをUbuntu仮想マシン上に導入する

!!! info "環境"
    - Windows10
    - VMware Workstation Player 16.2.1
    - Ubuntu Desktop 18.04.6 LTS
    - ROS Melodic Morenia

### ROSのパッケージリポジトリ情報を登録する

```
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

??? note "sh"
    - 書式：``sh -c `コマンド文字列` ``
    - 意味：`コマンド文字列` をシェルに実行させる


??? note "echo"
    - 書式：`echo "文字列" > ファイル名`
    - 意味：ファイル名が `ファイル名` で表されるファイルの内容を `文字列` で上書き保存する

??? note "lsb_release"
    - 書式：`lsb_release -sc`
    - 意味：ディストリビューションのコードネームを短縮形式で取得するコマンド

この工程は Ubuntu において APT が ROS のパッケージリポジトリを見つけられるようにするための設定です．

Ubuntu ではソフトウェア（アプリケーション）の単位をパッケージと呼びます．一般的に複雑なソフトウェアであればあるほど，ソフトウェアは自身以外のソフトウェアが利用出来る状態にあることを前提として作成されています．このような関係を依存関係と呼びます．依存関係はソフトウェア間に連鎖的に存在するため，適切なバージョンを選択することも考えると，あるソフトウェアを利用するためだけに膨大な数のパッケージのインストールを要求されるということが起こり得ます．これは非常に面倒なので，Ubuntu にはソフトウェアのインストールやアンインストールを管理しパッケージ間の依存関係の解決を行うパッケージ管理システム APT が含まれています．

APT でパッケージ管理をする上で，リポジトリ情報は `/etc/apt/sources.list` に保存します．ただしこれは公式リポジトリの場合の話で，サードパーティリポジトリの場合は `/etc/apt/sources.list.d/` ディレクトリ以下に `hogehoge.list` のようなファイルを適宜作成し，その中に情報を保存します．

この `sources.list` ファイルの書式は [https://manpages.ubuntu.com/manpages/bionic/en/man5/sources.list.5.html](https://manpages.ubuntu.com/manpages/bionic/en/man5/sources.list.5.html) から確認できて，該当箇所を抜粋すると以下のようになっています．

```
deb [ option1=value1 option2=value2 ] uri suite [component1] [component2] [...]
```

`uri` の部分をリポジトリの URI に，`suite` の部分を Ubuntu のコードネームに，`component` の部分をコンポーネント名に書き換えます．コンポーネントとはパッケージの分類のことで，Ubuntu ではパッケージをサポートの状態やライセンスの違いに基づき以下の4つに分類しています．

- main
- restricted
- universe
- multiverse

同じソフトウェアであってもそれぞれのコンポーネントに合わせたパッケージが複数用意されているリポジトリもあるので，対象にしたいものを選択して記述することになります．

Ubuntu のコードネームは `lsb_release -sc` コマンドにより取得でき，今回は Ubuntu 18 上で実行しているので，`bionic` が得られます．

<figure>
    <img width="800" src="95b1a6ec-09b1-4e03-a473-d8b601c77c64.png"/>
</figure>

#### 参考にしたページ
[第677回 aptで使うsources.listのオプションいろいろ：Ubuntu Weekly Recipe｜gihyo.jp … 技術評論社](https://gihyo.jp/admin/serial/01/ubuntu-recipe/0677)
[APT の設定 (/etc/apt/sources.list) をちゃんと理解する - くじらにっき++](https://kujira16.hateblo.jp/entry/2019/10/14/190008)


### ROSのパッケージ認証用公開鍵を追加する
```
sudo apt install curl
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

コマンドの意味：`curl`コマンドを`silent`オプション付きで実行して得られた公開鍵の文字列を，パイプによって`apt-key add -`コマンドへ標準入力として渡すことで，認証用公開鍵を追加する．

ちなみに `https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc` にアクセスしてみると以下の公開鍵ファイルであることが分かる．

```
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1

mQINBFzvJpYBEADY8l1YvO7iYW5gUESyzsTGnMvVUmlV3XarBaJz9bGRmgPXh7jc
VFrQhE0L/HV7LOfoLI9H2GWYyHBqN5ERBlcA8XxG3ZvX7t9nAZPQT2Xxe3GT3tro
u5oCR+SyHN9xPnUwDuqUSvJ2eqMYb9B/Hph3OmtjG30jSNq9kOF5bBTk1hOTGPH4
K/AY0jzT6OpHfXU6ytlFsI47ZKsnTUhipGsKucQ1CXlyirndZ3V3k70YaooZ55rG
aIoAWlx2H0J7sAHmqS29N9jV9mo135d+d+TdLBXI0PXtiHzE9IPaX+ctdSUrPnp+
TwR99lxglpIG6hLuvOMAaxiqFBB/Jf3XJ8OBakfS6nHrWH2WqQxRbiITl0irkQoz
pwNEF2Bv0+Jvs1UFEdVGz5a8xexQHst/RmKrtHLct3iOCvBNqoAQRbvWvBhPjO/p
V5cYeUljZ5wpHyFkaEViClaVWqa6PIsyLqmyjsruPCWlURLsQoQxABcL8bwxX7UT
hM6CtH6tGlYZ85RIzRifIm2oudzV5l+8oRgFr9yVcwyOFT6JCioqkwldW52P1pk/
/SnuexC6LYqqDuHUs5NnokzzpfS6QaWfTY5P5tz4KHJfsjDIktly3mKVfY0fSPVV
okdGpcUzvz2hq1fqjxB6MlB/1vtk0bImfcsoxBmF7H+4E9ZN1sX/tSb0KQARAQAB
tCZPcGVuIFJvYm90aWNzIDxpbmZvQG9zcmZvdW5kYXRpb24ub3JnPokCVAQTAQgA
PgIbAwULCQgHAgYVCgkICwIEFgIDAQIeAQIXgBYhBMHPbjHmut6IaLFytPQu1vur
F8ZUBQJgsdhRBQkLTMW7AAoJEPQu1vurF8ZUTMwP/3f7EkOPIFjUdRmpNJ2db4iB
RQu5b2SJRG+KIdbvQBzKUBMV6/RUhEDPjhXZI3zDevzBewvAMKkqs2Q1cWo9WV7Z
PyTkvSyey/Tjn+PozcdvzkvrEjDMftIk8E1WzLGq7vnPLZ1q/b6Vq4H373Z+EDWa
DaDwW72CbCBLWAVtqff80CwlI2x8fYHKr3VBUnwcXNHR4+nRABfAWnaU4k+oTshC
Qucsd8vitNfsSXrKuKyz91IRHRPnJjx8UvGU4tRGfrHkw1505EZvgP02vXeRyWBR
fKiL1vGy4tCSRDdZO3ms2J2m08VPv65HsHaWYMnO+rNJmMZj9d9JdL/9GRf5F6U0
quoIFL39BhUEvBynuqlrqistnyOhw8W/IQy/ymNzBMcMz6rcMjMwhkgm/LNXoSD1
1OrJu4ktQwRhwvGVarnB8ihwjsTxZFylaLmFSfaA+OAlOqCLS1OkIVMzjW+Ul6A6
qjiCEUOsnlf4CGlhzNMZOx3low6ixzEqKOcfECpeIj80a2fBDmWkcAAjlHu6VBhA
TUDG9e2xKLzV2Z/DLYsb3+n9QW7KO0yZKfiuUo6AYboAioQKn5jh3iRvjGh2Ujpo
22G+oae3PcCc7G+z12j6xIY709FQuA49dA2YpzMda0/OX4LP56STEveDRrO+CnV6
WE+F5FaIKwb72PL4rLi4
=i0tj
-----END PGP PUBLIC KEY BLOCK-----
```

### Aptのパッケージ一覧を更新
```
sudo apt update
```

先の工程でROSのパッケージリポジトリ情報を登録したので，それらの情報が追加されるはず．

### ROSをインストール
```
sudo apt install ros-melodic-desktop-full
```

### 環境変数の設定
```
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

コマンドの意味：bash起動毎に実行される`~/.bashrc`ファイルの末尾に`source /opt/ros/melodic/setup.bash`を追記し，その追記した`~/.bashrc`ファイルを実行する．つまり，`/opt/ros/melodic/setup.bash`に書かれた設定がbash起動毎に読み込まれることになる．

### ROSパッケージをなんやかんやするためのツールを導入
```
sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
```

### rosdep（ROSパッケージをなんやかんやするためのツールのひとつ）の初期化
```
sudo rosdep init
rosdep update
```

### ROSのワークスペースを作成、ビルド
```
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make
echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

```
echo $ROS_PACKAGE_PATH
```
でPATHが通っているか確認する．
```
/home/hogehoge/catkin_ws/src:/opt/ros/melodic/share
```
hogehogeのところはユーザー名で読み替えて下さい．


### 確認
```
roscore
```
<img width="922.5" alt="image.png (113.5 kB)" src="https://img.esa.io/uploads/production/attachments/15139/2021/12/03/12652/eb7e4ef5-6e6d-4208-9376-1ad2d406d217.png">

