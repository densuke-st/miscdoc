.. _winget:

==================================================
wingetの利用
==================================================

注意
========================

PowerShellのプロンプトとして " :code:`PS>` " を用いています。
入力しないでください。
また、コマンドラインサンプル中に含まれる " :code:`#` " およびそれ以降はコメントです。そのため入力不要です。

この中で使っているPowerShell(端末)は、管理者モードで利用しています。
PowerShellを開く際に管理者モードで開くように注意しておいて下さい。

TL;DR
===========

- Windows10以降の公式のパッケージマネージャー
- 今すぐ入れろ
- ストアにて「アプリインストーラー」を探して入れれば良い
    - Windows11だと入ってるらしいが更新しないとダメかも

wingetって?
===============================

Microsoftが `Debian GNU/Linux <https://debian.org/>`_ (と派生物)で使われている `apt` や、 `Homebrew <https://brew.sh/>`_ と似た感じで、ソフトウェアの導入や削除をコマンドラインでお手軽に行うツールとして開発されました。

別にMS謹製のツールだけではなく、サードパーティ製のものでもインストールできたりします。
開発自体もOSS化されており、github上で見られます。

- `winget公式 <https://winget.run/>`_
- `wingetコマンド <https://github.com/microsoft/winget-cli>`_
- `パッケージリポジトリ <https://github.com/microsoft/winget-pkgs>`_
    - 厳密にはこのリポジトリは『インストール・アンインストールの方法』がまとめられているようなものです

インストール
============================

『アプリインストーラー』という名前でWindows11では標準で入っているようですが、
Windows11インストール直後では見えないことがあるので、一度更新をかけた方が良い。

1. 『Microsoftストア』アプリを開く
2. `app installer` を検索
3. 『アプリインストーラー』が見えればそれを選ぶ、更新が提示されていたら更新すれば良い

インストールできたかの確認については、管理者のプロンプト(PowerShellでも良い)を開けば確認できる。

.. code-block::
    :caption: インストール確認

    PS> Get-Command winget
    # エラーが出ていなければOK

どうやって使うん?
===================================

初回使用時や、サードパーティ製アプリケーションの導入時などにライセンスの確認がでることがあります。適宜 :kbd:`y` を入力して対応してください。

パッケージ検索
-----------------------------

入れたいものの名前を  :command:`winget search` で検索してみれば概ねわかります。

.. code-block::
    :caption: 例: Zoomを検索

        名前                     ID                                   バージョン 一致      ソース
    ----------------------------------------------------------------------------------------------
    ZOOM Cloud Meetings      XP99J3KP4XZ4VV                           Unknown              msstore
    Zoom Rooms               9NH8747BW3BC                             Unknown              msstore
    Zoom Player              XP88X30WJ519GR                           Unknown              msstore
    Zoom                     Zoom.Zoom                                5.9.3931             winget
    Zoom Outlook Plugin      Zoom.Zoom.OutlookPlugin                  5.9.3      Tag: zoom winget
    AeroZoom Beta            wandersick.AeroZoom                      4.0 beta 2 Tag: zoom winget
    Virtual Magnifying Glass VirtualMagnifyingGlass.VirtualMag...     3.6        Tag: zoom winget

インストールに使うのは、 IDの部分です。名前からすれば、Zoomのアプリは "Zoom.Zoom" が適当と思われます。

なお別の方法として、 `winget.run <https://winget.run/>`_ で検索するというのもあります。

.. figure:: images/01-winget-run1.png
    :width: 75%

    winget.runでZoomを検索したときの様子

.. figure:: images/01-winget-run2.png
    :width: 75%

    Zoomパッケージの情報

インストール
-------------------------

インストールは、  :command:`winget install` で行えばOK。

.. code-block::
    :caption: Zoomのインストール

    PS> winget install -h Zoom.Zoom # 調べておいたIDを引数に渡す

.. note::

    引数に渡している :code:`-h` はサイレントインストールを促すためのものです。
    問題無ければ『お任せ』の方法でインストールを進めてくれます。

アンインストール
-------------------------------

同様に  :command:`winget uninstall` で不要となったものは削除できます。

更新
----------------

同様に  :command:`winget upgrade` で更新版が出ているかを確認し、あれば更新を行っていきます。
ただし、配布元が更新版を出していても、wingetのアプリリポジトリ上で反映されていないと降ってこないこともありますので、急ぐときは配布元からダウンロード・更新をすれば良いでしょう。

.. code-block::
    :caption: 更新の確認

    PS> winget upgrade
    名前                                        ID                   バージョン   利用可能     ソース
    --------------------------------------------------------------------------------------------------
    Windows Terminal                    Microsoft.WindowsTerminal     1.11.3471.0  1.12.10393.0 winget
    Evernote                            evernote.evernote             10.32.4      10.33.4      winget
    Microsoft Windows Desktop Runtime  Microsoft.dotnetRuntime.3-x64 3.1.22.30721 3.1.23.31022  winget
    3 アップグレードを利用できます。

    PS> winget upgrade --all # 見つけた更新を全て適用

.. figure:: images/01-winget-run3.png
    :width: 75%

    実際の更新の様子

インストールされているものの確認
-----------------------------------------------

:command:`winget list` で取得できます。

おまけ
------------------------

悪名高きブラウザは、ここから簡単にアンインストールできます。

.. code-block::
    :caption: ブラウザの削除

    PS> winget uninstall Microsoft.Edge

.. note::

    なお削除の際に結構荒ぶることがあります。赤い×マークのウィンドウを表示しようとすることもありますが、
    適宜ウィンドウを閉じていけばそのうち消えます。
