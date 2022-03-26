.. _virtualbox_vt:

仮想マシン関係の機能調整(Windows10,11)
================================================

VirtualBoxを単純に使うのであれば特段困らないのですが、
:ref:`Docker <docker-index>` を使う場合は地味にぶつかって困ることがあります。

『Windowsの機能の有効化または無効化』にて、2つの機能を有効にするといいでしょう。

(Homeエディション)仮想マシンプラットフォーム
    仮想マシン(Hyper-Vベース)を使うための機能を提供します。Docker DesktopやWSL2の利用で必要となります。
(Proエディション)Hyper-V
    Hyper-Vを使うための機能を提供します。Docker DesktopやWSL2の利用で必要となります。
    Proエディションの場合、『仮想マシンプラットフォーム』も選べますが、こちらを選ぶと良いでしょう(リソース管理等はこちらが有利)。
(Home/Pro)Windowsハイパーバイザープラットフォーム
    仮想化機構(Hyper-Vなど)を利用するときに、仮想化支援機能の並行利用を補助するための環境を提供します。
    Docker Desktop(WSL2利用)とVirtualBoxを同時利用する場合などはこちらも有効にする必要があります。

以上の設定を変更した場合、構成変更のための再起動が必要となります。

.. figure:: /images/windows-feature.png
    :width: 60%

    Windows11 Homeエディションでの設定例(VirtualBox+Docker Desktopの共存向け)

