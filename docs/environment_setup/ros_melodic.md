## ROS Melodic を Ubuntu 仮想マシン上に導入する

!!! info "環境"
    - Windows10
    - VMware Workstation Player 16.2.1
    - Ubuntu Desktop 18.04.6 LTS

上記の環境に新たに ROS Melodic Morenia を導入する方法を説明します．既に別のディストリビューションが導入されている場合などの考慮はここではしていません．

### ROS のパッケージリポジトリ情報を登録する

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
    <img width="800" src="ros_melodic/95b1a6ec-09b1-4e03-a473-d8b601c77c64.png"/>
</figure>

#### 参考にしたページ
- [第677回 aptで使うsources.listのオプションいろいろ：Ubuntu Weekly Recipe｜gihyo.jp … 技術評論社](https://gihyo.jp/admin/serial/01/ubuntu-recipe/0677)
- [APT の設定 (/etc/apt/sources.list) をちゃんと理解する - くじらにっき++](https://kujira16.hateblo.jp/entry/2019/10/14/190008)


### ROS のパッケージ認証用公開鍵を追加する
```
sudo apt install curl
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
```

??? note "apt"
    - 書式：`apt install パッケージ名`
    - 意味：`パッケージ名` をAPT管理の元インストールする

??? note "curl"
    - 書式：`curl -s URL`
    - 意味：`URL` で表されたファイルの内容を取得し標準出力に出力する

??? note "apt-key"
    - 書式：`apt-key add -`
    - 意味：標準入力から得られた文字列を APT のパッケージ認証用公開鍵として追加する

??? note "パイプ"
    - 書式：`コマンド1 | コマンド2`
    - 意味：`コマンド1` の標準出力を `コマンド2` の標準入力へ繋げる

APT は必要とあらばそのパッケージをリポジトリからダウンロードしてきますが，そのダウンロードしたファイルが本当にその作者によって作成されたものであるのか（悪意あるパッケージにすり替えられていないかどうか）検証する機能を持っています．その方法として公開鍵暗号方式による電子署名が利用されています．そのため ROS のリポジトリ提供者が公開している認証用公開鍵を APT に登録しておく必要があります．

2行目のコマンドにはパイプ処理が活用されています．これにより，`curl` コマンドの結果を標準出力に表示させずに，直接 `at-key` コマンドへの入力として繋げることができます．

`curl` コマンドは `-s` オプションを付けることでファイル取得中のプログレス表記をミュートさせることができます．これにより取得したファイルの内容のみをパッケージ認証用公開鍵として追加することができます．

[https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc](https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc) にアクセスしてみるとその内容は以下の公開鍵ファイルであることが分かります．

``` asc
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

### APT のパッケージ一覧を更新
```
sudo apt update
```

??? note "apt"
    - 書式：`apt update`
    - 意味：APT のパッケージ情報を更新する

先の工程で ROS のパッケージリポジトリ情報を登録しました．そのため，それらのパッケージ情報が新たに取得できます．このコマンドではパッケージがインストールされたりバージョンアップがされたりということはなく，登録されているリポジトリにどのようなパッケージが存在しているのかという情報が更新されるだけです．

### ROS をインストール
```
sudo apt install ros-melodic-desktop-full
```

### 環境変数の設定
```
echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

??? note "echo"
    - 書式：`echo "文字列" >> ファイル名`
    - 意味：ファイル名が `ファイル名` で表されるファイルの末尾に `文字列` を追記する

??? note "source"
    - 書式：`source ファイル名`
    - 意味：`ファイル名` に書かれたコマンドを現在のシェルで実行する

`~/.bashrc` ファイルは bash 起動毎に毎回読み込まれるファイルです．`~/.bashrc`ファイルの末尾に`source /opt/ros/melodic/setup.bash`が追記されることで，`/opt/ros/melodic/setup.bash` に書かれたスクリプトが bash 起動毎に呼び出され，その呼び出し元のシェルで実行されることになります．

``` bash linenums="1" title="/opt/ros/melodic/setup.bash"
#!/usr/bin/env bash
# generated from catkin/cmake/templates/setup.bash.in

CATKIN_SHELL=bash

# source setup.sh from same directory as this file
_CATKIN_SETUP_DIR=$(builtin cd "`dirname "${BASH_SOURCE[0]}"`" > /dev/null && pwd)
. "$_CATKIN_SETUP_DIR/setup.sh"

```

??? note "builtin"
    - 書式：`builtin コマンド`
    - 意味：同じ名前のコマンドがエイリアスとビルトインコマンドの両方で見つかった場合にビルトインコマンドの方を優先させる

??? note "dirname"
    - 書式：`dirname パス名`
    - 意味：パス名からディレクトリ名を取得する

??? note "リダイレクション"
    - 書式：`コマンド > ファイル名`
    - 意味：`コマンド` の実行結果を標準出力ではなく `ファイル名` に出力する

??? note "バッククォート"
    バッククォートで囲まれた文字列をコマンドとして実行した結果を得る

??? note "ダブルクォート"
    ダブルクォートで囲まれた文字列のうち，`$`，バッククォート，バックスラッシュのみを解釈し，それ以外をエスケープした文字列を得る

??? note ドット
    - 書式：`. ファイル名`
    - 意味：`ファイル名` に書かれたコマンドを現在のシェルで実行する

このスクリプトにより，環境変数 `CATKIN_SHELL` は `bash` に，`_CATKIN_SETUP_DIR` は `/opt/ros/melodic` となり，結局 `/opt/ros/melodic/setup.sh` が現在のシェルで実行されます．

``` bash linenums="1" title="/opt/ros/melodic/setup.sh"
#!/usr/bin/env sh
# generated from catkin/cmake/template/setup.sh.in

# Sets various environment variables and sources additional environment hooks.
# It tries it's best to undo changes from a previously sourced setup file before.
# Supported command line options:
# --extend: skips the undoing of changes from a previously sourced setup file
# --local: only considers this workspace but not the chained ones
# In plain sh shell which doesn't support arguments for sourced scripts you can
# set the environment variable `CATKIN_SETUP_UTIL_ARGS=--extend/--local` instead.

# since this file is sourced either use the provided _CATKIN_SETUP_DIR
# or fall back to the destination set at configure time
: ${_CATKIN_SETUP_DIR:=/opt/ros/melodic}
_SETUP_UTIL="$_CATKIN_SETUP_DIR/_setup_util.py"
unset _CATKIN_SETUP_DIR

if [ ! -f "$_SETUP_UTIL" ]; then
  echo "Missing Python script: $_SETUP_UTIL"
  return 22
fi

# detect if running on Darwin platform
_UNAME=`uname -s`
_IS_DARWIN=0
if [ "$_UNAME" = "Darwin" ]; then
  _IS_DARWIN=1
fi
unset _UNAME

# make sure to export all environment variables
export CMAKE_PREFIX_PATH
if [ $_IS_DARWIN -eq 0 ]; then
  export LD_LIBRARY_PATH
else
  export DYLD_LIBRARY_PATH
fi
unset _IS_DARWIN
export PATH
export PKG_CONFIG_PATH
export PYTHONPATH

# remember type of shell if not already set
if [ -z "$CATKIN_SHELL" ]; then
  CATKIN_SHELL=sh
fi

# invoke Python script to generate necessary exports of environment variables
# use TMPDIR if it exists, otherwise fall back to /tmp
if [ -d "${TMPDIR:-}" ]; then
  _TMPDIR="${TMPDIR}"
else
  _TMPDIR=/tmp
fi
_SETUP_TMP=`mktemp "${_TMPDIR}/setup.sh.XXXXXXXXXX"`
unset _TMPDIR
if [ $? -ne 0 -o ! -f "$_SETUP_TMP" ]; then
  echo "Could not create temporary file: $_SETUP_TMP"
  return 1
fi
CATKIN_SHELL=$CATKIN_SHELL "$_SETUP_UTIL" $@ ${CATKIN_SETUP_UTIL_ARGS:-} >> "$_SETUP_TMP"
_RC=$?
if [ $_RC -ne 0 ]; then
  if [ $_RC -eq 2 ]; then
    echo "Could not write the output of '$_SETUP_UTIL' to temporary file '$_SETUP_TMP': may be the disk if full?"
  else
    echo "Failed to run '\"$_SETUP_UTIL\" $@': return code $_RC"
  fi
  unset _RC
  unset _SETUP_UTIL
  rm -f "$_SETUP_TMP"
  unset _SETUP_TMP
  return 1
fi
unset _RC
unset _SETUP_UTIL
. "$_SETUP_TMP"
rm -f "$_SETUP_TMP"
unset _SETUP_TMP

# source all environment hooks
_i=0
while [ $_i -lt $_CATKIN_ENVIRONMENT_HOOKS_COUNT ]; do
  eval _envfile=\$_CATKIN_ENVIRONMENT_HOOKS_$_i
  unset _CATKIN_ENVIRONMENT_HOOKS_$_i
  eval _envfile_workspace=\$_CATKIN_ENVIRONMENT_HOOKS_${_i}_WORKSPACE
  unset _CATKIN_ENVIRONMENT_HOOKS_${_i}_WORKSPACE
  # set workspace for environment hook
  CATKIN_ENV_HOOK_WORKSPACE=$_envfile_workspace
  . "$_envfile"
  unset CATKIN_ENV_HOOK_WORKSPACE
  _i=$((_i + 1))
done
unset _i

unset _CATKIN_ENVIRONMENT_HOOKS_COUNT

```

### ROSパッケージをなんやかんやするためのツールを導入
```
sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
```

| ツール名 | 用途 |
| --- | --- |
| python-rosdep | ROS パッケージの依存解決 |
| python-rosinstall |  |
| python-rosinstall-generator |  |
| python-wstool |  |
| build-essential |  |

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



``` bash linenums="1" title="~/catkin_ws/devel/setup.bash"
#!/usr/bin/env bash
# generated from catkin/cmake/templates/setup.bash.in

CATKIN_SHELL=bash

# source setup.sh from same directory as this file
_CATKIN_SETUP_DIR=$(builtin cd "`dirname "${BASH_SOURCE[0]}"`" > /dev/null && pwd)
. "$_CATKIN_SETUP_DIR/setup.sh"

```

``` bash linenums="1" title="~/catkin_ws/devel/setup.sh"
#!/usr/bin/env sh
# generated from catkin/cmake/template/setup.sh.in

# Sets various environment variables and sources additional environment hooks.
# It tries it's best to undo changes from a previously sourced setup file before.
# Supported command line options:
# --extend: skips the undoing of changes from a previously sourced setup file
# --local: only considers this workspace but not the chained ones
# In plain sh shell which doesn't support arguments for sourced scripts you can
# set the environment variable `CATKIN_SETUP_UTIL_ARGS=--extend/--local` instead.

# since this file is sourced either use the provided _CATKIN_SETUP_DIR
# or fall back to the destination set at configure time
: ${_CATKIN_SETUP_DIR:=/home/alnico/catkin_ws/devel}
_SETUP_UTIL="$_CATKIN_SETUP_DIR/_setup_util.py"
unset _CATKIN_SETUP_DIR

if [ ! -f "$_SETUP_UTIL" ]; then
  echo "Missing Python script: $_SETUP_UTIL"
  return 22
fi

# detect if running on Darwin platform
_UNAME=`uname -s`
_IS_DARWIN=0
if [ "$_UNAME" = "Darwin" ]; then
  _IS_DARWIN=1
fi
unset _UNAME

# make sure to export all environment variables
export CMAKE_PREFIX_PATH
if [ $_IS_DARWIN -eq 0 ]; then
  export LD_LIBRARY_PATH
else
  export DYLD_LIBRARY_PATH
fi
unset _IS_DARWIN
export PATH
export PKG_CONFIG_PATH
export PYTHONPATH

# remember type of shell if not already set
if [ -z "$CATKIN_SHELL" ]; then
  CATKIN_SHELL=sh
fi

# invoke Python script to generate necessary exports of environment variables
# use TMPDIR if it exists, otherwise fall back to /tmp
if [ -d "${TMPDIR:-}" ]; then
  _TMPDIR="${TMPDIR}"
else
  _TMPDIR=/tmp
fi
_SETUP_TMP=`mktemp "${_TMPDIR}/setup.sh.XXXXXXXXXX"`
unset _TMPDIR
if [ $? -ne 0 -o ! -f "$_SETUP_TMP" ]; then
  echo "Could not create temporary file: $_SETUP_TMP"
  return 1
fi
CATKIN_SHELL=$CATKIN_SHELL "$_SETUP_UTIL" $@ ${CATKIN_SETUP_UTIL_ARGS:-} >> "$_SETUP_TMP"
_RC=$?
if [ $_RC -ne 0 ]; then
  if [ $_RC -eq 2 ]; then
    echo "Could not write the output of '$_SETUP_UTIL' to temporary file '$_SETUP_TMP': may be the disk if full?"
  else
    echo "Failed to run '\"$_SETUP_UTIL\" $@': return code $_RC"
  fi
  unset _RC
  unset _SETUP_UTIL
  rm -f "$_SETUP_TMP"
  unset _SETUP_TMP
  return 1
fi
unset _RC
unset _SETUP_UTIL
. "$_SETUP_TMP"
rm -f "$_SETUP_TMP"
unset _SETUP_TMP

# source all environment hooks
_i=0
while [ $_i -lt $_CATKIN_ENVIRONMENT_HOOKS_COUNT ]; do
  eval _envfile=\$_CATKIN_ENVIRONMENT_HOOKS_$_i
  unset _CATKIN_ENVIRONMENT_HOOKS_$_i
  eval _envfile_workspace=\$_CATKIN_ENVIRONMENT_HOOKS_${_i}_WORKSPACE
  unset _CATKIN_ENVIRONMENT_HOOKS_${_i}_WORKSPACE
  # set workspace for environment hook
  CATKIN_ENV_HOOK_WORKSPACE=$_envfile_workspace
  . "$_envfile"
  unset CATKIN_ENV_HOOK_WORKSPACE
  _i=$((_i + 1))
done
unset _i

unset _CATKIN_ENVIRONMENT_HOOKS_COUNT

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

<figure>
    <img width="800" src="ros_melodic/eb7e4ef5-6e6d-4208-9376-1ad2d406d217.png"/>
</figure>