---
title: Azure の Linux 向け SAP BusinessObjects BI プラットフォームのデプロイ | Microsoft Docs
description: Azure の Linux 向け SAP BusinessObjects BI プラットフォームのデプロイと構成
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: b94e1f82409da3329eb6d978fa2ae0222928cd97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505938"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Azure の Linux 向け SAP BusinessObjects BI プラットフォーム デプロイ ガイド

この記事では、Azure 上に Linux 向け SAP BOBI プラットフォームをデプロイする戦略について説明します。 この例には、Premium SSD マネージド ディスクをインストール ディレクトリとする 2 つの仮想マシンが構成されています。 CMS データベースには Azure Database for MySQL が使用され、ファイル リポジトリ サーバーの Azure NetApp Files は両方のサーバーで共有されます。 既定の Tomcat Java Web アプリケーションと BI プラットフォーム アプリケーションは、両方の仮想マシンに一緒にインストールされます。 ユーザー要求の負荷を分散するために、ネイティブの TLS/SSL オフロード機能を備えた Application Gateway が使用されます。

この種類のアーキテクチャは、小規模なデプロイまたは非運用環境で効果的です。 運用環境または大規模なデプロイの場合は、Web アプリケーション用に個別のホストを使用できます。また、複数の BOBI アプリケーション ホストを使用して、サーバーでより多くの情報を処理できるようにすることもできます。

