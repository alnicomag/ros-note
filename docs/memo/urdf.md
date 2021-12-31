## URDF とは

URDF とは Unified Robot Description Format の省略形で，リンクとジョイントと呼ばれる2種類のノードによって構成される木構造を用いてロボットの機械的構造を表現するファイルフォーマットです．根ノードは必ずリンクノードとなり，リンクノードは1つ以上の子ノードを持ち，ジョイントノードは唯一の子ノードを持ちます．そのため，葉ノードは全てリンクノードとなります．

リンクノードにはビジュアライゼーションに利用するための3Dデータを対応付けたり，質量や慣性モーメントなどの物理シミュレーションに必要な情報を書き込むことができます．

<figure markdown>
  ![](FHiv0hoaAAUx_xf.jfif)
  <figcaption>Fig. 1 単一リンクのみを持つ URDF を RViz で可視化させた例</figcaption>
</figure>



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