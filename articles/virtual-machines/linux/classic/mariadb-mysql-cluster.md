---
title: "Azure で MariaDB (MySQL) クラスターを実行する | Microsoft Docs"
description: "Azure の仮想マシン上に MariaDB + Galera MySQL クラスターを作成する"
services: virtual-machines-linux
documentationcenter: 
author: sabbour
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d0d21937-7aac-4222-8255-2fdc4f2ea65b
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/15/2015
ms.author: asabbour
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 53e9bf18b26338212411ea7c4f260eb308486738
ms.lasthandoff: 03/27/2017


---
# <a name="mariadb-mysql-cluster-azure-tutorial"></a>MariaDB (MySQL) クラスター: Azure チュートリアル
> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager](../../../resource-manager-deployment-model.md) とクラシックの 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイメント モデルについて説明します。 最新のデプロイでは、Azure Resource Manager を使用することをお勧めします。

> [!NOTE]
> Azure Marketplace では、MariaDB Enterprise cluster のダウンロードが追加されました。 新しいサービスでは、自動的に Azure Resource Manager 上に MariaDB Galera クラスターをデプロイします。 新しいサービスは [Azure Marketplace](https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/) から使用できます。
>
>

この記事では、[MariaDB](https://mariadb.org/en/about/) のマルチマスター [Galera](http://galeracluster.com/products/) クラスターを作成する方法を説明します。これを Azure 仮想マシン上の高可用性環境で稼働することで、MySQL の堅牢でスケーラブル、かつ信頼性の高い代替製品として実行するためです。

## <a name="architecture-overview"></a>アーキテクチャの概要
この記事では、次の手順を完了する方法について説明します。

- 3 ノード クラスターを作成します。
- OS ディスクからデータ ディスクを分離します。
- RAID 0/ストライプ化設定でデータ ディスクを作成し、IOPS を増やします。
- Azure Load Balancer を使用して、3 つのノードの負荷を分散します。
- 繰り返し作業を最小限に抑えるには、MariaDB + Galera を含む VM イメージを作成し、他のクラスター VM の作成に使用します。

![システム アーキテクチャ](./media/mariadb-mysql-cluster/Setup.png)

> [!NOTE]
> このトピックでは [Azure CLI](../../../cli-install-nodejs.md) ツールを使用するため、Azure CLI ツールをダウンロードし、指示に従って Azure サブスクリプションに接続してください。 Azure CLI で使用できるコマンドのリファレンスが必要な場合は、「[Azure CLI コマンド リファレンス](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)」をご覧ください。 また、[認証用に SSH キーを作成]し、.pem ファイルの場所をメモしておく必要があります。
>
>

## <a name="create-the-template"></a>テンプレートを作成する
### <a name="infrastructure"></a>インフラストラクチャ
1. リソースをまとめて保持するためにアフィニティ グループを作成します。

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"
2. 仮想ネットワークを作成します。

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet
3. すべてのディスクをホストするストレージ アカウントを作成します。 ストレージ アカウントの 20,000 IOPS の制限に達することを避けるため、同じストレージ アカウント上での、使用頻度の高いディスクの作成は 40 台までにしてください。 このチュートリアルでは、わかりやすくするために、この制限に達しないようにしているので、同じアカウントですべてを格納します。

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster
4. CentOS 7 仮想マシン イメージの名前を検索します。

        azure vm image list | findstr CentOS
   出力は、`5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926` のようになります。

   この名前を次の手順で使用します。
5. VM テンプレートを作成して、/path/to/key.pem を生成した .pem SSH キーを格納した場所のパスに置き換えます。

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser
6. RAID 構成に使用するために、4 x 500 GB のデータ ディスクを VM に接続します。

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd
7. mariadbhatemplate.cloudapp.net:22 で作成したテンプレート VM に SSH を使用してサインインし、秘密鍵を使用することで接続します。

### <a name="software"></a>ソフトウェア
1. ルートを取得します。

        sudo su

2. RAID サポートをインストールします。

    a. mdadm をインストールします。

              yum install mdadm

    b. EXT4 ファイル システムでの RAID 0/ストライプ構成を作成します。

              mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
              mdadm --detail --scan >> /etc/mdadm.conf
              mkfs -t ext4 /dev/md0
    c. マウント ポイントのディレクトリを作成します。

              mkdir /mnt/data
    d. 新しく作成された RAID デバイスの UUID を取得します。

              blkid | grep /dev/md0
    e. /etc/fstab を編集します。

              vi /etc/fstab
    f. デバイスを追加して再起動時に UUID を直前の **blkid** コマンドから取得した値と置き換える自動マウントを有効にします。

              UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2
    g. 新しいパーティションをマウントします。

              mount /mnt/data

3. MariaDB をインストールします。

    a. MariaDB.repo ファイルを作成します。

                vi /etc/yum.repos.d/MariaDB.repo

    b. repo ファイルを次の内容にします。

              [mariadb]
              name = MariaDB
              baseurl = http://yum.mariadb.org/10.0/centos7-amd64
              gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
              gpgcheck=1
    c. 競合を回避するために、既存の接尾語と mariadb-libs を削除します。

           yum remove postfix mariadb-libs-*
    d. MariaDB Galera インストールします。

           yum install MariaDB-Galera-server MariaDB-client galera

4. MySQL のデータ ディレクトリを、RAID ブロック デバイスに移動します。

    a. 新しい場所に現在の MySQL のディレクトリをコピーし、古いディレクトリを削除します。

           cp -avr /var/lib/mysql /mnt/data  
           rm -rf /var/lib/mysql
    b. それに応じて、新しいディレクトリに権限を設定します。

           chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/

    c. 古いディレクトリが RAID パーティションの新しい場所を指すようにシンボリック リンクを作成します。

           ln -s /mnt/data/mysql /var/lib/mysql

5. [SELinux はクラスターのオペレーションに干渉する](http://galeracluster.com/documentation-webpages/configuration.html#selinux)ので、現在のセッションに対して無効にする必要があります。 後の再起動のために `/etc/selinux/config` を編集して無効にします。

            setenforce 0

            then editing `/etc/selinux/config` to set `SELINUX=permissive`
6. MySQL の実行を検証します。

   a. MySQL を開始します。

           service mysql start
   b. MySQL のインストールをセキュリティで保護し、ルート パスワードを設定、匿名ユーザーを削除してリモート ルート ログインを無効にし、テスト データベースを削除します。

           mysql_secure_installation
   c. クラスター操作と、必要に応じて、アプリケーションのためにデータベースにユーザーを作成します。

           mysql -u root -p
           GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
           exit

   d. MySQL を停止します。

            service mysql stop
7. 構成プレースホルダーを作成します。

   a. MySQL の構成を編集して、クラスター設定のプレースホルダーを作成します。 ここでは、 **`<Variables>`** を置き換えたり、コメントを解除したりしないでください。 それらの処理は、このテンプレートから VM を作成した後で行います。

            vi /etc/my.cnf.d/server.cnf
   b. **[[galera]]** セクションを編集して空にします。

   c. **[mariadb]** セクションを編集します。

           wsrep_provider=/usr/lib64/galera/libgalera_smm.so
           binlog_format=ROW
           wsrep_sst_method=rsync
           bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
           default_storage_engine=InnoDB
           innodb_autoinc_lock_mode=2

           wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
           #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
           #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
           #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
           #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server
8. CentOS 7 で FirewallD を使用して、ファイアウォールで必要なポートを開きます。

   * MySQL: `firewall-cmd --zone=public --add-port=3306/tcp --permanent`
   * GALERA: `firewall-cmd --zone=public --add-port=4567/tcp --permanent`
   * GALERA IST: `firewall-cmd --zone=public --add-port=4568/tcp --permanent`
   * RSYNC: `firewall-cmd --zone=public --add-port=4444/tcp --permanent`
   * ファイアウォールの再読み: `firewall-cmd --reload`

9. パフォーマンスにシステムを最適化します。 詳細については、[パフォーマンス チューニング戦略に関するページ](optimize-mysql.md)をご覧ください。

   a. 再度、MySQL の構成ファイルを編集します。

            vi /etc/my.cnf.d/server.cnf
   b. **[mariadb]** セクションを編集し、次の内容を追加します。

   > [!NOTE]
   > innodb\_buffer\_pool_size を、お使いの VM のメモリの 70% にすることをお勧めします。 この例では、RAM が 3.5 GB の中程度の Azure VM の場合で、2.45 GB に設定しています。
   >
   >

           innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70 percent of physical memory.
           innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
           max_connections = 5000 # A larger value will give the server more time to recycle idled connections
           innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
           innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
           innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
           query_cache_size = 0
10. MySQL を停止し、ノード追加時のクラスターの障害を避けるために起動時の MySQL サービスの動作を無効にし、マシンのプロビジョニングを解除します。

        service mysql stop
        chkconfig mysql off
        waagent -deprovision
11. ポータルで VM をキャプチャします。 (現在、 [Azure CLI ツールの issue #1268](https://github.com/Azure/azure-xplat-cli/issues/1268) には、Azure CLI ツールでキャプチャしたイメージで、接続されているデータ ディスクがキャプチャされていないという問題が記載されています。)

    a. ポータル経由でコンピューターをシャット ダウンします。

    b. **[取り込み]** をクリックして、イメージの名前を **mariadb-galera-image** として指定します。 説明を入力して、[waagent を実行しました] をオンにします。
      
      ![仮想マシンをキャプチャする](./media/mariadb-mysql-cluster/Capture2.PNG)

## <a name="create-the-cluster"></a>クラスターを作成する
作成したテンプレートから 3 台の VM を作成してから、クラスターを構成、起動します。

1. 作成した mariadb-galera-image イメージから最初の CentOS 7 VM を作成します。次の情報を指定します。

 - 仮想ネットワーク名: mariadbvnet
 - サブネット: mariadb
 - マシンのサイズ: medium
 - クラウド サービス名: mariadbha (または mariadbha.cloudapp.net を使用してアクセスする任意の名前)
 - マシン名: mariadb1
 - ユーザー名: azureuser
 - SSH アクセス: enabled
 - SSH 証明書 .pem ファイルを渡し、生成した .pem SSH キーを格納した場所のパスで /path/to/key.pem を置き換える。

   > [!NOTE]
   > 次のコマンドは、わかりやすいように複数の行に分割しますが、それぞれ 1 つの行として入力する必要があります。
   >
   >
        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser
2. mariadbha クラウド サービスに接続することによって、あと 2 台の仮想マシンを作成します。 同じクラウド サービス内の他の VM と競合しないように、VM 名と SSH ポートはそれぞれ一意に変更します。

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
  MariaDB3 の場合:

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser
3. 次の手順で使用するために、3 台の VM のそれぞれの内部 IP アドレスを取得する必要があります。

    ![IP アドレスを取得する](./media/mariadb-mysql-cluster/IP.png)
4. SSH を使用して、3 台の VM にサインインして、それぞれの構成ファイルを編集します。

        sudo vi /etc/my.cnf.d/server.cnf

    行の先頭の **#** を削除することで、**`wsrep_cluster_name`** と **`wsrep_cluster_address`** をコメント解除します。
    また、**`wsrep_node_address`** の **`<ServerIP>`** と **`wsrep_node_name`** の **`<NodeName>`** を、それぞれ VM の IP アドレスと名前で置き換え、それらの行もコメント解除します。
5. MariaDB1 でクラスターを起動し、起動時に実行されるようにします。

        sudo service mysql bootstrap
        chkconfig mysql on
6. MariaDB2 と MariaDB3 で MySQL を起動し、起動時に実行されるようにします。

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balance-the-cluster"></a>クラスターの負荷を分散する
クラスター化された VM を作成した場合には、それらの VM を clusteravset という名前の可用性セットに追加し、それぞれの VM が異なる障害ドメインと更新ドメインに確実に配置され、Azure がすべてのマシンを同時にメンテナンスすることがないようにしています。 この構成は、Azure サービス レベル アグリーメント (SLA) によってサポートされる要件を満たしています。

ここでは、Azure Load Balancer を使用して、3 つのノードに要求を分散します。

Azure CLI を使用して、マシン上で次のコマンドを実行します。

コマンドのパラメーター構造は次のとおりです。`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

この CLI では、ロード バランサー プローブ間隔を 15 秒に設定していますが、少し長すぎます。 ポータルのいずれかの VM 配下の**エンドポイント**で、間隔を変更します。

![エンドポイントを編集する](./media/mariadb-mysql-cluster/Endpoint.PNG)

**[負荷分散セットの再構成]** を選択します。

![[負荷分散セットの再構成]](./media/mariadb-mysql-cluster/Endpoint2.PNG)

**[プローブ間隔]** を 5 秒に変更し、変更を保存します。

![プローブ間隔を変更する](./media/mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validate-the-cluster"></a>クラスターを検証する
設定作業は完了しました。 ロード バランサーにヒットし、円滑かつ効率的に 3 台の VM に要求をルーティングする `mariadbha.cloudapp.net:3306` でクラスターにアクセスできます。

お好みの MySQL クライアントを使用して接続するか、いずれかの VM から接続して、このクラスターの動作を検証します。

     mysql -u cluster -h mariadbha.cloudapp.net -p

データベースを作成し、データをいくつか追加します。

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

作成したデータベースは、次のテーブルを返します。

    +----+--------+
    | id | value  |
    +----+--------+
    |  1 | Value1 |
    |  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次のステップ
この記事では、CentOS 7 を実行している Azure の仮想マシン上に、3 ノードの MariaDB + Galera の可用性の高いクラスターを作成しました。 VM では、Azure Load Balancer で、負荷分散が行われます。

必要に応じて、[Linux 上で MySQL をクラスター化する別の方法](mysql-cluster.md)と [Azure Linux VM 上で MySQL のパフォーマンスを最適化とテストする方法](optimize-mysql.md)をご覧ください。

<!--Anchors-->
[Architecture overview]:#architecture-overview
[Creating the template]:#creating-the-template
[Creating the cluster]:#creating-the-cluster
[Load balancing the cluster]:#load-balancing-the-cluster
[Validating the cluster]:#validating-the-cluster
[Next steps]:#next-steps

<!--Image references-->

<!--Link references-->
[Galera]:http://galeracluster.com/products/
[MariaDBs]:https://mariadb.org/en/about/
[認証用に SSH キーを作成]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[issue #1268 in the Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268

