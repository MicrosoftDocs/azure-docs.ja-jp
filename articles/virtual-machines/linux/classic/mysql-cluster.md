---
title: "負荷分散セットを使用して MySQL をクラスター化する | Microsoft Docs"
description: "Azure 上のクラシック デプロイメント モデルを使用して作成された負荷分散された可用性の高い Linux MySQL クラスターをセットアップする"
services: virtual-machines-linux
documentationcenter: 
author: bureado
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 6c413a16-e9b5-4ffe-a8a3-ae67046bbdf3
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2015
ms.author: jparrel
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 4eaf86c9ac3e4dc2b51b88383626eda774cab0e9
ms.lasthandoff: 03/27/2017


---
# <a name="use-load-balanced-sets-to-clusterize-mysql-on-linux"></a>負荷分散セットを使用して Linux の MySQL をクラスター化する
> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager](../../../resource-manager-deployment-model.md) とクラシックの 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 MySQL クラスターをデプロイする必要がある場合、[Resource Manager テンプレート](https://azure.microsoft.com/documentation/templates/mysql-replication/)を使用できます。

この記事では、Linux ベースの高可用性サービスを Microsoft Azure 上にデプロイする際に使用できるさまざまな方法について詳細に説明すると共に、MySQL サーバーの高可用性についての初歩的な知識も説明します。 この記事の内容を紹介するビデオが [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL) で視聴できます。

ここでは、DRBD、Corosync、Pacemaker をベースとする、シェアード ナッシング、2 ノード、1 マスターの MySQL 高可用性ソリューションについて概説します。 同時に MySQL を実行するノードは 1 つだけです。 また、DRBD リソースに対して同時に読み書きできるノードも 1 つに制限されます。

Microsoft Azure の負荷分散セットを使用すると、ラウンド ロビン、エンドポイント検出、VIP の削除と適切な回復などの機能が提供されるため、LVS のような VIP ソリューションは必要ありません。 VIP はグローバルにルーティング可能な IPv4 アドレスで、最初にクラウド サービスを作成するときに Microsoft Azure によって割り当てられます。

他にも NBD Cluster、Percona、Galera などの MySQL 用アーキテクチャやいくつかのミドルウェア ソリューション (VM として利用できるものが [VM Depot](http://vmdepot.msopentech.com) に 1 つ以上掲載されています) が利用できる可能性があります。 これらのソリューションが、(マルチキャストまたはブロードキャストではなく) ユニキャストでのレプリケーションが可能で、共有ストレージや複数のネットワーク インターフェイスに依拠しないものであれば、Microsoft Azure 上で簡単にデプロイできます。

これらのクラスタリング アーキテクチャは、PostgreSQL や OpenLDAP などの他の製品にも同様の方法で拡張できます。 たとえば、ここで挙げたシェアード ナッシング方式の負荷分散手順は、マルチマスターの OpenLDAP でのテストに成功しています。その内容は、Microsoft の Channel 9 ブログで見ることができます。

## <a name="get-ready"></a>準備
次のリソースと機能が必要です。

  - 有効なサブスクリプションがあり、少なくとも 2 つの VM を作成できる Microsoft Azure アカウント (この例では XS を使用)
  - ネットワークとサブネット
  - アフィニティ グループ
  - 可用性セット
  - クラウド サービスと同じリージョンに VHD を作成して Linux VM に接続する機能

### <a name="tested-environment"></a>テスト環境
* Ubuntu 13.10
  * DRBD
  * MySQL サーバー
  * Corosync および Pacemaker

### <a name="affinity-group"></a>アフィニティ グループ
このソリューション用のアフィニティ グループを作成します。そのためには、Azure クラシック ポータルにサインインし、**[設定]** を選択して、アフィニティ グループを作成します。 後で作成したリソースは、このアフィニティ グループに割り当てられます。

### <a name="networks"></a>ネットワーク
ネットワークを作成し、その中にサブネットを作成します。 この例では、中に /24 のサブネットを 1 つだけ含む 10.10.10.0/24 ネットワークを使用します。

### <a name="virtual-machines"></a>仮想マシン
ギャラリーの動作保証済み Ubuntu イメージを使用して最初の Ubuntu 13.10 の VM を作成し、`hadb01` と名付けます。 このとき、hadb という名前の新しいクラウド サービスを作成します。 この名前は、リソースが追加されるときにこのサービスが持つ共有、負荷分散という性質を表します。 ポータルを使用すると、`hadb01` は簡単に作成できます。 SSH のエンドポイントは自動的に作成され、新しいネットワークが選択されます。 これで、VM の可用性セットを作成できます。

1 つ目の VM を作成した後 (厳密に言えば、クラウド サービスを作成した後)、`hadb02` という名前の 2 つ目の VM を作成します。 2 つ目の VM も、ポータルのギャラリーのイメージを使用して Ubuntu 13.10 の VM を作成します。ただし、新しいクラウド サービスを作成するのではなく、既存のクラウド サービス `hadb.cloudapp.net` を使用します。 ネットワークと可用性セットは自動的に選択されます。 SSH エンドポイントも作成されます。

2 つの VM の作成が完了したら、`hadb01` (TCP 22) と `hadb02` (Azure による自動割り当て) の SSH ポートをメモします。

### <a name="attached-storage"></a>ストレージの接続
両方の VM に新しいディスクを接続し、それぞれに 5 GB のディスクを作成します。 これらのディスクは、オペレーティング システムのメイン ディスクとして使用されている VHD コンテナーでホストされます。 ディスクを作成して接続すると、カーネルが新しいデバイスを認識するため、Linux を再起動する必要はありません。 通常、このデバイスは `/dev/sdc` です。 出力については、`dmesg` を確認してください。

それぞれの VM で、`cfdisk` (プライマリ、Linux パーティション) を使用してパーティションを作成し、新しいパーティション テーブルを書き込みます。 このパーティションにはファイル システムを作成しないでください。

## <a name="set-up-the-cluster"></a>クラスターをセットアップする
APT を使用して、両方の Ubuntu VM に Corosync、Pacemaker、DRBD をインストールします。 `apt-get` を使用してこの操作を行うには、次のコードを実行します。

    sudo apt-get install corosync pacemaker drbd8-utils.

この時点では MySQL をインストールしないでください。 Debian と Ubuntu のインストール スクリプトは、`/var/lib/mysql` の MySQL のデータ ディレクトリを初期化します。ただし、このディレクトリは DRBD のファイル システムに置き換わるため、MySQL は後でインストールする必要があります。

両方の VM が 10.10.10.0/24 サブネットのアドレスを使用していることと、相互に名前で Ping を実行できることを (`/sbin/ifconfig` を使用して) 確認します。 また、`ssh-keygen` と `ssh-copy-id` を使用すると、両方の VM がパスワードを求められることなく SSH 経由で通信できることも確認できます。

### <a name="set-up-drbd"></a>DRBD をセットアップする
基盤の `/dev/sdc1` パーティションを使用する DRBD リソースを作成して、`/dev/drbd1` リソースを作成します。このリソースは、ext3 を使用することによりフォーマット可能で、プライマリ ノードでもセカンダリ ノードでも使用できます。

1. `/etc/drbd.d/r0.res` を開いて、両方の VM に次のリソース定義をコピーします。

        resource r0 {
          on `hadb01` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.4:7789;
            meta-disk internal;
          }
          on `hadb02` {
            device  /dev/drbd1;
            disk   /dev/sdc1;
            address  10.10.10.5:7789;
            meta-disk internal;
          }
        }

2. 両方の VM で、`drbdadm` を使用してリソースを初期化します。

        sudo drbdadm -c /etc/drbd.conf role r0
        sudo drbdadm up r0

3. プライマリ VM (`hadb01`) で、DRBD リソースの所有権 (プライマリ) を強制適用します。

        sudo drbdadm primary --force r0

両方の VM で、/proc/drbd (`sudo cat /proc/drbd`) の内容をチェックする場合には、`hadb01` の `Primary/Secondary` および `hadb02` の `Secondary/Primary` がこの時点のソリューションと一致していることを確認する必要があります。 5 GB ディスクは、お客様への課金なしで 10.10.10.0/24 ネットワーク上で同期されます。

ディスクが同期されたら、`hadb01` 上にファイル システムを作成できます。 ここでは、テスト目的で ext2 を使用しましたが、次のコードを実行すると ext3 ファイル システムが作成されます。

    mkfs.ext3 /dev/drbd1

### <a name="mount-the-drbd-resource"></a>DRBD リソースをマウントする
これで、DRBD リソースを `hadb01` にマウントする準備ができました。 Debian 系の Linux は、 `/var/lib/mysql` を MySQL のデータ ディレクトリとして使用します。 まだ MySQL をインストールしていないため、このディレクトリを作成して、DRBD リソースをマウントします。 このオプションを実行するには、`hadb01` で次のコードを実行します。

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="set-up-mysql"></a>MySQL を設定します。
`hadb01` 上に MySQL をインストールします。

    sudo apt-get install mysql-server

`hadb02` の場合には、2 つの選択肢があります。 1 つは、MySQL サーバーをインストールすることです。これにより、/var/lib/mysql が作成され、新しいデータ ディレクトリが割り当てられて、その内容が削除されます。 このオプションを実行するには、`hadb02` で次のコードを実行します。

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

もう 1 つのオプションは、`hadb02` にフェールオーバーしてから、そこに MySQL サーバーをインストールすることです。 既存のインストールがあれば、インストール スクリプトはそれを認識してそのまま保持します。

`hadb01` で次のコードを実行します。

    sudo drbdadm secondary –force r0

`hadb02` で次のコードを実行します。

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

ここで、DRBD のフェールオーバーを計画していない場合、1 つ目の選択肢は簡単ですがスマートさに欠けるかもしれません。 このセットアップを行うと、次のように、MySQL データベースの操作を開始できます。 `hadb02` (または DRBD にとってアクティブな任意のサーバー) で次のコードを実行します。

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

> [!WARNING]
> この最後のステートメントは、このテーブルのルート ユーザーに対する認証を無効にします。 この部分は、製品レベルの GRANT ステートメントに置き換えて、単なる説明用として含める必要があります。

VM の外部からクエリを実行する (この記事の目的) 場合には、MySQL 用のネットワークも有効にする必要があります。 両方の VM で `/etc/mysql/my.cnf` を開き、`bind-address` に移動します。 アドレスを 127.0.0.1 から 0.0.0.0 に変更します。 このファイルを保存した後で、現在のプライマリで `sudo service mysql restart` を発行します。

### <a name="create-the-mysql-load-balanced-set"></a>MySQL 負荷分散セットを作成する
ポータルに戻って `hadb01` に移動し、**[エンドポイント]** を選択します。 エンドポイントを作成するには、ドロップダウン リストから "MySQL (TCP 3306)" を選択し、**[新しい負荷分散セットを作成します]** を選択します。 この負荷分散エンドポイントに `lb-mysql` という名前を付けます。 **[時間]** を 5 秒 (最小値) に設定します。

エンドポイントの作成後、`hadb02` に移動して **[エンドポイント]** を選択し、エンドポイントを作成します。 `lb-mysql` を選択し、ドロップダウン リストから "MySQL" を選択します。 この手順では、Azure CLI も使用できます。

これで、クラスターの手動操作に必要なものはすべて揃いました。

### <a name="test-the-load-balanced-set"></a>負荷分散セットをテストする
任意の MySQL クライアントや特定のアプリケーション (Azure Web サイトとして実行している phpMyAdmin など) を使用して、外部のマシンからテストを実行できます。 ここでは、別の Linux ボックスで MySQL のコマンド ライン ツールを使用します。

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>手動によるフェールオーバー
MySQL をシャットダウンし、DRBD のプライマリを切り替えて、MySQL を再起動することで、フェールオーバーをシミュレートできます。

このタスクを実行するには、hadb01 で次のコードを実行します。

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

hadb02 の場合:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

手動でフェールオーバーした後、リモート クエリを繰り返すことができ、これは正しく動作します。

## <a name="set-up-corosync"></a>Corosync をセットアップする
Corosync は、クラスターの基盤となるインフラストラクチャで、Pacemaker を動作させるために必要です。 ハートビート (および Ultramonkey などの他の手法) から見れば、Corosync には CRM 機能の一部しかありませんが、Pacemaker には、よりハートビートに近い機能が残されています。

Azure で Corosync を使用する場合、Microsoft Azure のネットワークがユニキャスト通信しかサポートしないのに対して、Corosync は、ユニキャストよりもブロードキャスト、ブロードキャストよりもマルチキャストを優先するという大きな制約があります。

ただし、Corosync にはユニキャスト動作モードがあります。 実際上の制約は、各ノードのノード間通信が行われないため、各ノードの IP アドレスを含む構成ファイルでノードを定義する必要があることです。 Corosync のユニキャスト用サンプル ファイルを使用し、バインド アドレス、ノード リスト、ログ ディレクトリ (Ubuntu は `/var/log/corosync` を使用しますが、サンプル ファイルは `/var/log/cluster` を使用します) を変更し、クォーラム ツールを有効にできます。

> [!NOTE]
> 両方のノードで、次の `transport: udpu` ディレクティブと手動で定義した IP アドレスを使用します。

両方のノードの `/etc/corosync/corosync.conf` で、次のコードを実行します。

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

両方の VM でこの構成ファイルをコピーし、両方のノードで Corosync を起動します。

    sudo service start corosync

サービスを開始するとすぐに、現在のリング内にクラスターが構築される必要があります。また、クォーラムも構成される必要があります。 この機能をチェックするには、ログを確認するか、次のコードを実行します。

    sudo corosync-quorumtool –l

次の図のような出力が表示されます。

![corosync-quorumtool -l sample output](./media/mysql-cluster/image001.png)

## <a name="set-up-pacemaker"></a>Pacemaker をセットアップする
Pacemaker は、クラスターを利用してリソースを監視し、プライマリがダウンしたときにリソースをセカンダリに切り替える時期を定義します。 リソースは、利用できるスクリプト セット、LSB (init 風) スクリプトなどの選択肢から定義できます。

Pacemaker が DRBD リソース、マウント ポイント、MySQL サービスを "所有" するようにします。 プライマリに不具合が発生したとき、Pacemaker によって DRBD の有効化と無効化、マウントとマウント解除、MySQL の起動と停止を正しい順序で行うことができれば、セットアップは完了したことになります。

初めて Pacemaker をインストールする場合には、次のような簡単な構成にします。

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

1. `sudo crm configure show` を実行して、構成を確認します。
2. 次のリソースを含むファイル (`/tmp/cluster.conf` など) を作成します。

        primitive drbd_mysql ocf:linbit:drbd \
              params drbd_resource="r0" \
              op monitor interval="29s" role="Master" \
              op monitor interval="31s" role="Slave"

        ms ms_drbd_mysql drbd_mysql \
              meta master-max="1" master-node-max="1" \
                clone-max="2" clone-node-max="1" \
                notify="true"

        primitive fs_mysql ocf:heartbeat:Filesystem \
              params device="/dev/drbd/by-res/r0" \
              directory="/var/lib/mysql" fstype="ext3"

        primitive mysqld lsb:mysql

        group mysql fs_mysql mysqld

        colocation mysql_on_drbd \
               inf: mysql ms_drbd_mysql:Master

        order mysql_after_drbd \
               inf: ms_drbd_mysql:promote mysql:start

        property stonith-enabled=false

        property no-quorum-policy=ignore

3. このファイルを構成に読み込みます。 この操作は、1 ノードのみで行う必要があります。

        sudo crm configure
          load update /tmp/cluster.conf
          commit
          exit

4. 両方のノードで、ブート時に Pacemaker が起動することを確認します。

        sudo update-rc.d pacemaker defaults

5. `sudo crm_mon –L` を使用して、ノードの 1 つがクラスターのマスターになり、すべてのリソースを実行していることを確認します。 mount と ps を使用して、リソースが実行中であることを確認できます。

次のスクリーンショットは、1 つのノードが停止した状態の `crm_mon` を示しています (Ctrl + C キーを選択すると終了)。

![crm_mon node stopped](./media/mysql-cluster/image002.png)

次のスクリーンショットは両方のノードを示しており、1 つがマスター、もう 1 つがスレーブとなっています。

![crm_mon operational master/slave](./media/mysql-cluster/image003.png)

## <a name="testing"></a>テスト
これで、自動フェールオーバー シミュレーションの準備ができました。 このシミュレーションを実行する場合、ソフト方式とハード方式の 2 つの方法があります。

ソフト方式では、クラスターのシャットダウン機能 (``crm_standby -U `uname -n` -v on``) を利用します。 マスターでこの方式を使用すると、スレーブが引き継ぎます。 この機能は、必ずオフに戻してください。 そうしないと、crm_mon は、1 つのノードがスタンバイ中であると示します。

ハード方式では、ポータル経由でプライマリ VM (hadb01) をシャットダウンするか、この VM の実行レベル (停止、シャットダウンなど) を変更することでシャットダウンします。 これにより、マスターがダウンしているという通知が Corosync と Pacemaker に送られます。 このテストはメンテナンス時に有用ですが、VM をフリーズさせて同じ状況を強制的に作り出すこともできます。

## <a name="stonith"></a>STONITH
物理デバイスを制御する STONITH スクリプトの代わりに、Azure CLI 経由で VM のシャットダウンを発行することができます。 ベースとして `/usr/lib/stonith/plugins/external/ssh` を使用し、クラスターの構成で STONITH を有効にできます。 Azure CLI をグローバルにインストールし、発行設定とプロファイルをクラスターのユーザー用に読み込む必要があります。

このリソースのサンプル コードは [GitHub](https://github.com/bureado/aztonith) で入手できます。 `sudo crm configure` に次のコードを追加することにより、クラスターの構成を変更します。

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

> [!NOTE]
> このスクリプトは、up/down のチェックは実行しません。 元の SSH リソースには、15 の ping チェックがありますが、Azure VM の復旧時間はさらに大きな可変性を持ちます。

## <a name="limitations"></a>制限事項
次の制限事項が適用されます。

* ノードをシャットダウンする場合、Pacemaker で DRBD をリソースとして管理する linbit DRBD リソース スクリプトは、ノードがスタンバイを始めたばかりでも、`drbdadm down` を使用します。 マスターへの書き込み中はスレーブは DRBD リソースと同期しないため、このような動作は適切ではありません。 マスターのフェールオーバーが正しく行われなかった場合、スレーブは古いファイル システムの状態を引き継ぐ可能性があります。 こうした状況は、次の 2 つの方法により解決する可能性があります。
  * ローカルの (クラスター化されていない) ウォッチドッグを介してすべてのクラスター ノードに `drbdadm up r0` を強制適用する。
  * linbit DRBD スクリプトを編集し、`down` が `/usr/lib/ocf/resource.d/linbit/drbd` で呼び出されないようにする。
* ロード バランサーの応答には少なくとも 5 秒必要なため、アプリケーションはクラスター対応であると共に、タイムアウトに寛容である必要があります。 アプリケーション内キュー、クエリ ミドルウェアなど、他のアーキテクチャが役立つ可能性もあります。
* 管理しやすいペースで書き込みが実行されるためには MySQL の調整が必要です。また、メモリ損失を最小化するため、できるだけ頻繁にキャッシュをディスクにフラッシュします。
* DRBD はデバイスをリプリケートするためのメカニズムとして、仮想スイッチで VM を相互に接続しているため、書き込み性能は、仮想スイッチでの接続状態に左右されます。

