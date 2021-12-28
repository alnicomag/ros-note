## VMware Workstation Playerを用いてWindowsマシン上にUbuntu仮想マシンを構築する

!!! info "環境"
    - Windows10
    - VMware Workstation Player 16.2.1
    - Ubuntu Desktop 18.04.6 LTS


### VMware Workstation Player のインストール

[ここ](https://www.vmware.com/jp/products/workstation-player.html)からダウンロードして普通にインストール．複数回再起動を要する．

### Ubuntu イメージファイルの入手

[ここ](https://releases.ubuntu.com/18.04/)からダウンロード

### Ubuntu仮想マシンの作成
入力を要するのは次のスクショくらい．進めていくと勝手に終わる．

<figure>
    <img width="374.25" src="adaf98de-35af-4a5e-8dd6-b68f47b8024b.png"/>
</figure>

### Ubuntuのアップデート
初回起動時に自動的に起動するはずだが，何れにせよ「Software Updater」というアプリを起動する．

<figure>
    <img width="800" src="aea37a3a-9f92-4ee2-83c2-2df2aab2a968.png"/>
</figure>

アップデートを勧められれば（Ubuntu20にはせずに）その通りすすめる．最終的にこれが表示されるのでSettingsボタンをクリックする．

<figure>
    <img width="800" src="b475cdfb-6fa8-4980-8f86-0af2744bc184.png"/>
</figure>


利用するリポジトリの種類を確認．以下の4つにチェックマークが入っていること．

- main
- restricted
- universe
- multiverse

<figure>
    <img width="800" src="2a1d5c5a-1c00-43d2-8757-09ef2cf4b415.png"/>
</figure>

ダウンロードサーバは適当なミラーサーバーを選ぶ．自動で良さげなところを探してくれるのでそれに任せてもいい．

<figure>
    <img width="800" src="ac682a6d-81c4-4c0f-bb0f-96d2e82801cf.png"/>
</figure>

アップデートが完了していることを確認してOKを押す．

<figure>
    <img width="800" src="b475cdfb-6fa8-4980-8f86-0af2744bc184.png"/>
</figure>

