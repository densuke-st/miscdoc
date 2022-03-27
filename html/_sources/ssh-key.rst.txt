.. _ssh-key:

公開鍵ログイン
##############


TL;DR
*****

.. code-block::
    :caption: Windows側

    PS> ssh-keygen
    # 保存場所の確認(パスが出る)は標準で良い
    # 秘密鍵のパスフレーズは忘れない範囲で適当に長い文字列で ※超重要
    PS> Get-Content ~/.ssh/id_rsa.pub | clip # 出力される公開鍵をクリップボードにコピーしておく

.. code-block::
    :caption: Linux(VM)側

    $ mkdir -m 0755 -pv ~/.ssh
    $ echo "<Win側でクリップボードに入れた公開鍵文字列をペースト>" > ~/.ssh/authorized_keys
    $ chmod 0600 ~/.ssh/authorized_keys

これでWindowsのターミナル側からsshのログイン時にパスワードではなくパスフレーズ(Passphrase)を聞いてくれば成功、秘密鍵に設定したパスフレーズでログインすれば良い。

sshの公開鍵ログインとは
***********************

sshでは、ログイン時にパスワードを(暗号回線とはいえ)リモートサーバーに投げない仕組みとして、公開鍵によるログインをサポートしています。

.. note::

    実際には、プラグインやLinux側のPAM(Plugable Authentication Module)を組み合わせることで、その他の認証方法をサポートします(スマートカードやGoogle2要素認証とか)

公開鍵は既に学んでいるとおり、2つの鍵を使って情報を暗号化・複合化するメカニズムを提供するアルゴリズムです。

秘密鍵(Private Key)
    外部に晒してはいけない隠すべき鍵、この鍵で暗号化すると暗号鍵自身では復号できない。
    でもペアとなっている公開鍵で暗号化した暗号は復号できる。
