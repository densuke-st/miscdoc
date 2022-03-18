.. _docker-commands:

============================================
よく使いそうなDockerのコマンド
============================================


.. _run:

:code:`run` (:code:`container run`)
======================================

- イメージからコンテナを生成する
- 特に指示が無ければイメージに埋め込まれているコマンドが実行される
- コンテナ名は指定が無ければ適当(形容詞_名詞)で生成される

使い方
------------------

.. code-block::
    :caption: 使い方

    % docker run [run向けオプション...] イメージ名 [コンテナ引数...]

イメージは事前にpullしているならそれが使われます。
ない時はその場でpullを開始、完了してから実行されます。
命令的には  :command:`docker container run` と行きたいところですが、非常によく使うため短縮命令として(従来から使われていた)  :command:`docker run` がそのまま採用されています。

run向けオプション
-----------------------

きちんと調べたいときはオンラインマニュアルか  :command:`docker run --help` で確認してください。
ここでは頻出オプションを簡単に記しています。

:code:`-i`
    コンテナのstdinを端末に繋ぎます(対話操作が可能になる)。

:code:`-t`
    コンテナの出力をダム端末ではなく制御端末に結びつけます(文字色の変更や内部カーソル移動などをサポート)。
    いわゆる端末に接続という意味では、 :code:`-it` の使い方が多い。

:code:`--rm`
    コンテナ終了時に自動的に破棄します。

:code:`-v FROM:TO`
    ホスト側のディレクトリFROMをコンテナ内のディレクトリTOにマップします。
    これを使ってホスト側のデータをコンテナに送り込むことができますが、コンテナの操作の影響を逆に招き入れる可能性もあるので要注意。
    もし読み出しのみで良いなら :code:`-v FROM:TO:ro` とすればコンテナ側での書き込みは抑制できます。

:code:`-p FROM:TO`
    ホスト側のポートFROMをコンテナ内ポートTOにマップします。
    これによりコンテナ側でサーバープロセスを起動し、ホスト側から接続できるようになります。

.. code-block::
    :caption: 実行例

    % docker run --rm hello-world

.. _ls:

:code:`images` (:code:`image ls`)
=========================================

ローカルにキャッシュされているイメージの一覧を取得します。
命令的には  :command:`docker image ls` と行きたいところですが、非常によく使うため短縮命令として(従来から使われていた)  :command:`docker images` がそのまま採用されている。

.. code-block::
    :caption: 実行例

    % docker images
    REPOSITORY               TAG               IMAGE ID       CREATED          SIZE
    testing                  latest            96155697b768   42 minutes ago   1.62GB
    densuke/atuq             latest            a39117ef9718   3 weeks ago      5.59MB
    sphinx                   test              95251b96d2bc   4 weeks ago      1.61GB
    densukest/alpine-ja      latest            30c5f3e4f257   4 weeks ago      11.3MB
    ...(以下略)

REPOSIROTY
    イメージの名前(レジストリ/名前空間/イメージ名)

TAG
    イメージに付与しているタグ(明示的について無いものはlatest)

IMAGE ID
    内部ID(:code:`--digest` できちんと出力できるがすごく長い)

CREATED
    そのイメージがいつ頃作成されたか(現在からの距離)

SIZE
    イメージの大きさ

.. note::
    イメージは階層構造で生成されており、共用できる階層は共用されます。
    よって、サイズが妙に大きくても、他のイメージと共有している部分は重複して保存されているわけでもありません。

    たとえば ubuntu:focal イメージをベースに作成したイメージAの場合、
    サイズとしては『ubuntu:focalのサイズ+A独自のサイズ』となりますが、
    同様に作った **イメージB** の場合は『ubuntu:focalのサイズ+ **B独自** のサイズ』となります。
    全体での消費量は『ubuntu:focal+A独自+B独自』という計算になります。

    .. figure:: /images/size.drawio.png
        :width: 75%

        イメージは可能なところは共有されている

.. _pull:

:code:`pull` (:code:`image pull`)
=====================================

指定したイメージを取得します。既に取得済みのイメージの場合、更新されているときに更新された階層のみ再取得を行います。

.. code-block::
    :caption: 使い方

    % docker pull イメージ名[:タグ]

イメージ名
    取得したいイメージの名前

    - 基本形は『レジストリ名/名前空間/イメージ名』
    - レジストリ名(ホスト名)は省略した場合には `Docker Hub <https://hub.docker.com/>`_ の扱い
    - 名前空間は一般的には作成したユーザ名となる

    Docker HubにおいてはDocker側が事前に認定したものに関しては省略されることがあります [#ubuntu]_

タグ
    開発側が適当に付けて区別することがあるので、必要なものを選んで渡すこと。
    指定が無い場合は自動的に :code:`latest` タグが付いているものとして扱われる(最新版)

.. code-block::
    :caption: 実行例

    # Docker Hubよりdensuke/sphinx(:latest)を取得
    # 実際に存在するイメージですが、1GB越えするので少し時間かかります
    % docker pull densuke/sphinx

    # 認証済みイメージ centos を取得

.. rubric:: 脚注

.. [#ubuntu] Ubuntuのイメージには名前空間指定が無い(:code:`ubuntu:focal` などでOK)のはこのため