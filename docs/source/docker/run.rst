.. _docker-run:

===========================================
:command:`docker run` について
===========================================

:command:`run` サブコマンドは、Dockerにおけるコンテナ起動の基本となります。
これを認識できるようになると、事前に立ち上げの準備をしておくための  :command:`docker create` にも応用できますし、
`docker compose` を使うときの基礎となります。

基本の起動
======================

基本の起動は、利用したいイメージを引数に渡すだけです。

.. code-block::
    :caption: runの基本

    # hello-worldイメージからのコンテナ生成
    % docker run hello-world

この場合、 `hello-world` 公式イメージをベースとしたコンテナが生成されますが、裏でいくつか動きがあります。

- 適当な名前のコンテナ名(形容詞-名前)が生成されます、コンテナ名は各コンテナを区別するためのものです
- イメージに設定されているコマンドが自動的にコンテナ内で呼ばれます
- 呼ばれたコマンドがコンテナ内のプロセスID(PID)1として動きます
- コンテナ内のPID 1が終了するとそのコンテナは動作を終了します

動いた・動いていたコンテナは、  :command:`docker ps` で確認できます。

.. code-block::
    :caption: 実行済みコンテナプロセスの確認

    % docker ps -a # 実行済みはそのままでは出ないため、-a(all)付きで
    CONTAINER ID   IMAGE        COMMAND   CREATED       STATUS                   PORTS NAMES
    18141b4d8925   hello-world  "/hello"  4 seconds ago Exited (0) 4 seconds ago        vigilant_sammet

CONTAINER ID
    コンテナに対し一意に割り付けられたID
IMAGE
    コンテナのために用意したイメージ名
COMMAND
    コンテナ起動時に実行されたコマンド(PID 1になる)
CREATEDとSTATUS
    生成された時間および現在の状態、時間に関しては今を基準とした『どれぐらい前』かという時間
PORTS
    ポートマッピング使用時のマップ状態(今回は未使用)
NAMES
    コンテナに割り付けられた名前(形容詞_名前)

名前は無指定なら適当に生成されるため、コンテナ実行を繰り返すとその分だけ(終了した)コンテナが増えます。

コンテナに名前を付ける
=================================

コンテナ実行時に :code:`--name NAME` を用いると、一意の名前を自分で付けることができます…
ただし同じ名前のコンテナは起動できないため、繰り返すとエラーになります。

.. code-block::
    :caption: 重複名のコンテナは作れない

    # 名前を "sample" としてコンテナを生成
    % docker run --name sample hello-world # 成功
    % docker run --name sample hello-world
    docker: Error response from daemon: Conflict. The container name "/sample" is already
    in use by container "787f052(略)". You have to remove (or rename) that container to be
    able to reuse that name.
    See 'docker run --help'.

よく使うコンテナのには名前を付けて繰り返し使うと良いと思います。

コンテナの削除
==============================

残っているコンテナは  :command:`docker rm` もしくは  :command:`docker container rm` で削除できます。
稼働中コンテナは削除できません。

.. code-block::
    :caption: sampleコンテナの削除

    % docker container ps -a | grep sample
    787f05251f2a   hello-world     "/hello"   3 minutes ago    Exited (0) 3 minutes ago sample

    # この場合、コンテナのIDかコンテナ名で削除ができる
    % docker container rm sample # もしくは 787f05251f2a
    sample
    % docker container rm sample # もう一度やると無くなってるのでエラーになる
    Error: No such container: sample
    % docker container ps -a | grep sample # 存在しないので戻りは無し
    %