公開鍵(Public Key)
    外部に公開してかまわない鍵、この鍵で暗号化すると、公開鍵自身では復号できない [#pub]_ 。
    ペアとなっている秘密鍵でのみ復号できる。

.. figure:: /images/ssh-key.drawio.png
    :width: 50%

    ssh公開鍵と秘密鍵の関係図

公開鍵ベースのログインにする場合、チャレンジコードをというランダムな情報をログイン先であるサーバーが生成し、
ログインしようとしているユーザーの公開鍵を用いて暗号化します。
暗号化されたチャレンジコードを受け取ったクライアント側は、対となる秘密鍵を用いてチャレンジコードを復号化して取得します。
コードを暗号回線内で送信し、認証の代わりとして利用します。
万一途中でのぞき見されたとしても、チャレンジコードは使い捨てのため、再利用はできません(リプレイ攻撃対策)。

.. figure:: /images/ssh-key-auth.drawio.png
    :width: 50%

    公開鍵ベースのログイン概念図

鍵ペアの生成
************

鍵の生成は、 :command:`ssh` のツールとして移用されている :command:`ssh-keygen` を使います。

.. code-block::
    :caption: ssh公開鍵ペアの生成(:command:`ssh-keygen`)

    PS> ssh-keygen
    Generating public/private rsa key pair.
    # ↓ 鍵ファイルの作成パスの確認、通常はこのままでOK
    Enter file in which to save the key (C:\Users\densuke/.ssh/id_rsa):
    # 既に鍵を作っている場合は上書きの確認が出ます
    # ↓ パスフレーズの設定、セキュリティ上重要なので、長い単語でパスフレーズを構成しておくこと
    Enter passphrase (empty for no passphrase): (ここの入力は画面に出ません)
    Enter passphrase (empty for no passphrase): (再入力してチェック)

    # ↓ 作成に成功すると鍵の指紋と鍵から生成されるランダムアートが出力されます
    The key fingerprint is:
    SHA256:yNDqhUFcakAEKOUavMs+ha8UEVmr09jZ7KO8sZD4Zqg densuke@example
    The key's randomart image is:
    +---[RSA 3072]----+
    |o*Bo...          |
    |+o.ooo           |
    |oo..= .          |
    | oB.+* .         |
    |.*.+oo+ S        |
    |o.=o..           |
    |.Bo..o           |
    |+o=.+ .          |
    |E=+=.            |
    +----[SHA256]-----+

- 鍵の指紋(fingerprint)は鍵の一致性を確認するためのダイジェストです、同一鍵ペアでないと同じ文字列になりません
- ランダムアートは鍵から生成されるアスキーアート(AA)で、1ビットでも異なるとまったく違うものとなります、ぱっと見で比較するために使えます。

標準的な暗号化では、RSA方式が採用されています [#KL]_ 。
鍵のファイルは :file:`id_rsa` が **秘密鍵** で、 :file:`id_rsa.pub` が **公開鍵** になっています。
ファイルのベース名(:file:`id_rsa`)が鍵ペアでは一致するように生成されます。

もし鍵ファイルが既に存在する場合は上書きの危険があるため注意が出ます。

.. code-block::
    :caption: 既に鍵ファイルがある場合の注意喚起

    Generating public/private rsa key pair.
    Enter file in which to save the key (C:\Users\densuke/.ssh/id_rsa):
    C:\Users\densuke/.ssh/id_rsa already exists.
    Overwrite (y/n)?  (キャンセルするべき)

この場合は、オプション :code:`-f 出力ファイル名` で鍵のベース名を変更するといいでしょう。

.. code-block::
    :caption: 鍵ファイル名sampleで出力する例

    PS> ssh-keygen -f .ssh/sample # ホームディレクトリ直下の :file:`.ssh` ディレクトリ内に作るのが基本

なお、WindowsのPowerShellではチルダ展開(`~` をホームディレクトリに展開する操作)が中途半端に行われない残念仕様のため、
カレントディレクトリがホームになっていることを前提に相対パスで書くのが短くて済みそうです [#home]_ 。

鍵の登録
********

作成した鍵のうち、公開鍵をLinux(ログインしたいホスト)に設定します。
ここでは授業で使うVMにしてみましょう。

公開鍵の準備
============

公開鍵ファイルを出力し、クリップボードに入れておきます。
地味なコマンドですが、ストリームの内容をクリップボードにいれる :command:`clip` が効きます。

.. code-block::
    :caption: ssh公開鍵をクリップボードに入れる

    # 内容の確認、鍵ファイルは必ず公開鍵ファイル(.pub)を指定すること
    PS> get-content ~/.ssh/id_rsa.pub
    ssh-rsa AAAA...(中略)...3QdZsY6f2Sk= densuke@example

    # 出力を確認したら、パイプ渡しでclipコマンドに送り、クリップボードに入れる
    PS> get-content ~/.ssh/id_rsa.pub | clip

公開鍵の登録
============

続いてLinux側で公開鍵ファイルを用意し、登録します。

.. code-block::
    :caption: 公開鍵の登録

    # ssh設定用のディレクトリを(無ければ)作る、
    # 他人がアクセスできないアクセス権にしないと無視されるので注意
    $ mkdir -m 0700 -pv ~/.ssh
    # 鍵を登録する、途中でクリップボードに入れた鍵を貼り付ける操作が
    # 入るので操作に注意、あと手入力の場合はファイル名ミスタイプに注意
    $ echo "ここにクリップボードから公開鍵を貼り付け" > ~/.ssh/authorized_keys
    $ chmod 600 ~/.ssh/authorized_keys # こちらも他人のアクセスができないようにする
    $ exit

接続の確認
**********

ログインを試みます。

以下の出力は授業用VMに従来の方法(パスワードログイン)の例です。

.. code-block::
    :caption: 従来のパスワードベースログインの例
    :emphasize-lines: 3

    PS /Users/densuke> ssh sl
    Warning: Permanently added '[localhost]:2022' (ED25519) to the list of known hosts.
    linux@localhost's password:
    Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-104-generic x86_64)

    * Documentation:  https://help.ubuntu.com
    * Management:     https://landscape.canonical.com
    * Support:        https://ubuntu.com/advantage
    Last login: Wed Mar 16 01:07:41 2022 from 10.0.2.2

パスワードを聞かれています。

続いて公開鍵ベースに切り替えたときです。サーバー側公開鍵を発見した場合に自動的に切り替わります。

.. code-block::
    :caption: 公開鍵を検出して公開鍵ベースログインに切り替わった例
    :emphasize-lines: 3

    PS /Users/densuke> ssh sl
    Warning: Permanently added '[localhost]:2022' (ED25519) to the list of known hosts.
    Enter passphrase for key '/Users/densuke/.ssh/id_rsa':
    Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-104-generic x86_64)

    * Documentation:  https://help.ubuntu.com
    * Management:     https://landscape.canonical.com
    * Support:        https://ubuntu.com/advantage
    Last login: Mon Mar 28 05:33:13 2022 from 10.0.2.2

少しわかりにくいですが、秘密鍵ファイルに対するパスフレーズを求めてきています(Enter passphrase)。

おまけ: 秘密鍵パスフレーズの記憶
********************************

Windows10/11では、秘密鍵のパスフレーズは通常は都度入力が必要ですが、パスフレーズを記憶して入力を簡略化できるサービスが用意されています。
この記憶については、 :command:`ssh-add` コマンドを使って操作しますが、記憶先となるエージェントサービスが動いていないと話になりません。

.. code-block::
    :caption: ssh-agentサービスの確認
    :emphasize-lines: 5

    PS> Get-Service -Name "*ssh*"

    Status   Name               DisplayName
    ------   ----               -----------
    Stopped  ssh-agent          OpenSSH Authentication Agent
    Running  sshd               OpenSSH SSH Server

5行目に出ている "ssh-agent" サービス(OpenSSH Authentication Agent)がそれです。
ただし初期状態では止まっている(Stopped)ので、有効化しておきます。

.. code-block::
    :caption: ssh-agentサービスの有効化(管理者ターミナル上で操作)

    # -StartupType Automatic → 起動時に自動起動
    # -Status Running → 状態を起動(中)にする
    PS>  Set-Service -Name ssh-agent -StartupType Automatic -Status Running

これで動き出しました。 :command:`ssh-add` で鍵操作をしてみます。

.. code-block::
    :caption: 鍵パスフレーズの記憶

    PS> ssh-add
    Enter passphrase for C:\Users\densuke/.ssh/id_rsa:
    Identity added: C:\Users\densuke/.ssh/id_rsa (densuke@nezumi)

標準的な鍵ファイル名は検出しますが、見つけられない場合は、引数に鍵ファイルのパスを渡せば可能です。
これでパスフレーズ入力を省略できます。

.. code-block::
    :caption: パスフレーズ省略ログイン(成功)

    PS> ssh sl
    Warning: Permanently added '[localhost]:2022' (ED25519) to the list of known hosts.
    Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-104-generic x86_64)

    * Documentation:  https://help.ubuntu.com
    * Management:     https://landscape.canonical.com
    * Support:        https://ubuntu.com/advantage
    Last login: Mon Mar 28 05:35:41 2022 from 10.0.2.2

と、パスフレーズ入力を省略してのログインが可能になりました。
これはvscodeでのリモート開発をするときなどにバックエンドでログインを自動化させるためにも使えるものとなります。


.. rubric::
.. [#pub] これができてしまうと、公開鍵自身が端なり共有鍵になってしまってます。
.. [#KL] 鍵長はその時により異なりますが、現状2048ビット長以上なら実用的な問題はありません。
.. [#home] 敢えて書くなら :code:`${HOME}/.ssh` といった具合にするとなにげに動きます、 :command:`ssh-keygen -f` でも使えます。