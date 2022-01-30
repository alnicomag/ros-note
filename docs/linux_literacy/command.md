## ターミナルで遭難しないための Tips

覚えておいたほうが絶対に幸せになれると思われるキーボード操作を表にしておきます．

### カーソル移動

カーソル位置は ++arrow-left++ と ++arrow-right++ で移動できますが，これを覚えておくと行頭と行末への移動が一瞬です．

| タイプ | 機能 |
| --- | --- |
| ++ctrl+a++ | 行頭まで移動 |
| ++ctrl+e++ | 行末まで移動 |

### コピペ

| タイプ | 機能 |
| --- | --- |
| ++ctrl+k++ | カーソル位置から行末まで切り取り |
| ++ctrl+u++ | 行頭からカーソル位置まで切り取り |
| ++ctrl+y++ | 最後の切り取り文字を貼り付け |

### 履歴

同じコマンドを何度も手打ちする必要はありません．

| タイプ | 機能 |
| --- | --- |
| ++arrow-up++ または ++ctrl+p++ | 行を一つ前のコマンド履歴に置き換え |
| ++arrow-down++ または ++ctrl+n++ | 行を一つ後のコマンド履歴に置き換え |

### その他

| タイプ | 機能 |
| --- | --- |
| ++ctrl+c++ | 実行中のコマンドを強制終了 |
| ++tab++ | 入力補完 |

## 頻出するシェル文法

### リダイレクション

!!! note "（標準出力の）リダイレクション"
    ``` bash title="書式"
    コマンド > ファイル名
    ```
    `コマンド` の出力先を切り替え，標準出力の内容を `ファイル名` に書き込みます．ファイル書き込みの権限は `コマンド` に `sudo` を付与していた場合でも元のユーザー権限が適用されます．

!!! note "（標準入力の）リダイレクション"
    ``` bash title="書式"
    コマンド < ファイル名
    ```
    `コマンド` への入力元を切り替え，`ファイル名` の内容を `コマンド` への標準入力として使用します．

### パイプ

!!! note "パイプ"
    ``` bash title="書式"
    コマンド1 | コマンド2 | コマンド3
    ```
    `コマンド1` の標準出力を `コマンド2` の標準入力へ繋げます．ふたつのコマンドだけでなく任意の数のコマンドを順次繋げていくことができます．パイプ後の各々のコマンドは全てカレントシェルから派生する独立したサブシェルで実行されます．

### 変数

!!! note "シェル変数"
    ``` bash title="書式"
    変数名=値
    ```
    `変数名`という名前で値が`値`の変数を定義します．

### 括弧

!!! note "$()"
    ``` bash title="書式"
    $(コマンド)
    ```
    丸括弧で囲まれた文字列をコマンドと解釈して実行した結果を文字列として展開します．ネストできます．

!!! note "${}"
    ``` bash title="書式"
    ${変数}
    ```
    `変数` の値を文字列として展開します．

!!! note "()"
    ``` bash title="書式"
    (コマンド1; コマンド2)
    ```
    丸括弧で囲まれた複数のコマンドをサブシェル内で実行します．

### クォーテーション

!!! note "バッククォート"
    ``` bash title="書式"
    `コマンド`
    ```
    バッククォートで囲まれた文字列をコマンドと解釈して実行した結果を文字列として展開します．バッククォート同士でネストはできません．

!!! note "ダブルクォート"
    ダブルクォートで囲まれた文字列のうち，`$`，バッククォート，バックスラッシュのみを解釈し，それ以外をエスケープした文字列を得る

!!! note "シングルクォート"
    シングルクォートで囲まれた文字列全てをエスケープした文字列を得る

### 条件分岐

!!! note "AND"
    ``` bash title="書式"
    コマンド1 && コマンド2
    ```
    コマンド1が成功した場合のみ，コマンド2を実行する

!!! note "OR"
    ``` bash title="書式"
    コマンド1 || コマンド2
    ```
    コマンド1が失敗した場合のみ，コマンド2を実行する

## 特殊なファイル

/dev/null

## よく使うコマンドたち

ls

cd

mkdir

rmdir

mv

rm

cp

echo

cat

touch

tee

pwd

bash [opption] [file]

bash -c string

dirname

### export
```
export [option] [変数名]
```

### pstree
```
pstree [option] [プロセスID or ユーザ名]
```

標準出力にプロセスツリーを出力する．

| オプション | 意味 |
| --- | --- |
| `-p` | プロセス ID を表示する |
| `-s` | プロセス ID が指定されている場合，指定したプロセスの親を表示する |
| `-U` | 罫線に UTF-8 文字を利用する |

### :
```
: [param]
```

`param` を評価するが，何もしない．

### unset
```
unset [option] [name ...]
```

変数を削除する．

```
Unset values and attributes of shell variables and functions.
For each NAME, remove the corresponding variable or function.
    
Options:
    -f	treat each NAME as a shell function
    -v	treat each NAME as a shell variable
    -n	treat each NAME as a name reference and unset the variable itself rather than the variable it references

Without options, unset first tries to unset a variable, and if that fails, tries to unset a function.

Some variables cannot be unset; also see `readonly'.

Exit Status: Returns success unless an invalid option is given or a NAME is read-only.
```


### test
```
test [option] [param]
```

| `[option]` | `[param]` | 意味 |
| --- | --- | --- |
| `-z` | 文字列 | 文字列の長さが0の時，True． |
| `-f` | ファイル | ファイルが存在する時，True． |
| `-d` | ディレクトリ | ディレクトリが存在する時，True． |

```
test [param] [option] [param]
```

```
[ [option] [param] ]
```

### uname
```
uname [option]
```

標準出力にシステム情報を出力する．

| `[option]` | 意味 |
| --- | --- |
| `-a` | 全情報を出力する |
| `-s` | カーネル名を出力する |


``` title="option"
-a, --all                print all information, in the following order, except omit -p and -i if unknown:
-s, --kernel-name        print the kernel name
-n, --nodename           print the network node hostname
-r, --kernel-release     print the kernel release
-v, --kernel-version     print the kernel version
-m, --machine            print the machine hardware name
-p, --processor          print the processor type (non-portable)
-i, --hardware-platform  print the hardware platform (non-portable)
-o, --operating-system   print the operating system
    --help               display this help and exit
    --version            output version information and exit
```

### printenv
```
printenv [option] [variable]
```

指定された環境変数の値を標準出力に出力する．指定のない場合は全ての環境変数の値を出力する．

| `[option]` | 意味 |
| --- | --- |
| `-0` | 改行をしない |

`export -p` でも似たような結果が得られるが，書式が少し違う．