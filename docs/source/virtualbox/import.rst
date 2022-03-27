.. _virtualbox-import:

仮想マシンのインポート
================================

仮想マシンは **エクスポート** という処理をすることであるタイミングの状態をファイルのかたちで固めて保存できます。
保存したもの(イメージ)は **インポート** という処理で取り込んで使うことができます。

VirtualBoxの場合、主に以下の形式のインポートが使われています。

OVA(Open Virtualization Appliance)
    仮想マシンのイメージ構成の形式(ストレージ込み)
OVF(Open Virtualization Format)
    仮想マシンのイメージ構成の形式(ストレージ別)

一般的にはOVA形式の方が広く流通しています。

VirtualBoxでのインポートは :menuselection:`ファイル-->仮想アプライアインスのインポート` を選んで下さい。
インポートしたいファイル(ova形式)を指定することで、内容の確認が出るので、そのまますすめれば基本的にOKです。

.. figure:: /images/vm-import1.png
    :width: 75%

    ファイルの指定

.. figure:: /images/vm-import2.png
    :width: 75%

    設定の確認(普通はいじる必要ない)

.. figure:: /images/vm-import3.png
    :width: 75%

    インポート中(今時のマシンなら普通は1〜2分で終わります)

インポート後はメインメニューに仮想マシンがリストされます。

.. figure:: /images/vm-import4.png
    :width: 75%

    インポート後、VMの一覧にインポートしたVMが出ます

何かやらかしたときのために、VMのスナップショットを作っておくのも良いでしょう。
