.. _virtualbox-install:

インストール
==================

:ref:`winget <winget>` が入っていれば特に難しいことはありません。
管理者端末を使えばさっとインストールを指示できます。

.. code-block::
    :caption: VirtualBoxのインストール(winget)

    PS> winget install -h Oracle.VirtualBox

macOSの方はIntel macの場合はhomebrewでいけますが、 **M1 macの場合は動かせません** 。
学校での授業用VMも現状では用意されていないので、Windows端末を用意して行って下さい。

.. code-block:: zsh
    :caption: VirtualBoxのインストール(macOS, homebrew)

    % brew install virtualbox


手動で入れたい方
-----------------------------

:code:`winget` もできない残念な方は、VirtualBoxのサイトにて入手して下さい。

- `Oracle VM VirtualBox <https://www.virtualbox.org/>`_
- `Downloads <https://www.virtualbox.org/wiki/Downloads>`_

ダウンロードするファイルは、ホスト(インストールするシステム)のOSを選択して下さい。
よく勘違いされますが、Windows(10,11)上で仮想マシンを走らせる場合はWindows版を選びます。
インストーラーをダウンロードした後、実行して指示に従って進めればインストールできます。マウスポチポチは面倒くさい。
