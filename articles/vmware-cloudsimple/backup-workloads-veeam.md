---
title: Azure VMware Solution by CloudSimple - Veeam を使用してプライベート クラウド上のワークロード仮想マシンをバックアップする
description: Azure ベースの CloudSimple プライベート クラウドで実行されている仮想マシンを Veeam B&R 9.5 を使用してバックアップする方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be606b3e23a594e67acf3f169d88353403d8577
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899339"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Veeam B&R を使用して CloudSimple プライベート クラウド上のワークロード VM をバックアップする

このガイドでは、Azure ベースの CloudSimple プライベート クラウドで実行されている仮想マシンを、Veeam B&R 9.5 を使用してバックアップする方法について説明します。

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Veeam バックアップおよび復旧ソリューションについて

Veeam ソリューションには、次のコンポーネントが含まれています。

**バックアップ サーバー**

バックアップ サーバーは、Veeam のコントロールセンターとして機能し、次の機能を実行する Windows サーバー (VM) です。 

* バックアップ、レプリケーション、復旧検証、復元の各タスクを調整する
* ジョブのスケジューリングとリソースの割り当てを制御する
* バックアップ インフラストラクチャ コンポーネントを設定および管理し、バックアップ インフラストラクチャのグローバル設定を指定する

**プロキシ サーバー**

プロキシ サーバーは、バックアップ サーバーと、バックアップ インフラストラクチャのその他のコンポーネントとの間にインストールされます。 次の機能を管理します。

* 運用ストレージからの VM データの取得
* 圧縮
* 重複除去
* 暗号化
* バックアップ リポジトリへのデータの転送

**バックアップ リポジトリ**

バックアップ リポジトリは、バックアップ ファイル、VM のコピー、レプリケートされた VM のメタデータを Veeam が保持するストレージの場所です。  リポジトリにできるのは、ローカル ディスク (またはマウントされた NFS/SMB) を備えた Windows または Linux サーバー、あるいはハードウェア ストレージ重複除去アプライアンスです。

### <a name="veeam-deployment-scenarios"></a>Veeam のデプロイのシナリオ
Azure を利用して、バックアップ リポジトリと、長期のバックアップおよびアーカイブのためのストレージ ターゲットを提供できます。 プライベート クラウド内の VM と、Azure 内のバックアップ リポジトリ間とのバックアップ ネットワーク トラフィックはすべて、高帯域幅、低待機時間のリンクを経由して送信されます。 リージョン間のレプリケーション トラフィックは内部の Azure バックプレーン ネットワークを経由して送信されるため、ユーザーの帯域幅コストが削減されます。

**基本的なデプロイ**

バックアップが 30 TB 未満の環境に対して、CloudSimple は次の構成を推奨しています。

* プライベート クラウド内の同じ VM に Veeam バックアップ サーバーとプロキシ サーバーをインストールします。
* Azure 内の Linux ベースのプライマリ バックアップ リポジトリをバックアップ ジョブのターゲットとして構成します。
* `azcopy` を使用して、プライマリ バックアップ リポジトリのデータを Azure BLOB コンテナーにコピーし、このコンテナーを別のリージョンにレプリケートします。

![基本的な Veeam のデプロイのシナリオを示す図。](media/veeam-basicdeployment.png)

**高度なデプロイ**

バックアップが 30 TB を超える環境に対して、CloudSimple は次の構成を推奨しています。

* Veeam が推奨するように、vSAN クラスター内のノードごとに 1 つのプロキシ サーバーを配置します。
* プライベート クラウド内の Windows ベースのプライマリ バックアップ リポジトリに 5 日分のデータをキャッシュすることで復元を高速化します。
* Azure 内の Linux バックアップ リポジトリをバックアップ コピー ジョブのターゲットにすることでリテンション期間を延長します。 このリポジトリは、スケールアウト バックアップ リポジトリとして構成する必要があります。
* `azcopy` を使用して、プライマリ バックアップ リポジトリのデータを Azure BLOB コンテナーにコピーし、このコンテナーを別のリージョンにレプリケートします。

![基本的なデプロイのシナリオ](media/veeam-advanceddeployment.png)

前の図で、バックアップ プロキシは、vSAN データストア上のワークロード VM ディスクに対してホットアド アクセス権のある VM であることに注意してください。 Veeam では、仮想アプライアンスのバックアップ プロキシ転送モードを vSAN に使用します。

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>CloudSimple 上の Veeam ソリューションの要件

