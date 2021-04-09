---
title: Azure の共有ディスクを使用して WSFC 上の SAP ASCS/SCS インスタンスをクラスター化する | Microsoft Docs
description: クラスター共有ディスクを使って Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する方法を説明します。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c903cf06981e1336ae30942775de11d09bb1299b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675358"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Azure のクラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する

> ![Windows OS][Logo_Windows] Windows
>

Windows Server フェールオーバー クラスタリングは、Windows での高可用性の SAP ASCS/SCS インストールと DBMS の基盤です。

フェールオーバー クラスターとは、アプリケーションとサービスの可用性を高めるために連携する、1 + n 台の独立したサーバー (ノード) の 1 つのグループです。 ノード障害が発生した場合、Windows Server フェールオーバー クラスタリングは、アプリケーションとサービスを提供するクラスターを正常な状態で維持するうえで許容できるエラーの数を計算します。 フェールオーバー クラスタリングを実現するために、さまざまなクォーラム モードを選択できます。

## <a name="prerequisites"></a>前提条件
この記事のタスクを始める前に、次の記事を確認してください。

* [SAP NetWeaver のための Azure Virtual Machines 高可用性のアーキテクチャとシナリオ][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Azure での Windows Server フェールオーバー クラスタリング

Azure Virtual Machines を使用した Windows Server フェールオーバー クラスタリングには、追加の構成手順が必要です。 クラスターを構築するときは、SAP ASCS/SCS インスタンスに複数の IP アドレスと仮想ホスト名を設定する必要があります。

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Azure での名前解決とクラスターの仮想ホスト名

Azure クラウド プラットフォームには、フローティング IP アドレスのような仮想 IP アドレスを構成するオプションは用意されていません。 クラウド内のクラスター リソースに到達するために仮想 IP アドレスを設定する別のソリューションが必要となります。 

Azure Load Balancer サービスは、Azure に "*内部ロード バランサー*" を提供します。 内部ロード バランサーでは、クライアントはクラスターの仮想 IP アドレスを使用してクラスターにアクセスします。 

クラスター ノードを含むリソース グループに、内部ロード バランサーをデプロイします。 その後、内部ロード バランサーのプローブ ポートを使って、必要なすべてのポート フォワーディング規則を構成します。 クライアントは仮想ホスト名を使って接続できます。 DNS サーバーがクラスター IP アドレスを解決し、内部ロード バランサーがクラスターのアクティブ ノードへのポート フォワーディングを処理します。

> [!IMPORTANT]
> フローティング IP は、負荷分散シナリオの NIC セカンダリ IP 構成ではサポートされていません。 詳細については、[Azure Load Balancer の制限事項](../../../load-balancer/load-balancer-multivip-overview.md#limitations)に関する記事を参照してください。 VM に追加の IP アドレスが必要な場合は、2 つ目の NIC をデプロイします。  

![図 1: 共有ディスクを使わない Azure の Windows フェールオーバー クラスタリング構成][sap-ha-guide-figure-1001]

_共有ディスクを使用しない Azure での Windows Server フェールオーバー クラスタリング構成_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>クラスター共有ディスクを使う SAP ASCS/SCS HA
Windows では、SAP ASCS/SCS インスタンスには、SAP セントラル サービス、SAP メッセージ サーバー、エンキュー サーバー プロセス、および SAP グローバル ホスト ファイルが含まれます。 SAP グローバル ホスト ファイルは、SAP システム全体のセントラル ファイルを格納します。

SAP ASCS/SCS インスタンスには、次のコンポーネントがあります。

* SAP セントラル サービス:
    * メッセージとエンキュー サーバーの 2 つのプロセス、およびこれら 2 つのプロセスへのアクセスに使われる \<ASCS/SCS virtual host name>。
    * ファイル構造: S:\usr\sap\\&lt;SID&gt;\ASCS/SCS\<instance number\>


* SAP グローバル ホスト ファイル:
  * ファイル構造: S:\usr\sap\\&lt;SID&gt;\SYS\..
  * 次の UNC パスを使ってこれらのグローバルな S:\usr\sap\\&lt;SID&gt;\SYS\... ファイルにアクセスできるようにする sapmnt ファイル共有。

    \\\\&lt;ASCS/SCS 仮想ホスト名\>\sapmnt\\&lt;SID&gt;\SYS\..


![図 2:プロセス、ファイル構造、および SAP ASCS/SCS インスタンスのグローバル ホスト sapmnt ファイル共有][sap-ha-guide-figure-8001]

_プロセス、ファイル構造、および SAP ASCS/SCS インスタンスのグローバル ホスト sapmnt ファイル共有_

高可用性の設定では、SAP ASCS/SCS インスタンスをクラスター化します。 "*クラスター化された共有ディスク*" (この例ではドライブ S) を使って、SAP ASCS/SCS ファイルと SAP グローバル ホスト ファイルを配置します。

![図 3:共有ディスクを使う SAP ASCS/SCS HA のアーキテクチャ][sap-ha-guide-figure-8002]

_共有ディスクを使う SAP ASCS/SCS HA のアーキテクチャ_


エンキュー サーバー レプリケーション 1 アーキテクチャの場合:
* 同じ \<ASCS/SCS virtual host name> が、SAP メッセージ プロセスとエンキュー サーバー プロセスへのアクセス、および sapmnt ファイル共有経由での SAP グローバル ホスト ファイルへのアクセスに使われます。
* 同じクラスター共有ディスク ドライブ S が、これらの間で共有されます。  

エンキュー サーバー レプリケーション 2 アーキテクチャの場合: 
* 同じ \<ASCS/SCS virtual host name> が、SAP メッセージ サーバー プロセスへのアクセス、および sapmnt ファイル共有経由での SAP グローバル ホスト ファイルへのアクセスに使われます。
* 同じクラスター共有ディスク ドライブ S が、これらの間で共有されます。
* エンキュー サーバー プロセスにアクセスするための別の \<ERS virtual host name> があります。  

![図 4:共有ディスクを使う SAP ASCS/SCS HA のアーキテクチャ][sap-ha-guide-figure-8003]

_共有ディスクを使う SAP ASCS/SCS HA のアーキテクチャ_

#### <a name="shared-disk-and-enqueue-replication-server"></a>共有ディスクとエンキュー レプリケーション サーバー 

1. 共有ディスクは、エンキュー レプリケーション サーバー (ERS) インスタンスが以下を満たす場合に、エンキュー サーバー レプリケーション 1 アーキテクチャでサポートされます。   

   - クラスター化されていない
   - `localhost` 名を使用する
   - 各クラスター ノードのローカル ディスクにデプロイされている

2. 共有ディスクは、エンキュー レプリケーション サーバー 2 (ERS2) インスタンスが以下を満たす場合に、エンキュー サーバー レプリケーション 2 アーキテクチャでもサポートされます。  

   - クラスター化されている
   - 専用の仮想/ネットワーク ホスト名を使用する
   - (A)SCS IP アドレスに加えて、Azure 内部ロード バランサーで構成するための ERS 仮想ホスト名の IP アドレスを必要とする
   - 各クラスター ノードの **ローカル ディスク** にデプロイされるため、共有ディスクを必要としない

   > [!TIP]
   > エンキュー レプリケーション サーバー 1 と 2 (ERS1 と ERS2) の詳細については、次を参照してください。  
   > [Microsoft フェールオーバー クラスターでのエンキュー レプリケーション サーバー](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [フェールオーバー クラスター環境での新しいエンキュー レプリケーター](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>SAP ワークロード用の Azure の共有ディスクのオプション

Azure の Windows フェールオーバー クラスターの共有ディスクには、次の 2 つのオプションがあります。

- [Azure 共有ディスク](../../disks-shared.md) - Azure マネージド ディスクを複数の VM に同時に接続できるようにする機能。 
- サード パーティ製ソフトウェアの [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster) を使用して、クラスター共有記憶域をシミュレートするミラー化された記憶域を作成する。 

共有ディスクのテクノロジを選択するときは、次の点に注意してください。

**SAP ワークロード用の Azure 共有ディスク**
- 追加のソフトウェアを維持して運用する必要なく、Azure マネージド ディスクを複数の VM に同時に接続することができます。 
- 1 つの記憶域クラスターで 1 つの Azure 共有ディスクを操作します。 これは SAP ソリューションの信頼性に影響を与えます。
- 現在サポートされているデプロイは、可用性セット内の Azure 共有 Premium ディスクを使用するものだけです。 Azure 共有ディスクはゾーン デプロイではサポートされていません。     
- [Premium SSD の範囲](../../disks-shared.md#disk-sizes)に指定されている最小ディスク サイズで Azure Premium ディスクをプロビジョニングして、必要な数の VM に同時に接続できるようにします (通常、SAP ASCS Windows フェールオーバー クラスターの場合は 2 つ)。 
- Azure 共有 Ultra Disk は、可用性セットのデプロイまたはゾーン デプロイをサポートしていないため、SAP ワークロードではサポートされません。  
 
**SIOS**
- SIOS ソリューションは、2 つのディスク間のリアルタイムの同期データ レプリケーションを実現します
- SIOS ソリューションでは 2 つのマネージド ディスクを使用し、可用性セットまたは可用性ゾーンのいずれかを使用している場合、マネージド ディスクは異なる記憶域クラスターに配置されます。 
- 可用性ゾーンでのデプロイはサポートされています
- サードパーティのソフトウェアをインストールして運用する必要があります。ソフトウェアは追加で購入する必要があります

### <a name="shared-disk-using-azure-shared-disk"></a>Azure 共有ディスクを使用した共有ディスク

Microsoft では、共有ディスク オプションを使用して SAP ASCS/SCS 高可用性を実装するために使用できる [Azure 共有ディスク](../../disks-shared.md)を提供しています。

#### <a name="prerequisites-and-limitations"></a>前提条件と制限事項

現時点では、SAP ASCS/SCS インスタンスの Azure 共有ディスクとして Azure Premium SSD ディスクを使用できます。 次の制限事項が現在適用されます。

-  [Azure Ultra Disk](../../disks-types.md#ultra-disk) は、SAP ワークロード用の Azure 共有ディスクとしてはサポートされていません。 現時点では、Azure Ultra Disk を可用性セット内で使用して、Azure VM を配置することはできません
-  Premium SSD ディスクを使用した [Azure 共有ディスク](../../disks-shared.md)は、可用性セット内の VM でのみサポートされています。 Availability Zones のデプロイではサポートされていません。 
-  Azure 共有ディスクの値 [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) によって、その共有ディスクを使用できるクラスター ノードの数が決まります。 通常、SAP ASCS/SCS インスタンスには、Windows フェールオーバー クラスターに 2 つのノードを構成するため、`maxShares` の値は 2 に設定する必要があります。
-  すべての SAP ASCS/SCS クラスター VM が、同じ [Azure 近接通信配置グループ](../../windows/proximity-placement-groups.md)にデプロイされる必要があります。   
   Windows クラスター VM を、PPG を使用せずに Azure 共有ディスクがある可用性セット内にデプロイすることはできますが、PPG を使用すると Azure 共有ディスクとクラスター VM の物理的近距離を確保できるため、VM とストレージ層の間の待機時間が短くなります。    

Azure 共有ディスクの制限事項の詳細については、Azure 共有ディスクのドキュメントの「[制限事項](../../disks-shared.md#limitations)」セクションを十分注意して参照してください。

> [!IMPORTANT]
> Azure 共有ディスクを使用して SAP ASCS/SCS Windows フェールオーバー クラスターをデプロイする場合は、デプロイが 1 つの記憶域クラスター内の単一の共有ディスクを使用して動作することに注意してください。 SAP ASCS/SCS インスタンスは、Azure 共有ディスクがデプロイされている記憶域クラスターで問題が発生した場合に影響を受けます。    

> [!TIP]
> SAP デプロイを計画するときの重要な考慮事項については、[SAP Netweaver on Azure の計画ガイド](./planning-guide.md)および[SAP ワークロード用の Azure Storage ガイド](./planning-guide-storage.md)に関する記事を参照してください。

### <a name="supported-os-versions"></a>サポートされている OS のバージョン

Windows Server 2016 と 2019 の両方がサポートされています (最新のデータ センター イメージを使用してください)。

次の理由から、**Windows Server 2019 Datacenter** を使用することを強くお勧めします。
- Windows 2019 フェールオーバー クラスター サービスは Azure に対応しています
- Azure ホスト メンテナンスの統合と認識が追加され、Azure のスケジュール イベントを監視することでエクスペリエンスが向上しました。
- 分散ネットワーク名を使用することができます (これは既定のオプションです)。 そのため、クラスター ネットワーク名に専用の IP アドレスを設定する必要がありません。 また、この IP アドレスを Azure 内部ロード バランサーで構成する必要もありません。 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>SIOS DataKeeper を使う Azure の共有ディスク

共有ディスクの代わりに、サード パーティ製ソフトウェアの SIOS DataKeeper Cluster Edition を使用して、クラスター共有記憶域をシミュレートするミラー化された記憶域を作成できます。 SIOS ソリューションは、リアルタイムの同期データ レプリケーションを実現します。

クラスターの共有ディスク リソースを作成するには:

1. Windows クラスター構成内の各仮想マシンに追加ディスクを接続します。
2. 両方の仮想マシン ノードで、SIOS DataKeeper Cluster Edition を実行します。
3. ソース仮想マシンの追加ディスク接続ボリュームの内容をターゲット仮想マシンの追加ディスク接続ボリュームにミラー化するように SIOS DataKeeper Cluster Edition を構成します。 SIOS DataKeeper は、ソースとターゲットのローカル ボリュームを抽象化し、1 つの共有ディスクとして Windows フェールオーバー クラスタリングに提示します。

詳細については、[SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/) を参照してください。

![図 5:SIOS DataKeeper を使用する Azure での Windows Server フェールオーバー クラスタリング構成][sap-ha-guide-figure-1002]

_SIOS DataKeeper を使用する Azure での Windows フェールオーバー クラスタリング構成_

> [!NOTE]
> SQL Server のような一部の DBMS 製品では、高可用性のために共有ディスクは必要ありません。 SQL Server Always On は、DBMS のデータとログ ファイルを、クラスター ノードのローカル ディスクから別のクラスター ノードのローカル ディスクにレプリケートします。 その場合、Windows クラスター構成に共有ディスクは不要です。
>

## <a name="next-steps"></a>次のステップ

* [SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP HA 向けに Azure インフラストラクチャを準備する][sap-high-availability-infrastructure-wsfc-shared-disk]

* [SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクに SAP NetWeaver HA をインストールする][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP マルチ SID 高可用性構成)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md