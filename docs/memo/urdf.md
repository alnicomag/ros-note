## URDF とは

URDF とは Unified Robot Description Format の省略形で，リンクとジョイントと呼ばれる2種類のノードによって構成される木構造を用いてロボットの機械的構造を表現するファイルフォーマットです．根ノードは必ずリンクノードとなり，リンクノードは1つ以上のジョイントノードを子ノードとしてを持ち，ジョイントノードはただ1つのリンクノードを子ノードとして持ちます．そのため，葉ノードは全てリンクノードとなります．木構造を採用しているため直接的に閉リンクを表現することはできません．

木構造を記述する手段としては XML(Extensible Markup Language) が採用されています．XML は汎用的に利用できる構造記述のためのマークアップ言語で，文法は完全に異なりますが立ち位置としての類似言語には JSON や YAML などが挙げられます．

ここで，ボディに左右の車輪が付き1つの回転アームと1つのセンサが載っている簡単な2輪移動ロボットを URDF で表現することを考えます．

``` mermaid
flowchart TB
    L1(Body)
    J2{{Joint<br>回転}}
    L2(Right Wheel)
    J3{{Joint<br>回転}}
    L3(Left Wheel)
    J4{{Joint<br>固定}}
    L4(ArmBase)
    J5{{Joint<br>回転}}
    L5(Arm)
    J6{{Joint<br>固定}}
    L6(Sensor)

    L1 --> J2 --> L2
    L1 --> J3 --> L3
    L1 --> J4 --> L4 --> J5 --> L5
    L1 --> J6 --> L6
```

この図は URDF の木構造を図で表したものです．角丸ボックスがリンクを，六角形ボックスがジョイントを表しています．リンクが複数のジョイントを子ノードとして持っていても，ジョイントが複数のリンクを子ノードして持っていることはないということが確認できると思います．

ジョイントにはリンク同士の接合状態を説明させることができ，車輪はそれぞれ回転の自由度を持ち，センサは固定されているということが表現されています．リンクノードにはビジュアライゼーションに利用するための3Dデータを対応付けたり，質量や慣性モーメントなどの物理シミュレーションに必要な情報を書き込むことができます．

このような構造化されたデータを用意しておくことで，リンク間での座標変換処理なども簡潔に記述することが可能となります．

以下のコードは1つのリンクのみを持つ最も簡単な URDF のサンプルです．

``` xml
<?xml version="1.0"?>

<robot name="percepption_camera">
  <link name="base_link">
    <visual>
      <geometry>
        <mesh filename="package://perceptioncam_description/meshes/EnclosureAssy.stl" scale="0.001 0.001 0.001"/>
      </geometry>
    </visual>
  </link>
</robot>

```

これを Rviz と呼ばれる 3D 可視化ツールによって表示させると以下のようになります．

<figure markdown>
  ![](urdf/FHiv0hoaAAUx_xf.jfif)
  <figcaption>Fig. 1 単一リンクのみを持つ URDF を RViz で可視化させた例</figcaption>
</figure>