Veeam ソリューションでは、次の操作を行う必要があります。

* 独自の Veeam ライセンスを提供します。
* CloudSimple プライベート クラウドで実行されているワークロードをバックアップするために、Veeam をデプロイして管理します。

このソリューションでは、Veeam バックアップ ツールを完全に制御でき、VM バックアップ ジョブの管理にネイティブの Veeam インターフェイスと Veeam vCenter プラグインのどちらを使用するか選択できます。

既存の Veeam ユーザーである場合、Veeam ソリューション コンポーネントに関するセクションを飛ばして「[Veeam のデプロイのシナリオ](#veeam-deployment-scenarios)」に直接進むことができます。

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドに Veeam バックアップをインストールして構成する

以下のセクションでは、CloudSimple プライベート クラウド用の Veeam バックアップ ソリューションをインストールして構成する方法について説明します。

デプロイのプロセスは、以下の手順で構成されます。

1. [vCenter UI:プライベート クラウド内にインフラストラクチャ サービスを設定する](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple ポータル:Veeam 用にプライベート クラウドのネットワークを設定する](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple ポータル:特権をエスカレートする](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure portal: 仮想ネットワークをプライベート クラウドに接続する](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure portal: Azure 内にバックアップ リポジトリを作成する](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure portal: 長期的なデータ保持のために Azure BLOB ストレージを構成する](#configure-azure-blob-storage-for-long-term-data-retention)
7. [プライベート クラウドの vCenter UI:Veeam B&R をインストールする](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam コンソール:Veeam Backup & Recovery ソフトウェアを構成する](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple ポータル:Veeam のアクセス特権とエスカレート解除特権を設定する](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>開始する前に

Veeam のデプロイを開始する前に、以下のものが必要です。

* 組織が所有する Azure サブスクリプション
* 事前に作成された Azure リソース グループ
* 利用中のサブスクリプション内の Azure 仮想ネットワーク
* Azure ストレージ アカウント
* CloudSimple ポータルを使用して作成された[プライベート クラウド](create-private-cloud.md)。  

実装フェーズ中は、以下の項目が必要です。

* Veeam をインストールするための Windows 用 VMware テンプレート (64 ビット版 Windows Server 2012 R2 のイメージなど)
* 1 つの使用可能な VLANがバックアップ ネットワーク用に識別されていること
* バックアップ ネットワークに割り当てるためのサブネットの CIDR
* Veeam 9.5 u3 のインストール可能メディア (ISO) がプライベート クラウドの vSAN データストアにアップロードされていること

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter UI:プライベート クラウド内にインフラストラクチャ サービスを設定する

ワークロードとツールを簡単に管理できるように、プライベート クラウド内でインフラストラクチャ サービスを構成します。

* 次のいずれかに該当する場合、[Active Directory を使用するための vCenter ID ソースの設定](set-vcenter-identity.md)に関する記事で説明されているように外部 ID プロバイダーを追加できます。

  * プライベート クラウド内のオンプレミスの Active Directory (AD) からユーザーを識別したい。
  * すべてのユーザーに対してプライベート クラウドに AD を設定したい。
  * Azure AD を使用したい。
* プライベート クラウド内のワークロードに対して IP アドレス参照、IP アドレス管理、名前解決サービスを提供するには、「[CloudSimple プライベート クラウドでの DNS および DHCP アプリケーションとワークロードの設定](dns-dhcp-setup.md)」の説明に従って DHCP および DNS サーバーを設定します。

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple プライベート クラウド:Veeam 用にプライベート クラウドのネットワークを設定する

CloudSimple ポータルにアクセスして、Veeam ソリューションのプライベート クラウド ネットワークを設定します。

バックアップ ネットワーク用の VLAN を作成し、それをサブネット CIDR に割り当てます。 手順については、[VLAN/サブネットの作成と管理](create-vlan-subnet.md)に関する記事をご覧ください。

Veeam によって使用されるポートのネットワーク トラフィックを許可するために、管理サブネットとバックアップ ネットワークの間にファイアウォール規則を作成します。 [使用されるポート](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)に関する Veeam のトピックを参照してください。 ファイアウォール規則の作成手順については、[ファイアウォールのテーブルと規則の設定](firewall.md)に関する記事を参照してください。

次の表にポートの一覧を示します。

| アイコン | 説明 | アイコン | 説明 |
| ------------ | ------------- | ------------ | ------------- |
| バックアップ サーバー  | vCenter  | HTTPS/TCP  | 443 |
| バックアップ サーバー <br> *Veeam Backup & Replication コンポーネントのデプロイに必要* | バックアップ プロキシ  | TCP/UDP  | 135、137 ～ 139、445 |
    | バックアップ サーバー   | DNS  | UDP  | 53  | 
    | バックアップ サーバー   | Veeam 更新通知サーバー  | TCP  | 80  | 
    | バックアップ サーバー   | Veeam ライセンス更新サーバー  | TCP  | 443  | 
    | バックアップ プロキシ   | vCenter |   |   | 
    | バックアップ プロキシ  | Linux バックアップ リポジトリ   | TCP  | 22  | 
    | バックアップ プロキシ  | Windows バックアップ リポジトリ  | TCP  | 49152 ～ 65535   | 
    | バックアップ リポジトリ  | バックアップ プロキシ  | TCP  | 2500 ～ 5000  | 
    | ソース バックアップ リポジトリ<br> *バックアップ コピー ジョブに使用*  | ターゲット バックアップ リポジトリ  | TCP  | 2500 ～ 5000  | 

[ファイアウォールのテーブルと規則の設定](firewall.md)に関する記事で説明されているうに、ワークロード サブネットとバックアップ ネットワーク間のファイアウォール規則を作成します。  アプリケーション対応のバックアップと復元を行うには、特定のアプリケーションをホストするワークロード VM で[追加のポート](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)を開く必要があります。

既定では、CloudSimple は 1 Gbps の ExpressRoute リンクを提供します。 環境の規模がさらに大きい場合、より高帯域幅のリンクが必要になることがあります。 高帯域幅リンクの詳細については、Azure サポートにお問い合わせください。

セットアップを続行するには、承認キーとピア回線 URI、および Azure サブスクリプションへのアクセスが必要です。  この情報は、CloudSimple ポータルの仮想ネットワーク接続に関するページで確認できます。 手順については、「[Azure 仮想ネットワークから CloudSimple 接続へのピアリング情報を取得する](virtual-network-connection.md)」を参照してください。 情報の取得に問題がある場合は、[サポートに連絡](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple プライベート クラウド:cloudowner の特権をエスカレートする

既定の 'cloudowner' ユーザーには、VEEAM をインストールするための十分な特権がプライベート クラウド vCenter にないため、ユーザーの vCenter 特権をエスカレートする必要があります。 詳しくは、[特権のエスカレート](escalate-private-cloud-privileges.md)に関する記事をご覧ください。

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure portal:仮想ネットワークをプライベート クラウドに接続する

[ExpressRoute を使用した Azure 仮想ネットワーク接続](azure-expressroute-connection.md)に関する記事の説明に従って、仮想ネットワークをプライベート クラウドに接続します。

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure portal:バックアップ リポジトリ VM を作成する

1. Standard D2 v3 VM (2 個の vCPU と 8 GB メモリ) を作成します。
2. CentOS 7.4 ベースのイメージを選択します。
3. VM のネットワーク セキュリティ グループ (NSG) を構成します。 VM にパブリック IP アドレスがなく、パブリック インターネットから VM に到達できないことを確認します。
4. ユーザー名とパスワードを使用するユーザー アカウントを新しい VM 用に作成します。 手順については、「[Azure portal で Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-portal.md)」を参照してください。
5. 512 GiB Standard HDD を 1 個作成し、リポジトリ VM に接続します。  手順については、「[Azure portal を使用して Windows VM にマネージド データ ディスクを接続する](../virtual-machines/windows/attach-managed-disk-portal.md)」を参照してください。
6. [マネージド ディスク上に XFS ボリュームを作成します](https://www.digitalocean.com/docs/volumes/how-to/)。 前述の資格情報を使用して VM にログインします。 次のスクリプトを実行して、論理ボリュームを作成し、それにディスクを追加し、XFS ファイル システムの[パーティション](https://www.digitalocean.com/docs/volumes/how-to/partition/)を作成して、そのパーティションを /backup1 パス下に[マウント](https://www.digitalocean.com/docs/volumes/how-to/mount/)します。

    スクリプトの例:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. /backup1 を、プライベート クラウドで実行されている Veeam バックアップ サーバーへの NFS マウント ポイントとして公開します。 手順については、[CentOS 6 で NFS マウントを設定する方法](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6)に関する Digital Ocean の記事を参照してください。 Veeam バックアップ サーバーでバックアップ リポジトリを構成するときは、この NFS 共有名を使用します。

8. VM との間のすべてのネットワーク トラフィックを明示的に許可するように、バックアップ リポジトリ VM の NSG でフィルタリング規則を構成します。

> [!NOTE]
> Veeam Backup & Replication は、SSH プロトコルを使用して Linux バックアップ リポジトリと通信し、Linux リポジトリ上で SCP ユーティリティを必要とします。 SSH デーモンが正しく構成されていること、および、その SCP が Linux ホスト上で使用可能であることを確認します。

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>長期的なデータ保持のために Azure BLOB ストレージを構成する

1. [Azure Storage の基本](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)に関する Microsoft のビデオで説明されているように、標準タイプの汎用ストレージ アカウント (GPv2) と、BLOB コンテナーを作成します。
2. [コンテナーの作成](/rest/api/storageservices/create-container)に関するリファレンスで説明されているように、Azure ストレージ コンテナーを作成します。
2. Linux 用の `azcopy` コマンド ライン ユーティリティを Microsoft からダウンロードします。 CentOS 7.5 の bash シェルでは、次のコマンドを使用できます。

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. BLOB コンテナーとの間でバックアップ ファイルをコピーするには、`azcopy` コマンドを使用します。  詳細なコマンドについては、「[AzCopy on Linux を使ったデータの転送](../storage/common/storage-use-azcopy-v10.md)」を参照してください。

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>プライベート クラウドの vCenter コンソール:Veeam B&R をインストールする

プライベート クラウドから vCenter にアクセスして、Veeam サービス アカウントを作成し、Veeam B&R 9.5 をインストールし、サービス アカウントを使用して Veeam を構成します。

1. "Veeam Backup Role" という名前の新しいロールを作成し、Veeam によって推奨されている必要なアクセス許可をそのロールに割り当てます。 詳細については、[必要なアクセス許可](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)に関する Veeam のトピックを参照してください。
2. 新しい "Veeam User Group" グループを vCenter で作成して、それに "Veeam Backup Role" を割り当てます。
3. 新しい "Veeam Service Account" ユーザーを作成して、それを "Veeam User Group" に追加します。

    ![Veeam サービス アカウントの作成](media/veeam-vcenter01.png)

4. バックアップ ネットワーク VLAN を使用して、vCenter で分散ポート グループを作成します。 詳細については、[vSphere Web Client での分散ポート グループの作成](https://www.youtube.com/watch?v=wpCd5ZbPOpA)に関する VMware のビデオを参照してください。
5. vCenter で、[Veeam のシステム要件](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)に従って、Veeam のバックアップ サーバーとプロキシ サーバー用の VM を作成します。 Windows 2012 R2 または Linux を使用できます。 詳細については、[Linux バックアップ リポジトリを使用するための要件](https://www.veeam.com/kb2216)に関するページを参照してください。
6. インストール可能な Veeam ISO を CDROM デバイスとして Veeam バックアップ サーバー VM にマウントします。
7. Windows 2012 R2 マシン (Veeam インストールのターゲット) への RDP セッションを使用して、Windows 2012 R2 VM に [Veeam B&R 9.5u3 をインストール](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)します。
8. Veeam バックアップ サーバー VM の内部 IP アドレスを特定し、その IP アドレスが静的となるように DHCP サーバーで構成します。 これを行うために必要とされる厳密な手順は、DHCP サーバーによって異なります。 例として、<a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">静的 DHCP マッピング</a>に関する Netgate の記事では、pfSense ルーターを使用して DHCP サーバーを構成する方法について説明しています。

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam コンソール:Veeam バックアップおよび復旧ソフトウェアをインストールする

Veeam コンソールを使用して、Veeam バックアップおよび復旧ソフトウェアを構成します。 詳細については、[Veeam Backup & Replication v9 のインストールとデプロイ](https://www.youtube.com/watch?v=b4BqC_WXARk)に関するビデオを参照してください。

1. マネージド サーバー環境として VMware vSphere を追加します。 プロンプトが表示されたら、次の手順の冒頭で作成した Veeam サービス アカウントの資格情報を指定します: [プライベート クラウドの vCenter コンソール:Veeam B&R をインストールする](#vcenter-console-of-private-cloud-install-veeam-br)。

    * 負荷制御設定および詳細設定は既定のままにします。
    * マウント サーバーの場所がバックアップ サーバーとなるように設定します。
    * Veeam サーバーの構成バックアップの場所をリモート リポジトリに変更します。

2. Azure 内の Linux サーバーをバックアップ リポジトリとして追加します。

    * 負荷制御設定および詳細設定は既定のままにします。 
    * マウント サーバーの場所がバックアップ サーバーとなるように設定します。
    * Veeam サーバーの構成バックアップの場所をリモート リポジトリに変更します。

3. **[ホーム] > [Configuration Backup Settings]\(構成のバックアップの設定\)** を使用して、構成のバックアップの暗号化を有効にします。

4. VMware 環境用のプロキシ サーバーとして Windows サーバー VM を追加します。 プロキシに対する "トラフィック規則" を使用して、ネットワーク経由でバックアップ データを暗号化します。

5. バックアップ ジョブを構成します。
    * バックアップ ジョブを作成するには、[バックアップ ジョブの作成](https://www.youtube.com/watch?v=YHxcUFEss4M)に関するビデオの指示に従います。
    * **[Advanced Settings]\(詳細設定\) > [Storage]\(ストレージ\)** でバックアップ ファイルの暗号化を有効にします。

6. バックアップ コピー ジョブを構成します。

    * バックアップ コピー ジョブを構成するには、[バックアップ コピー ジョブの作成](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)に関するビデオの指示に従います。
    * **[Advanced Settings]\(詳細設定\) > [Storage]\(ストレージ\)** でバックアップ ファイルの暗号化を有効にします。

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple ポータル:Veeam のアクセス特権とエスカレート解除特権を設定する
Veeam バックアップおよび復旧サーバーのパブリック IP アドレスを作成します。 手順については、[パブリック IP アドレスの割り当て](public-ips.md)に関する記事をご覧ください。

を使用してファイアウォール規則を作成し、Veeam Web サイトへのアウトバウンド接続を作成することを Veeam バックアップ サーバーに許可して、TCP ポート 80 で更新プログラム/パッチをダウンロードできるようにします。 手順については、[ファイアウォールのテーブルと規則の設定](firewall.md)に関する記事をご覧ください。

特権のエスカレートを解除するには、「[特権のエスカレートを解除する](escalate-private-cloud-privileges.md#de-escalate-privileges)」をご覧ください。

## <a name="references"></a>References

### <a name="cloudsimple-references"></a>CloudSimple リファレンス

* [プライベート クラウドを作成する](create-private-cloud.md)
* [VLAN/サブネットを作成して管理する](create-vlan-subnet.md)
* [vCenter の ID ソース](set-vcenter-identity.md)
* [ワークロードの DNS と DHCP の設定](dns-dhcp-setup.md)
* [特権のエスカレート](escalate-privileges.md)
* [ファイアウォールのテーブルとルールのセットアップ](firewall.md)
* [プライベート クラウドのアクセス許可](learn-private-cloud-permissions.md)
* [パブリック IP アドレスを割り当てる](public-ips.md)

### <a name="veeam-references"></a>Veeam リファレンス

* [使用されるポート](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [必要なアクセス許可](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [システム要件](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Veeam Backup & Replication のインストール](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Linux でのマルチ OS FLR とリポジトリのサポートに必要なモジュールとアクセス許可](https://www.veeam.com/kb2216)
* [Veeam Backup & Replication v9 - インストールとデプロイ - ビデオ](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 バックアップ ジョブの作成 - ビデオ](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 バックアップ コピー ジョブの作成 - ビデオ](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure リファレンス

* [Azure portal を使用して ExpressRoute の仮想ネットワーク ゲートウェイを構成する](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [VNet を回線に接続する - 異なるサブスクリプション](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Azure portal で Linux 仮想マシンを作成する](../virtual-machines/linux/quick-create-portal.md)
* [Azure portal で Windows VM にマネージド データ ディスクを接続する方法](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Azure Storage の基本 - ビデオ](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [コンテナーの作成](/rest/api/storageservices/create-container)
* [AzCopy on Linux を使ったデータの転送](../storage/common/storage-use-azcopy-v10.md)

### <a name="vmware-references"></a>VMware リファレンス

* [vSphere Web Client での分散ポート グループの作成 - ビデオ](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>その他のリファレンス

* [マネージド ディスク上の XFS ボリュームの作成 - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [CentOS 7 で NFS マウントを設定する方法 - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [DHCP サーバーの構成 - Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
