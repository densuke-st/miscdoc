.. _docker-start:

=========================================================
Dockerが動いているかのチェック
=========================================================

Dockerが動いているかを見たければ、Dockerコマンドを叩いてみれば良い。

.. code-block::
    :caption: Dockerの動作状態チェック

    % docker info

:command:`info` サブコマンドにより、現状の動作状態を確認できる。
Dockerのシステムが動いていなければ、接続できない旨が出る。

うまくいけば、諸々の情報が出力される。


.. code-block::
    :caption: 接続できたときの出力例(macOS)

    Client:
     Context:    default
     Debug Mode: false
     Plugins:
      buildx: Docker Buildx (Docker Inc., v0.8.0)
      compose: Docker Compose (Docker Inc., v2.3.3)
      scan: Docker Scan (Docker Inc., v0.17.0)

    Server:
     Containers: 12
      Running: 2
      Paused: 0
      Stopped: 10
     Images: 40
     # ↓ Dockerのサーバー側バージョン
     Server Version: 20.10.13
     Storage Driver: overlay2
      Backing Filesystem: extfs
      Supports d_type: true
      Native Overlay Diff: true
      userxattr: false
     Logging Driver: json-file
     Cgroup Driver: cgroupfs
     Cgroup Version: 2
     Plugins:
      Volume: local
      Network: bridge host ipvlan macvlan null overlay
      Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
     Swarm: inactive
     Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
     Default Runtime: runc
     Init Binary: docker-init
     containerd version: 2a1d4dbdb2a1030dc5b01e96fb110a9d9f150ecc
     runc version: v1.0.3-0-gf46b6ba
     init version: de40ad0
     Security Options:
      seccomp
       Profile: default
      cgroupns
     # ↓ VM内のLinuxカーネルバージョン
     Kernel Version: 5.10.104-linuxkit
     Operating System: Docker Desktop
     OSType: linux
     Architecture: x86_64
     CPUs: 4
     Total Memory: 7.774GiB
     Name: docker-desktop
     ID: FXKT:GAM5:QM5H:X5DF:HC72:RJSP:DGSI:YMBZ:MIMG:LUFM:6N75:UR76
     Docker Root Dir: /var/lib/docker
     Debug Mode: false
     HTTP Proxy: http.docker.internal:3128
     HTTPS Proxy: http.docker.internal:3128
     No Proxy: hubproxy.docker.internal
     Registry: https://index.docker.io/v1/
     Labels:
     Experimental: false
     Insecure Registries:
      hubproxy.docker.internal:5000
      127.0.0.0/8
     Live Restore Enabled: false

========================================
実際に走らせて確認する
========================================

Dockerに接続できていることが確認できたら、テスト用のイメージとコンテナを生成すれば良い。
お約束の **Hello World** が存在するので利用すれば良い。

.. code-block::
    :caption: Docker的"Hello World"

    % docker run --rm hello-world

    Hello from Docker!
    This message shows that your installation appears to be working correctly.

    To generate this message, Docker took the following steps:
     1. The Docker client contacted the Docker daemon.
     2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
        (amd64)
     3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
     4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.

    To try something more ambitious, you can run an Ubuntu container with:
     $ docker run -it ubuntu bash

    Share images, automate workflows, and more with a free Docker ID:
     https://hub.docker.com/

    For more examples and ideas, visit:
     https://docs.docker.com/get-started/

この操作により、以下の点が正常に機能していることが確認できます。

- Dockerのシステムに接続して指示が送れている
- Docker Hubと接続して **イメージ** を取得できている
- イメージから **コンテナ** が生成できている
- コンテナが実行できている