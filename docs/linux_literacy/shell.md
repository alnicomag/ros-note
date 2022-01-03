## ターミナルソフトとシェル

GUI 環境の Ubuntu にログインした後，デフォルトでインストールされている「Terminal」という名前のアプリケーションを利用して作業を始めると思います．Mac OS であれば「iTerm」というターミナルソフトがインストールされています．これはターミナルソフトと呼ばれるものです．シェルとは違います．

<figure markdown>
  ![](ss_terminal.png)
  <figcaption>Fig. 1 アプリケーション「Terminal」の見た目</figcaption>
</figure>

このターミナルソフトで様々なコマンド打ち込むことでシェルに対して命令を出すことができます．シェルはこの命令を解釈してカーネルに伝達することで実際の処理が行われます．つまり，シェルは画面上に見えているものを指すわけではなく，その画面を通して動作しているプログラムということになります．

ターミナルソフトにおいて CUI ベースの操作体系をとるこのようなシェルを特にコマンドラインシェルと呼びます．

## コマンドラインシェルの分類

シェルはターミナルソフト上で見えているものではなく，プログラムです．ただ1つだけ起動するという訳ではなく，複数起動することもあります．呼び出されれば起動し，処理が終われば終了します．あくまでもプログラムのひとつなので，その他の一般的なプログラムの挙動と同じ概念で考えることができます．

ただ，シェルがどのような条件で起動したのかによって呼び分けることがあります．その理由は起動のタイミングによって，ユーザーが指示を出す前に既に読み込まれている変数の種類に違いがあるからです．この変数には例えば「PATH」のようなプログラムを探しに行くディレクトリの情報などがあり，このような変数の総称を環境変数と呼びます．

### ログインシェルと非ログインシェル

コンピュータのあるユーザに対してログインしたその時に起動されるシェルはログインシェルと呼ばれます．例えば SSH 接続を利用してリモートマシンにログインした時です．

### 対話的シェルと非対話的シェル

### カレントシェルとサブシェル

シェルはプログラムの一種なので，シェルが起動した時，その実行中のプログラムは OS からはひとつのプロセスとして認識されます．起動しているプロセスは `pstree` というコマンドで確認できます．その結果を以下に示します．

```
systemd─┬─ModemManager───2*[{ModemManager}]
        ├─NetworkManager─┬─dhclient
        │                └─2*[{NetworkManager}]
        ├─VGAuthService
        ├─accounts-daemon───2*[{accounts-daemon}]
        ├─acpid
        ├─avahi-daemon───avahi-daemon
        ├─bluetoothd
        ├─boltd───2*[{boltd}]
        ├─colord───2*[{colord}]
        ├─cron
        ├─cups-browsed───2*[{cups-browsed}]
        ├─cupsd
        ├─dbus-daemon
     （省略）
        ├─systemd─┬─(sd-pam)
        │         ├─at-spi-bus-laun─┬─dbus-daemon
        │         │                 └─3*[{at-spi-bus-laun}]
        │         ├─at-spi2-registr───2*[{at-spi2-registr}]
        │         ├─dbus-daemon
        │         ├─dconf-service───2*[{dconf-service}]
        │         ├─evolution-addre─┬─evolution-addre───5*[{evolution-addre}]
        │         │                 └─4*[{evolution-addre}]
        │         ├─evolution-calen─┬─evolution-calen───8*[{evolution-calen}]
        │         │                 └─4*[{evolution-calen}]
        │         ├─evolution-sourc───3*[{evolution-sourc}]
        │         ├─gnome-shell-cal───5*[{gnome-shell-cal}]
        │         ├─gnome-terminal-─┬─bash───pstree
        │         │                 └─3*[{gnome-terminal-}]
        │         ├─goa-daemon───3*[{goa-daemon}]
        │         ├─goa-identity-se───3*[{goa-identity-se}]
        │         ├─gvfs-afc-volume───3*[{gvfs-afc-volume}]
        │         ├─gvfs-goa-volume───2*[{gvfs-goa-volume}]
        │         ├─gvfs-gphoto2-vo───2*[{gvfs-gphoto2-vo}]
        │         ├─gvfs-mtp-volume───2*[{gvfs-mtp-volume}]
        │         ├─gvfs-udisks2-vo───2*[{gvfs-udisks2-vo}]
        │         ├─gvfsd─┬─gvfsd-trash───2*[{gvfsd-trash}]
        │         │       └─2*[{gvfsd}]
        │         ├─gvfsd-fuse───5*[{gvfsd-fuse}]
        │         ├─gvfsd-metadata───2*[{gvfsd-metadata}]
        │         ├─ibus-portal───2*[{ibus-portal}]
        │         ├─nautilus───3*[{nautilus}]
        │         └─xdg-permission-───2*[{xdg-permission-}]
     （省略）
```

全てを記載すると長いので途中で省略した箇所がありますが，`systemd` → `systemd` → `gnome-terminal-` → `bash` → `pstree` という経路が確認できます．矢印の左側のプロセスが矢印の右側のプロセスを呼び出していると解釈してください．

ここで次のようなコマンドを入力してみます．

``` bash
pstree -p -s -U $$ | { cat; pstree -p -s -U $$; }
```

```
systemd(1)───systemd(1597)───gnome-terminal-(21431)───bash(21441)─┬─bash(28077)───cat(28078)
                                                                  └─pstree(28076)
systemd(1)───systemd(1597)───gnome-terminal-(21431)───bash(21441)───bash(28077)───pstree(28079)
```

## 色々あるコマンドラインシェル

### Bourne Shell (sh)

### Bash (bash)