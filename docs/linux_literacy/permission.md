## 権限とは

## 確認方法

`ls` コマンドに `-l` オプションを付けることで権限情報も含めたディレクトリ情報が得られます．

``` bash
ls -l
```

<figure markdown>
  ![](permission/ss_ros-merodic-permission.png)
  <figcaption>Fig. 1 ROS Melodic のインストールディレクトリにあるファイルの権限を確認してみる</figcaption>
</figure>

Fig. 1 は ROS Melodic のインストールディレクトリで `ls` コマンドを `-l` オプション付きで実行した結果を示しています．各行の先頭10文字がそれぞれのファイルやディレクトリの権限を表しています．

| 場所 | 意味 |
| --- | --- |
| 1桁目 | ファイル・ディレクトリ・シンボリックリンクのどれであるかを表す<br>ファイル：`-`<br>ディレクトリ：`d`<br>シンボリックリンク：`l` |
| 2～4桁目 | ファイル所有者に対する権限を表す<br>左から順番にそれぞれの桁が読み取り・書き込み・実行権限を表す<br>読み取り権限がある場合は`r`，ない場合は`-`<br>書き込み権限がある場合は`w`，ない場合は`-`<br>実行権限がある場合は`x`，ない場合は`-` |
| 5～7桁目 | ファイル所有グループに対する権限を表す<br>書式は2～4桁目と同じ |
| 8～10桁目 | その他のユーザに対する権限を表す<br>書式は2～4桁目と同じ |

上の表はこの読み方を示したものです．

## 変更方法

例えば `sample.txt` というファイルの権限を所有者は `rwx`，所有グループは `rw-`，その他のユーザは `r--` と設定したい場合は次のように `chmod` コマンドを利用します．

``` bash
chmod 764 sample.txt
```

この 764 の数字はそれぞれの桁が所有者，所有グループ，その他のユーザの権限を表す数字になっています．各桁の数字は `rwx` の権限の並びを2進数の各桁とみなした上で，権限ありの桁に 1 を，権限なしの桁に 0 を代入した数の十進表記になっています．つまり権限 `rwx` は $4+2+1=7$ となり，`rw-` は $4+2+0=6$ となります．