![Azure での Linux 向け SAP BOBI のデプロイ](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

この例では、次の製品バージョンとファイル システム レイアウトが使用されています。

- SAP BusinessObjects プラットフォーム 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (バージョン: 8.0.15)
- MySQL C API コネクタ - libmysqlclient (バージョン: 6.1.11)

| ファイル システム        | 説明                                                                                                               | サイズ (GB)             | 所有者  | グループ  | ストレージ                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | SAP BOBI インスタンス、既定の Tomcat Web アプリケーション、およびデータベース ドライバー (必要な場合) をインストールするためのファイル システム | SAP のサイズ設定のガイドライン | bl1adm | sapsys | マネージド Premium ディスク - SSD |
| /usr/sap/frsinput  | マウント ディレクトリは、入力ファイル リポジトリ ディレクトリとして使用されるすべての BOBI ホスト間の共有ファイル用です  | ビジネス ニーズ         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | マウント ディレクトリは、出力ファイル リポジトリ ディレクトリとして使用されるすべての BOBI ホスト間の共有ファイル用です | ビジネス ニーズ         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Azure portal を使用した Linux 仮想マシンのデプロイ

このセクションでは、Linux オペレーティング システム (OS) イメージを使用して SAP BOBI プラットフォーム用の仮想マシン (VM) を 2 つ作成します。 仮想マシンを作成する手順の概要は次のとおりです。

1. [リソース グループ](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)を作成します。

2. [仮想ネットワーク](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成します。

   - SAP BI プラットフォームのデプロイの場合、すべての Azure サービス用に単一のサブネットを使用しないでください。 SAP BI プラットフォームのアーキテクチャに基づいて、複数のサブネットを作成する必要があります。 このデプロイの場合、アプリケーション サブネット、ファイル リポジトリ ストア サブネット、および Application Gateway サブネットという 3 つのサブネットを作成します。
   - Azure 上で Application Gateway と Azure NetApp Files は常に別のサブネットに存在する必要があります。 詳細については、[Azure Application Gateway](../../../application-gateway/configuration-overview.md) および [Azure NetApp Files のネットワーク計画のガイドライン](../../../azure-netapp-files/azure-netapp-files-network-topologies.md)の記事を参照してください。

3. 可用性セットを作成します。

   - 複数インスタンスのデプロイで各層の冗長性を実現するには、可用性セット内の各層に仮想マシンを配置します。 アーキテクチャに応じて、各レベルの可用性セットを分離してください。

4. 仮想マシン 1 **(azusbosl1)** を作成します。

   - カスタム イメージを使用することも、Azure Marketplace からイメージを選択することもできます。 必要に応じて「[Azure Marketplace から SAP 用 VM をデプロイする](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap)」または「[SAP のカスタム イメージを使用して VM をデプロイする](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap)」を参照してください。

5. 仮想マシン 2 **(azusbosl2)** を作成します。
6. Premium SSD ディスクを 1 つ追加します。 それは SAP BOBI のインストール ディレクトリとして使用されます。

## <a name="provision-azure-netapp-files"></a>Azure NetApp Files のプロビジョニング

Azure NetApp Files の設定を続行する前に、Azure [NetApp Files のドキュメント](../../../azure-netapp-files/azure-netapp-files-introduction.md)の内容をよく理解しておいてください。

Azure NetApp Files はいくつかの [Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)で利用できます。 選択した Azure リージョンで Azure NetApp Files が提供されているかどうかを確認してください。

[Azure NetApp Files を利用できる Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)に関するページを参照して、リージョンごとの Azure NetApp Files の利用可能性を確認してください。

Azure NetApp Files をデプロイする前に、「[Azure NetApp Files に登録する](../../../azure-netapp-files/azure-netapp-files-register.md)」の手順に従って、Azure NetApp Files へのオンボードを要求してください。

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files リソースのデプロイ

以下の手順では、お使いの [Azure 仮想ネットワーク](../../../virtual-network/virtual-networks-overview.md)が既にデプロイされていることを前提としています。 Azure NetApp Files のリソースと、そのリソースがマウントされる VM は、同じ Azure 仮想ネットワーク内またはピアリングされた Azure 仮想ネットワーク内にデプロイする必要があります。

1. リソースをまだデプロイしていない場合は、[Azure NetApp Files へのオンボード](../../../azure-netapp-files/azure-netapp-files-register.md)を要求してください。

2. 「[NetApp アカウントを作成する](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)」の手順に従って、選択した Azure リージョンに NetApp アカウントを作成します。

3. [Azure NetApp Files の容量プールの設定](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)に関するページの手順に従って、Azure NetApp Files の容量プールを設定します。

   - この記事で示されている SAP BI プラットフォーム アーキテクチャには、*Premium* サービス レベルで 1 つの Azure NetApp Files 容量プールが使用されています。 Azure の SAP BI ファイル リポジトリ サーバーには、Azure NetApp Files *Premium* または *Ultra* [サービス レベル](../../../azure-netapp-files/azure-netapp-files-service-levels.md)を使用することをお勧めします。

4. 「[サブネットを Azure NetApp Files に委任する](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)」の手順に従って、サブネットを Azure NetApp Files に委任します。

5. 「[Azure NetApp Files の NFS ボリュームを作成する](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)」の手順に従って、Azure NetApp Files のボリュームをデプロイします。

   ANF ボリュームは NFSv3 および NFSv4.1 としてデプロイできます (両方のプロトコルが SAP BOBI プラットフォームでサポートされているため)。 それぞれの Azure NetApp Files サブネット内にボリュームをデプロイします。 Azure NetApp ボリュームの IP アドレスは、自動的に割り当てられます。

Azure NetApp Files のリソースと Azure VM は、同じ Azure 仮想ネットワーク内またはピアリングされた Azure 仮想ネットワーク内に配置する必要があることに注意してください。 たとえば、azusbobi-frsinput、azusbobi-frsoutput がボリューム名で、nfs://10.31.2.4/azusbobi-frsinput、nfs://10.31.2.4/azusbobi-frsoutput が Azure NetApp Files ボリュームのファイル パスです。

- ボリューム azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- ボリューム azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>重要な考慮事項

SAP BOBI プラットフォーム ファイル リポジトリ サーバー用の Azure NetApp Files を作成するときは、次の考慮事項にご注意ください。

1. 最小容量のプールは 4 テビバイト (TiB) です。
2. 最小ボリューム サイズは 100 ギビバイト (GiB) です。
3. Azure NetApp Files と、Azure NetApp Files のボリュームがマウントされるすべての仮想マシンは、同じ Azure 仮想ネットワーク内、または同じリージョン内の[ピアリングされた仮想ネットワーク](../../../virtual-network/virtual-network-peering-overview.md)内に存在する必要があります。 同じリージョン内の VNET ピアリング経由での Azure NetApp Files のアクセスが、サポートされるようになっています。 グローバル ピアリング経由での Azure NetApp Files のアクセスは、まだサポートされていません。
4. 選択した仮想ネットワークには、Azure NetApp Files に委任されているサブネットがある必要があります。
5. Azure NetApp Files の[エクスポート ポリシー](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)では、ユーザーが制御できるのは、許可されたクライアントと、アクセスの種類 (読み取りと書き込み、読み取り専用など) です。
6. Azure NetApp Files 機能は、ゾーンにはまだ対応していません。 現在、その機能は、Azure リージョン内のすべての可用性ゾーンにはデプロイされていません。 Azure リージョンによっては、待ち時間が発生する可能性があることに注意してください。
7. Azure NetApp Files ボリュームは、NFSv3 または NFSv4.1 ボリュームとしてデプロイできます。 両方のプロトコルが SAP BI プラットフォーム アプリケーション用にサポートされています。

## <a name="configure-file-systems-on-linux-servers"></a>Linux サーバーでのファイル システムの構成

このセクションの手順では、次のプレフィックスを使用します。

**[A]** :この手順はすべてのホストに適用されます

### <a name="format-and-mount-sap-file-system"></a>SAP ファイル システムのフォーマットとマウント

1. **[A]** すべてのアタッチされたディスクを一覧表示します

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** /usr/sap のブロック デバイスをフォーマットします

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** マウント ディレクトリを作成します

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** ブロック デバイスの UUID を取得します

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** /etc/fstab にファイル システムのマウント エントリを維持します

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** ファイル システムをマウントします

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Azure NetApp Files ボリュームのマウント

1. **[A]** マウント ディレクトリを作成します

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** NFSv4.1 のマウントをサポートするようにクライアント OS を構成します **(NFSv4.1 を使用している場合のみ適用)**

   NFSv4.1 プロトコルの Azure NetApp Files ボリュームを使用している場合は、Azure NetApp Files NFSv4.1 ボリュームをマウントする必要があるすべての VM で次の構成を実行します。

   **NFS ドメイン設定の確認**

   ドメインが既定の Azure NetApp Files ドメイン (つまり、**defaultv4iddomain.com**) として構成され、マッピングが **nobody** に設定されていることを確認します。

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Azure NetApp Files の既定のドメイン構成 (**defaultv4iddomain.com**) と一致するように、VM 上の /etc/idmapd.conf に NFS ドメインを設定していることを確認します。 NFS クライアント (つまり、VM) と NFS サーバー (つまり、Azure NetApp 構成) のドメイン構成が一致しない場合、VM にマウントされている Azure NetApp ボリューム上のファイルのアクセス許可は nobody と表示されます。

   `nfs4_disable_idmapping` を確認します。 これは、**Y** に設定されている必要があります。`nfs4_disable_idmapping` が配置されるディレクトリ構造を作成するには、mount コマンドを実行します。 アクセスがカーネル/ドライバー用に予約されるため、/sys/modules の下に手動でディレクトリを作成することはできなくなります。

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** マウント エントリを追加します

   NFSv3 を使用する場合

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   NFSv4.1 を使用する場合

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** NFS ボリュームをマウントします

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>CMS データベースの構成 - Azure Database for MySQL

このセクションでは、Azure portal を使用して Azure Database for MySQL をプロビジョニングする方法について詳しく説明します。 また、SAP BOBI プラットフォーム用の CMS および監査データベースを作成する方法と、データベースにアクセスするためのユーザー アカウントについても説明します。

このガイドラインは、Azure DB for MySQL を使用している場合にのみ適用されます。 その他のデータベースの場合、手順については、SAP またはデータベース固有のドキュメントを参照してください。

### <a name="create-an-azure-database-for-mysql"></a>MySQL 用 Azure データベースの作成

Azure portal にサインインし、こちらの [Azure Database for MySQL のクイックスタート ガイド](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)に記載されている手順に従ってください。 Azure Database for MySQL のプロビジョニング時に注意する点がいくつかあります。

1. Azure Database for MySQL には、SAP BI プラットフォーム アプリケーション サーバーが実行されているのと同じリージョンを選択します。

2. [SAP BI の製品出荷マトリックス (PAM)](https://support.sap.com/pam) に基づいて、お使いの SAP BOBI バージョンに固有のサポートされている DB バージョンを選択します。 SAP PAM の MySQL AB に対して指定されているのと同じ互換性ガイドラインに従います。

3. [コンピューティングとストレージ] で、 **[サーバーの構成]** を選択し、出力のサイズに応じて適切な価格レベルを選択します。

4. **[ストレージの自動拡張]** が既定で有効になります。 [ストレージ](../../../mysql/concepts-pricing-tiers.md#storage)はスケールアップのみ可能で、スケールダウンはできないことにご注意ください。

5. **[バックアップの保有期間]** は既定で 7 日間ですが、最大 35 日間まで [必要に応じて構成](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)できます。

6. Azure Database for MySQL のバックアップは、既定ではローカル冗長です。そのため、geo 冗長ストレージにサーバーのバックアップが必要な場合は、 **[バックアップ冗長オプション]** から **[地理冗長]** を選択します。

> [!NOTE]
> サーバー作成後に [[バックアップ冗長オプション]](../../../mysql/concepts-backup.md#backup-redundancy-options) を変更することはサポートされていません。

### <a name="configure-connection-security"></a>接続のセキュリティを構成する

既定では、作成されたサーバーはファイアウォールで保護され、パブリックにアクセスすることはできません。 SAP BI プラットフォーム アプリケーション サーバーが実行されている仮想ネットワークへのアクセスを可能にするには、次の手順に従います。  

1. Azure portal で対象のサーバー リソースに移動し、そのサーバー リソースの左側のメニューから **[接続のセキュリティ]** を選択します。
2. **[Azure サービスへのアクセスを許可]** には **[はい]** を選択します。
3. [VNET ルール] で、 **[既存の仮想ネットワークを追加]** を選択します。 SAP BI プラットフォーム アプリケーション サーバーの仮想ネットワークとサブネットを選択します。 また、[MySQL Workbench](../../../mysql/connect-workbench.md) を Azure Database for MySQL に接続できるジャンプ ボックスまたはその他のサーバーへのアクセスを可能にする必要もあります。 MySQL Workbench を使用して CMS および監査データベースを作成します。
4. 仮想ネットワークが追加されたら、 **[保存]** を選択します。

### <a name="create-cms-and-audit-database"></a>CMS および監査データベースの作成

1. [MySQL の Web サイト](https://dev.mysql.com/downloads/workbench/)から MySQL Workbench をダウンロードしてインストールします。 MySQL Workbench は、必ず Azure Database for MySQL にアクセスできるサーバーにインストールします。

2. MySQL Workbench を使用してサーバーに接続します。 こちらの[記事](../../../mysql/connect-workbench.md#get-connection-information)に記載されている手順に従います。 接続テストが成功すると、次のメッセージが表示されます。

   ![SQL Workbench 接続](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. [SQL クエリ] タブで次のクエリを実行し、CMS および監査データベースのスキーマを作成します。

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. スキーマに接続するためのユーザー アカウントを作成します。

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. MySQL ユーザー アカウントの権限とロールを確認するには

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Linux サーバーへの MySQL C API コネクタ (libmysqlclient) のインストール

SAP BOBI アプリケーション サーバーからデータベースへのアクセスには、データベース クライアントまたはドライバーが必要です。 CMS および監査データベースにアクセスするには、Linux 用 MySQL C API コネクタを使用する必要があります。 CMS データベースへの ODBC 接続はサポートされていません。 このセクションでは、Linux で MySQL C API コネクタを設定する方法について説明します。

1. Azure Database for MySQL と互換性のあるドライバーについて説明している、[Azure Database for MySQL と互換性のある MySQL ドライバーと管理ツール](../../../mysql/concepts-compatibility.md)に関する記事を参照してください。 その記事で **MySQL コネクタ/C (libmysqlclient)** ドライバーについて確認します。

2. こちらの[リンク](https://downloads.mysql.com/archives/c-c/)を参照してドライバーをダウンロードします。

3. オペレーティング システムを選択し、MySQL コネクタの共有コンポーネント rpm パッケージをダウンロードします。 この例では、mysql-connector-c-shared-6.1.11 コネクタ バージョンが使用されています。

4. すべての SAP BOBI アプリケーション インスタンスにコネクタをインストールします。

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. libmysqlclient.so のパスを確認します。

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. インストールに使用されるユーザー アカウントの `/usr/lib64` ディレクトリを指すように LD_LIBRARY_PATH を設定します。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>サーバーの準備

このセクションの手順では、次のプレフィックスを使用します。

**[A]** :この手順はすべてのホストに適用されます。

1. **[A]** Linux のフレーバー (SLES または RHEL) に基づいて、カーネル パラメーターを設定し、必要なライブラリをインストールする必要があります。 「[Business Intelligence プラットフォーム インストール ガイド (UNIX)](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)」の「**システム要件**」のセクションを参照してください。

2. **[A]** コンピューターのタイム ゾーンが確実に正しく設定されているようにします。 インストール ガイドの[「追加の UNIX および Linux 要件」のセクション](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html)を参照してください。

3. **[A]** ソフトウェアのバックグラウンド プロセスを実行できるユーザー アカウント (**bl1** adm) とグループ (sap sys) を作成します。 このアカウントを使用してインストールを実行し、ソフトウェアを実行します。 このアカウントにルート特権は必要ありません。

4. **[A]** ユーザー アカウント (**bl1** adm) 環境を、サポートされている UTF-8 ロケールを使用するように設定し、コンソール ソフトウェアにより UTF-8 文字セットが確実にサポートされているようにします。 オペレーティング システムで正しいロケールが使用されるようにするには、(**bl1** adm) ユーザー環境で、LC_ALL および LANG 環境変数を優先ロケールに設定します。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** ユーザー アカウント (**bl1** adm) を構成します。

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. SAP サービス マーケットプレースから SAP BusinessObjects BI プラットフォームのメディアをダウンロードして抽出します。

## <a name="installation"></a>インストール

サーバー上のユーザー アカウント **bl1** adm のロケールを確認します。

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

SAP BusinessObjects BI プラットフォームのメディアに移動し、**bl1** adm ユーザーで以下のコマンドを実行します。

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

ご使用のバージョンに応じて、「[SAP BOBI プラットフォーム](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) インストール ガイド (UNIX)」に従います。 SAP BOBI プラットフォームのインストール時に注意する点がいくつかあります。

- **[製品登録の設定]** 画面で、SAP ノート [1288121](https://launchpad.support.sap.com/#/notes/1288121) にある SAP BusinessObjects ソリューションの一時ライセンス キーを使用するか、SAP サービス マーケットプレースでライセンス キーを生成することができます。

- **[インストール タイプの選択]** 画面で、最初のサーバー (azusbosl1) に **[フル]** を選択し、もう 1 つのサーバー (azusbosl2) には **[カスタム/拡張]** を選択して、既存の BOBI セットアップを拡張します。

- **[デフォルト データベースまたは既存データベースの選択]** 画面で、 **[既存のデータベースの設定]** を選択します。これにより、CMS および監査データベースを選択するよう求められます。 CMS データベースの種類と監査データベースの種類として **[MySQL]** を選択します。

  インストール中に監査を構成しない場合は、[監査データベースなし] を選択することもできます。

- **[Java Web アプリケーション サーバーの選択]** 画面で、使用している SAP BOBI アーキテクチャに基づいて適切なオプションを選択します。 この例では、オプション 1 を選択しました。これにより、同じ SAP BOBI プラットフォームに Tomcat サーバーがインストールされます。

- **[CMS リポジトリ データベースの設定 - MySQL]** に CMS データベースの情報を入力します。 Linux インストールの CMS データベース情報の入力例。 Azure Database for MySQL には既定のポート 3306 が使用されます。
  
  ![Linux での SAP BOBI のデプロイ - CMS データベース](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (オプション) 監査データベースの情報を **[監査リポジトリ データベースの設定 - MySQL]** に入力します。 Linux のインストールでの監査データベース情報の入力例。

  ![Linux での SAP BOBI のデプロイ - 監査データベース](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- 手順に従って必要な情報を入力し、インストールを完了させます。

複数インスタンス デプロイの場合は、2 番目のホスト (azusbosl2) に対してインストール セットアップを実行します。 **[インストール タイプの選択]** 画面で、 **[カスタム/拡張]** を選択します。これにより、既存の BOBI セットアップを拡張します。

Azure Database for MySQL オファリングの場合、ゲートウェイを使用して接続がサーバー インスタンスにリダイレクトされます。 接続が確立すると、MySQL には、MySQL サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MySQL のバージョンが表示されます。 MySQL サーバー インスタンスのバージョンを判断するには、MySQL プロンプトで `SELECT VERSION();` コマンドを使用します。 そのため、中央管理コンソール (CMC) には異なるデータベース バージョン (基本的にはゲートウェイに設定されているバージョン) が表示されます。 詳細については、「[サポートされている Azure Database for MySQL サーバー バージョン](../../../mysql/concepts-supported-versions.md)」を参照してください。

![Linux での SAP BOBI のデプロイ - CMC の設定](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>インストール後

### <a name="tomcat-clustering---session-replication"></a>Tomcat クラスタリング - セッション レプリケーション

Tomcat を使用すると、セッション レプリケーションとフェールオーバーのために、2 つ以上のアプリケーション サーバーのクラスタリングがサポートされます。 SAP BOBI プラットフォーム セッションはシリアル化されています。アプリケーション サーバーで障害が発生した場合でも、Tomcat の別のインスタンスへのユーザー セッションのシームレスなフェールオーバーが可能です。

たとえば、ユーザーが SAP BI アプリケーションでフォルダー階層内を移動しているときに、接続している Web サーバーに障害が発生した場合などです。 正しく構成されたクラスターを使用すると、ユーザーはサインイン ページにリダイレクトされることなく、フォルダー階層内を移動することができます。

SAP ノート [2808640](https://launchpad.support.sap.com/#/notes/2808640) には、マルチキャストを使用して Tomcat クラスタリングを構成する手順が示されています。 しかし、マルチキャストは Azure でサポートされていません。 そのため、Tomcat クラスターを Azure で機能させるには、[StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) を使用する必要があります (SAP ノート [2764907](https://launchpad.support.sap.com/#/notes/2764907))。 Azure で Tomcat クラスターを設定するには、SAP ブログの「[Tomcat Clustering using Static Membership for SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/)」(SAP BusinessObjects BI プラットフォームの静的なメンバーシップを使用した Tomcat クラスタリング) をご確認ください。

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI プラットフォームの Web 層の負荷分散

SAP BOBI の複数インスタンス デプロイの場合、Java Web アプリケーション サーバー (Web 層) が 2 つ以上のホストで実行されています。 Web サーバー間でユーザー負荷を均等に分散するために、エンド ユーザーと Web サーバーの間にロード バランサーを使用できます。 Azure で Azure Load Balancer または Azure Application Gateway を使用して、Web アプリケーション サーバーへのトラフィックを管理できます。 各オファリングの詳細について次のセクションで説明します。

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure Load Balancer (ネットワークベースのロード バランサー)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) は、ハイ パフォーマンスで低遅延のレイヤー 4 (TCP、UDP) ロード バランサーであり、正常な仮想マシン間でトラフィックを分散します。 Load Balancer の正常性プローブによって、各 VM の特定のポートが監視され、稼働している仮想マシンにのみトラフィックが分散されます。 SAP BI プラットフォームをインターネットからアクセスできるようにするかどうかに応じて、パブリック ロード バランサーまたは内部ロード バランサーのいずれかを選択できます。 ゾーン冗長であるため、可用性ゾーン全体で高可用性が確保されます。

次の図の内部ロード バランサーのセクションを参照してください。Web アプリケーション サーバーは、既定の Tomcat HTTP ポートであるポート 8080 で実行され、正常性プローブによって監視されます。 そのため、エンド ユーザーからの受信要求は、バックエンド プール内の Web アプリケーション サーバー (azusbosl1 または azusbosl2) にリダイレクトされます。 Load Balancer で TLS/SSL 終端 (TLS/SSL オフロードとも呼ばれます) はサポートされていません。 Azure Load Balancer を使用して Web サーバー間でトラフィックを分散する場合は、Standard Load Balancer を使用することをお勧めします。

> [!NOTE]
> パブリック IP アドレスのない VM が、内部 (パブリック IP アドレスがない) Standard の Azure Load Balancer のバックエンド プール内に配置されている場合、パブリック エンドポイントへのルーティングを許可するように追加の構成が実行されない限り、送信インターネット接続はありません。 送信接続を実現する方法の詳細については、「[SAP の高可用性シナリオにおける Azure Standard Load Balancer を使用した Virtual Machines のパブリック エンドポイント接続](high-availability-guide-standard-load-balancer-outbound-connections.md)」を参照してください。

![Web サーバー間でトラフィックを分散するための Azure Load Balancer](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (Web アプリケーション ロード バランサー)

[Azure Application Gateway (AGW)](../../../application-gateway/overview.md) によって、サービスとしてのアプリケーション デリバリー コントローラー (ADC) が提供されます。これは、アプリケーションでユーザー トラフィックを 1 つ以上の Web アプリケーション サーバーに転送するために使用されます。 TLS/SSL オフロード、Web Application Firewall (WAF)、Cookie ベースのセッション アフィニティなど、レイヤー 7 のさまざまな負荷分散機能がアプリケーションに提供されます。

Application Gateway によって、SAP BI プラットフォームのアプリケーション Web トラフィックがバックエンド プール (azusbosl1 または azusbos2) 内の指定されたリソースに転送されます。 ユーザーはリスナーをポートに割り当て、ルールを作成し、リソースをバックエンド プールに追加します。 次の図では、プライベート フロントエンド IP アドレス (10.31.3.20) を持つ Application Gateway がユーザーのエントリ ポイントとして機能し、それによって受信 TLS/SSL (HTTPS - TCP/443) 接続が処理され、TLS/SSL の暗号化が解除され、暗号化されていない要求 (HTTP - TCP/8080) がバックエンド プール内のサーバーに渡されます。 組み込みの TLS/SSL 終端機能を使用することで、Application Gateway で 1 つの TLS/SSL 証明書を維持するだけで済み、操作が簡単になります。

![Web サーバー間でトラフィックを分散するための Application Gateway](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

SAP BOBI Web サーバー用に Application Gateway を構成するには、SAP ブログの「[Load Balancing SAP BOBI Web Servers using Azure Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/)」(Azure Application Gateway を使用した SAP BOBI Web サーバーの負荷分散) を参照してください。

> [!NOTE]
> Azure Application Gateway を使用して Web サーバーへのトラフィックの負荷を分散することをお勧めします。それにより、SSL オフロードや、サーバーでの暗号化と解読のオーバーヘッドを削減する SSL 管理の一元化、トラフィック分散のためのラウンドロビン アルゴリズム、Web アプリケーション ファイアウォール (WAF) 機能、高可用性などのような機能が提供されます。

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI プラットフォーム - バックアップと復元

バックアップと復元は、データとアプリケーションのコピーを別の場所に定期的に作成するプロセスです。 それにより、元のデータまたはアプリケーションが失われたり破損したりした場合に、以前の状態に復元または復旧できます。 また、それはビジネス ディザスター リカバリー戦略の不可欠な要素でもあります。

SAP BOBI プラットフォームの包括的なバックアップと復元の戦略を策定するには、アプリケーションのシステム ダウンタイムや中断につながるコンポーネントを特定します。 SAP BOBI プラットフォームでは、アプリケーションを保護するために、次のコンポーネントのバックアップが不可欠です。

- SAP BOBI インストール ディレクトリ (マネージド Premium ディスク)
- ファイル リポジトリ サーバー (Azure NetApp Files または Azure Premium Files)
- CMS データベース (Azure Database for MySQL または Azure VM 上のデータベース)

次のセクションでは、SAP BOBI プラットフォームの各コンポーネントのバックアップと復元の戦略を実装する方法について説明します。

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>SAP BOBI インストール ディレクトリのバックアップと復元

Azure において、アプリケーション サーバーおよび接続されているすべてのディスクをバックアップする最も簡単な方法は、[Azure Backup](../../../backup/backup-overview.md) サービスを使用することです。 それにより、VM 上のデータが誤って破壊されることを防ぐために、独立した、分離されたバックアップが提供されます。 バックアップは、復旧ポイントの管理機能をビルトインで備えた Recovery Services コンテナーに格納されます。 構成とスケーリングはシンプルで、バックアップは最適化され、必要に応じて簡単に復元することができます。

バックアップ プロセスの一環として、スナップショットが取得され、運用環境のワークロードに影響を与えることなく、データが Recovery Services コンテナーに転送されます。 スナップショットによって、「[スナップショットの整合性](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency)」の記事で説明されているように、さまざまなレベルの一貫性が提供されます。 また、選択的なディスク バックアップと復元の機能を使用して、VM 内のデータ ディスクのサブセットをバックアップすることもできます。 詳細については、[Azure VM バックアップ](../../../backup/backup-azure-vms-introduction.md)のドキュメントと「[よくあるご質問 - Azure VM のバックアップ](../../../backup/backup-azure-vm-backup-faq.yml)」を参照してください。

#### <a name="backup--restore-for-file-repository-server"></a>ファイル リポジトリ サーバーのバックアップと復元

**Azure NetApp Files** に対しては、オンデマンドのスナップショットを作成し、スナップショット ポリシーを使用して自動スナップショット作成のスケジュールを設定できます。 スナップショット コピーによって、ANF ボリュームの特定時点のコピーが提供されます。 詳細については、「[Azure NetApp Files を使用して、スナップショットを管理する](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)」を参照してください。

**Azure Files** バックアップは、ネイティブの [Azure Backup](../../../backup/backup-overview.md) サービスと統合されています。これにより、バックアップと復元の機能が VM のバックアップと共に一元化され、運用作業が簡単になります。 詳細については、「[Azure ファイル共有のバックアップについて](../../../backup/azure-file-share-backup-overview.md)」と[よくあるご質問 (Azure Files のバックアップ)](../../../backup/backup-azure-files-faq.md) に関するページを参照してください。

#### <a name="backup--restore-for-cms-database"></a>CMS データベースのバックアップと復元

Azure Database for MySQL は、Azure の DBaaS オファリングであり、サーバーのバックアップが自動的に作成され、ユーザーが構成したローカル冗長ストレージまたは geo 冗長ストレージに保存されます。 Azure Database for MySQL によって、データ ファイルとトランザクション ログのバックアップが作成されます。 サポートされている最大ストレージ サイズに応じて、完全バックアップと差分バックアップ (最大 4 TB のストレージ サーバー) またはスナップショット バックアップ (最大 16 TB のストレージ サーバー) が作成されます。 これらのバックアップを使用すると、サーバーを、構成済みのバックアップ保持期間内の任意の時点に復元できます。 既定のバックアップ保持期間は 7 日間です。これは最大 3 日間まで[必要に応じて構成](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)できます。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。

これらのバックアップ ファイルはユーザーに公開されておらず、エクスポートできません。 これらのバックアップは、Azure Database for MySQL の復元操作にのみ使用できます。 [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) を使用して、データベースをコピーできます。 詳細については、「[Azure Database for MySQL でのバックアップと復元](../../../mysql/concepts-backup.md)」を参照してください。

Virtual Machines にインストールされているデータベースの場合、HANA データベースに対して標準のバックアップ ツールまたは [Azure Backup](../../../backup/sap-hana-db-about.md) を使用できます。 また、Azure のサービスとツールで要件が満たされない場合は、他のバックアップ ツールまたはスクリプトを使用してディスク バックアップを作成できます。

## <a name="sap-businessobjects-bi-platform-reliability"></a>SAP BusinessObjects BI プラットフォームの信頼性

SAP BusinessObjects BI プラットフォームには、特定のタスクと操作用に最適化されたさまざまな階層が含まれています。 いずれか 1 つの層のコンポーネントが使用できなくなると、SAP BOBI アプリケーションがアクセス不能になるか、アプリケーションの特定の機能が動作しなくなります。 そのため、ビジネスの中断なくアプリケーションが稼働し続けるには、各層が高い信頼性を持つように設計されている必要があります。

このセクションでは、SAP BOBI プラットフォームの次のオプションについて重点的に説明します。

- **高可用性:** 高可用性プラットフォームには、Azure リージョン内のあらゆるものが少なくとも 2 つずつ存在し、いずれかのサーバーが使用できなくなった場合でもアプリケーションが稼働し続けます。
- **ディザスター リカバリー:** 何らかの自然災害によって Azure リージョン全体が使用できなくなるなどの致命的な損失が発生した場合に、アプリケーションの機能を復元するプロセスです。

このソリューションの実装は、Azure のシステム設定の種類によって異なります。 そのため、高可用性およびディザスター リカバリー ソリューションは、お客様がビジネス要件に基づいて調整する必要があります。

### <a name="high-availability"></a>高可用性

高可用性は、IT の中断を最小限に抑えることができる一連のテクノロジを表し、同じデータ センター内の冗長性、フォールト トレランス、またはフェールオーバーで保護されたコンポーネントを通じてアプリケーションやサービスのビジネス継続性を提供することで実現されます。 ここの例では、データ センターは 1 つの Azure リージョン内にあります。 このセクションの手順を補完する [SAP のための高可用性アーキテクチャとシナリオ](sap-high-availability-architecture-scenarios.md)に関する記事から、Azure に用意されている SAP アプリケーション向けのさまざまな高可用性の手法と推奨事項について最初の分析情報が提供されます。

SAP BOBI プラットフォームのサイズ設定の結果に基づいて、ランドスケープを設計し、Azure Virtual Machines およびサブネット全体での BI コンポーネントの分散を決定する必要があります。 分散アーキテクチャの冗長性レベルは、ビジネスに必要な目標復旧時間 (RTO) と目標復旧時点 (RPO) によって異なります。 SAP BOBI プラットフォームには異なる複数の層が含まれており、各層のコンポーネントを、冗長性を実現するように設計する必要があります。 それにより、どれか 1 つのコンポーネントで障害が発生した場合に SAP BOBI アプリケーションが中断されることはほとんど、またはまったくありません。 たとえば、次のように入力します。

- BI アプリケーション サーバーや Web サーバーなどの冗長アプリケーション サーバー
- CMS データベース、ファイル リポジトリ サーバー、ロード バランサーなどの固有のコンポーネント

次のセクションでは、SAP BOBI プラットフォームの各コンポーネントで高可用性を実現する方法について説明します。

#### <a name="high-availability-for-application-servers"></a>アプリケーション サーバーの高可用性

BI および Web アプリケーション サーバーの場合、個別に (または一緒に) インストールされているかどうかにかかわらず、特定の高可用性ソリューションは必要ありません。 さまざまな Azure 仮想マシンに BI および Web サーバーの複数のインスタンスを構成することで、冗長性による高可用性を実現できます。

1 つまたは複数のイベントを原因とするダウンタイムの影響を軽減するために、複数の仮想マシンで実行されているアプリケーション サーバーについて、次の高可用性プラクティスに従うことをお勧めします。

- 可用性ゾーンを使ってデータセンターの障害から保護する。
- 冗長性実現のために複数の仮想マシンを可用性セット内に構成する。
- 可用性セット内の VM にマネージド ディスクを使用する。
- 各アプリケーション層に対して別々の可用性セットを構成する。

詳細については、[Linux 仮想マシンの可用性の管理](../../availability.md)に関するページを参照してください。

#### <a name="high-availability-for-cms-database"></a>CMS データベースの高可用性

Azure Database as a Service (DBaaS) サービスを CMS データベースとして使用している場合は、高可用性フレームワークが既定で提供されます。 ユーザーは、高可用性、冗長性、回復性の各機能が最初から備わっているリージョンとサービスを選択するだけで済み、追加のコンポーネントを構成する必要がありません。 Azure でサポートされている DBaaS オファリングの SLA の詳細については、[Azure Database for MySQL の高可用性](../../../mysql/concepts-high-availability.md)および [Azure SQL Database の高可用性](../../../azure-sql/database/high-availability-sla.md)に関するページを参照してください。

CMS データベースのその他の DBMS デプロイについては、[SAP ワークロードのための DBMS デプロイ ガイド](dbms_guide_general.md)のページを参照してください。さまざまな DBMS デプロイと、それぞれの高可用性を実現する分析情報が明解に説明されています。

#### <a name="high-availability-for-file-repository-server"></a>ファイル リポジトリ サーバーの高可用性

ファイル リポジトリ サーバー (FRS) とは、レポート、ユニバース、接続などの内容が格納されているディスク ディレクトリを指します。 それは、そのシステムのすべてのアプリケーション サーバー間で共有されます。 そのため、高可用性を確保する必要があります。

Azure を使用すると、高可用性と高い耐久性を実現するように設計された [Azure Premium Files](../../../storage/files/storage-files-introduction.md) または [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) のいずれかをファイル共有のために選択できます。 詳細については、Azure Files の「[冗長性](../../../storage/files/storage-files-planning.md#redundancy)」セクションを参照してください。

> [!NOTE]
> Azure Files の SMB プロトコルは一般公開されていますが、Azure Files の NFS プロトコル サポートは現在プレビューの段階です。 詳細については、「[Azure Files での NFS 4.1 サポートのプレビューが開始されました](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)」を参照してください。

このファイル共有サービスはすべてのリージョンで使用できるわけではないため、最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/en-us/global-infrastructure/services/)」のサイトを参照してください。 お客様のリージョンでサービスを利用できない場合は、NFS サーバーを作成し、そのファイル システムを SAP BOBI アプリケーションと共有できます。 しかし、高可用性を考慮する必要もあります。

#### <a name="high-availability-for-load-balancer"></a>ロード バランサーの高可用性

Web サーバー全体でトラフィックを分散するには、Azure Load Balancer または Azure Application Gateway のいずれかを使用できます。 デプロイ用に選択した SKU に基づいて、どちらのロード バランサーの冗長性も実現できます。

- Azure Load Balancer の場合、Standard Load Balancer フロントエンドをゾーン冗長として構成することで冗長性を実現できます。 詳細については、「[Standard Load Balancer と可用性ゾーン](../../../load-balancer/load-balancer-standard-availability-zones.md)」を参照してください。
- Application Gateway の場合、デプロイ中に選択した層の種類に基づいて高可用性を実現できます。
  - v1 SKU を使用すると、2 つ以上のインスタンスをデプロイした場合に高可用性シナリオがサポートされます。 Azure は、これらのインスタンスを更新ドメインと障害ドメインに分散させ、全インスタンスで同時に障害が発生しないようにします。 そのため、この SKU によって、ゾーン内で冗長性を実現できます。
  - v2 SKU を使用すると、新しいインスタンスが障害ドメインと更新ドメインに自動的に分散されます。 ゾーン冗長を選択した場合は、ゾーン障害回復性を提供するために、最新のインスタンスが可用性ゾーンにも分散されます。 詳細については、「[自動スケーリングとゾーン冗長 Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)」を参照してください。

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI プラットフォームの高可用性の参照アーキテクチャ

以下の参照アーキテクチャは、可用性セットを使用した SAP BOBI プラットフォームのセットアップを示しています。これにより、ゾーン内での VM の冗長性と可用性が提供されます。 このアーキテクチャには、SAP BOBI プラットフォーム向けのさまざまな Azure サービス (Azure Application Gateway、Azure NetApp Files、Azure Database for MySQL など) が使用されています。組み込みの冗長性が提供されるため、異なる複数の高可用性ソリューションを管理する複雑さが軽減されます。

次の図では、受信トラフィック (HTTPS - TCP/443) は Azure Application Gateway v1 SKU を使用して負荷分散されています。これにより、2 つ以上のインスタンスにデプロイした場合に高可用性が実現されます。 冗長性を確保するために、Web サーバー、管理サーバー、処理サーバーの複数のインスタンスが別々の仮想マシンにデプロイされ、各層が別々の可用性セットにデプロイされます。 Azure NetApp Files には、データ センター内に冗長性が組み込まれているため、ファイル リポジトリ サーバーの ANF ボリュームでの高可用性が実現されます。 CMS データベースは、高可用性が最初から備わっている Azure Database for MySQL (DBaaS) 上にプロビジョニングされます。 詳細については、「[Azure Database for MySQL での高可用性](../../../mysql/concepts-high-availability.md)」を参照してください。

![可用性セットを使用した SAP BusinessObjects BI プラットフォームの冗長性](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

上記のアーキテクチャは、Azure での SAP BOBI のデプロイを実行する分析情報を明解に示しています。 ただし、Azure 上の SAP BOBI プラットフォームでの可能なすべての構成オプションを網羅しているわけではありません。 お客様は、Load Balancer、ファイル リポジトリ サーバー、DBMS などのさまざまなコンポーネントとして異なる製品やサービスを選択することにより、ビジネス要件に基づいてデプロイを調整できます。

いくつかの Azure リージョンには可用性ゾーンが用意されています。これは、電源、冷却、ネットワークが独立して提供されることを意味します。 これにより、お客様は 2 つか 3 つの可用性ゾーンにまたがってアプリケーションをデプロイできます。 AZs 全体で高可用性を実現することを考えているお客様は、複数の可用性ゾーンにまたがって SAP BOBI プラットフォームをデプロイし、アプリケーションの各コンポーネントが確実にゾーン冗長になるようにしてください。

### <a name="disaster-recovery"></a>障害復旧

このセクションの手順では、SAP BOBI プラットフォームのディザスター リカバリー保護を用意する方法について説明します。 それにより、SAP のディザスター リカバリー アプローチ全体の主要なリソースを表す [SAP のディザスター リカバリー](../../../site-recovery/site-recovery-sap.md) ドキュメントを補完します。

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI プラットフォームのディザスター リカバリーの参照アーキテクチャ

この参照アーキテクチャにおいては、冗長なアプリケーション サーバーを使用した SAP BOBI プラットフォームの複数インスタンス デプロイが実行されています。 ディザスター リカバリーを行うために、すべての層をセカンダリ リージョンにフェールオーバーする必要があります。 各層では、さまざまな戦略を利用して、ディザスター リカバリーの保護を提供しています。

![SAP BusinessObjects BI プラットフォームのディザスター リカバリー](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Load Balancer

Load Balancer は、SAP BOBI プラットフォームの Web アプリケーション サーバー間でトラフィックを分散するために使用されます。 Azure Application Gateway の DR を実現するには、セカンダリ リージョン上にアプリケーション ゲートウェイの並列セットアップを実装します。

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Web および BI アプリケーション サーバーを実行している仮想マシン

Azure Site Recovery サービスを使用して、Web および BI アプリケーション サーバーを実行している仮想マシンをセカンダリ リージョン上でレプリケートできます。 災害や障害が発生したときに、レプリケートされた環境に簡単にフェールオーバーして作業を続けることができるように、セカンダリ リージョン上でサーバーをレプリケートします。

#### <a name="file-repository-servers"></a>ファイル リポジトリ サーバー

- **Azure NetApp Files** によって NFS および SMB ボリュームが提供されるため、ファイルベースの任意のコピー ツールを使用して、Azure リージョン間でデータをレプリケートできます。 別のリージョンにある ANF ボリュームをコピーする方法の詳細については、[Azure NetApp Files に関するよくあるご質問](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)のページを参照してください。

  Azure NetApp Files リージョン間レプリケーションを使用できます。これは現在[プレビュー](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/)段階であり、NetApp SnapMirror® テクノロジが活用されています。 そのため、変更されたブロックだけが、圧縮された効率的な形式でネットワーク経由で送信されます。 この独自のテクノロジによってリージョン間でのレプリケーションに必要なデータ量が最小化されることで、データ転送コストが削減されます。 また、レプリケーションにかかる時間が短縮されるため、より小さい回復ポイントの目標 (RPO) を実現できます。 詳細については、「[リージョン間レプリケーションを使用するための要件と考慮事項](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md)」を参照してください。

- **Azure Premium Files** を使用する場合、ローカル冗長 (LRS) およびゾーン冗長ストレージ (ZRS) のみがサポートされます。 Azure Premium Files の DR 戦略を使用する場合、[AzCopy](../../../storage/common/storage-use-azcopy-v10.md) または [Azure PowerShell](/powershell/module/az.storage/) を使用して、異なるリージョンの別のストレージ アカウントにファイルをコピーすることができます。 詳細については、「[ディザスター リカバリーとストレージ アカウントのフェールオーバー](../../../storage/common/storage-disaster-recovery-guidance.md)」を参照してください。

#### <a name="cms-database"></a>CMS データベース

Azure Database for MySQL には、障害が発生した場合にデータベースを回復するための複数のオプションが用意されています。 ご自身のビジネスに適したオプションを選択してください。

- 事業継続とディザスター リカバリーの計画を強化するには、リージョンにまたがる読み取りレプリカを有効にします。 ソース サーバーから最大 5 つのレプリカにレプリケートできます。 読み取りレプリカは、MySQL のバイナリ ログ レプリケーション テクノロジを使用して非同期的に更新されます。 レプリカは、通常の Azure Database for MySQL サーバーと同様に管理する新しいサーバーです。 読み取りレプリカ、利用可能なリージョン、制限、フェールオーバーする方法については、[読み取りレプリカの概念に関する記事](../../../mysql/concepts-read-replicas.md)を参照してください。

- geo 冗長バックアップを使ってサーバーを復元する Azure Database for MySQL の geo リストア機能を使用します。 これらのバックアップは、サーバーがホストされているリージョンがオフラインのときでもアクセスできます。 これらのバックアップから他の任意のリージョンに復元し、サーバーをオンラインに戻すことができます。

  > [!NOTE]
  > geo リストアは、geo 冗長バックアップ ストレージでサーバーをプロビジョニングした場合にのみ可能です。 サーバー作成後に **[バックアップ冗長オプション]** を変更することはサポートされていません。 詳細については、[バックアップの冗長性](../../../mysql/concepts-backup.md#backup-redundancy-options)に関する記事を参照してください。

この例で使用される各層のディザスター リカバリーの推奨事項は次のとおりです。

| SAP BOBI プラットフォームの層   | 推奨                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | セカンダリ リージョンでの Application Gateway の並列セットアップ                                                |
| Web アプリケーション サーバー   | Site Recovery を使ったレプリケーション                                                                         |
| BI アプリケーション サーバー    | Site Recovery を使ったレプリケーション                                                                         |
| Azure NetApp Files        | セカンダリ リージョンにデータをレプリケートするためのファイルベースのコピー ツール **または** ANF リージョン間レプリケーション (プレビュー) |
| Azure Database for MySQL  | リージョン間の読み取りレプリカ **または** geo 冗長バックアップからのバックアップの復元。                             |

## <a name="next-steps"></a>次の手順

- [多層 SAP アプリ デプロイのディザスター リカバリーを設定する](../../../site-recovery/site-recovery-sap.md)
- [SAP のための Azure Virtual Machines の計画と実装](planning-guide.md)
- [SAP のための Azure Virtual Machines のデプロイ](deployment-guide.md)
- [SAP のための Azure Virtual Machines DBMS のデプロイ](./dbms_guide_general.md)
