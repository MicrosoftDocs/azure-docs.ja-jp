---
title: Azure で SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクに SAP NetWeaver HA をインストールする | Microsoft Docs
description: SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクに SAP NetWeaver HA をインストールする方法を説明します。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12cfd39cf1358dcef79c3843627f0b45dc688c9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101667950"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Azure で SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクに SAP NetWeaver HA をインストールする

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
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

この記事では、SAP ASCS/SCS インスタンスのクラスタリング用の Windows Server フェールオーバー クラスターとクラスター共有ディスクを使って、Azure に高可用性の SAP システムをインストールして構成する方法について説明します。 [アーキテクチャ ガイド: クラスター共有ディスクを使用した Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスのクラスタリング][sap-high-availability-guide-wsfc-shared-disk]に関するページ記載されているように、"*クラスター共有ディスク*" には次に 2 つの代替手段があります。

- [Azure 共有ディスク](../../disks-shared.md)
- クラスター共有ディスクをシミュレートする、[SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) を使用したミラー化ストレージの作成 

## <a name="prerequisites"></a>前提条件

インストールを始める前に、次のドキュメントを確認してください。

* [アーキテクチャ ガイド: クラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する][sap-high-availability-guide-wsfc-shared-disk]

* [SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP HA 向けに Azure インフラストラクチャを準備する][sap-high-availability-infrastructure-wsfc-shared-disk]

DBMS のセットアップは使用する DBMS システムによって異なるため、この記事では説明はしません。 さまざまな DBMS ベンダーが Azure でサポートする機能を使用して、DBMS に関する高可用性の問題に対処していることを想定しています。 たとえば、SQL Server の Always On またはデータベース ミラーリング、Oracle データベースの Oracle Data Guard などがあります。 この記事では、DBMS の高可用性シナリオについては説明しません。

異なる DBMS サービスが Azure のクラスター化された SAP ASCS または SCS 構成と対話する際に、特別な考慮事項はありません。

> [!NOTE]
> SAP NetWeaver ABAP システム、Java システム、ABAP + Java システムのインストール手順はほとんど同じです。 最も重要な違いは、SAP ABAP システムでは 1 つの ASCS インスタンスを使用することです。 SAP Java システムは 1 つの SCS インスタンスを使用します。 SAP ABAP + Java システムでは、1 つの ASCS インスタンスと、同じ Microsoft フェールオーバー クラスター グループで実行する 1 つの SCS インスタンスを使用します。 各 SAP NetWeaver インストール スタックのインストールの違いについて明確に説明します。 残りの手順は同じであると見なすことができます。  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>高可用性 ASCS/SCS インスタンスでの SAP のインストール

> [!IMPORTANT]
> SIOS を使用して共有ディスクを提供する場合は、ページ ファイルを SIOS DataKeeper ミラー ボリュームに配置しないでください。 ページ ファイルは、既定の場所である Azure Virtual Machines の一時ドライブ D のままにしておいてかまいません。 既にそこにない場合は、Azure Virtual Machines のドライブ D に Windows ページ ファイルを移動します。  

高可用性 ASCS/SCS インスタンスでの SAP のインストールには、以下のタスクが含まれます。

* クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名を作成します。
* 最初のクラスター ノードに SAP をインストールします。
* ASCS/SCS インスタンスの SAP プロファイルを変更します。
* プローブ ポートを追加します。
* Windows ファイアウォールでプローブ ポートを開きます。

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成

1. Windows DNS マネージャーで、ASCS/SCS インスタンスの仮想ホスト名の DNS エントリを作成します。

   > [!IMPORTANT]
   > ASCS/SCS インスタンスの仮想ホスト名に割り当てる IP アドレスは Azure Load Balancer に割り当てた IP アドレスと同じでなければなりません。  

   ![図 1: SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する][sap-ha-guide-figure-3046]

   _SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する_

2. 新しい SAP エンキュー レプリケーション サーバー 2 を使用し、これもクラスター化されたインスタンスである場合、ERS2 の仮想ホスト名も DNS で予約する必要があります。 

   > [!IMPORTANT]
   > ERS2 インスタンスの仮想ホスト名に割り当てる IP アドレスは Azure Load Balancer に割り当てた 2 番目の IP アドレスと同じでなければなりません。    

   ![図 1A:SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する][sap-ha-guide-figure-3046-ers2]

   _SAP ERS2 クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する_

3. 仮想ホスト名に割り当てる IP アドレスを定義するには、 **[DNS マネージャー]**  >  **[ドメイン]** の順に選びます。

   ![図 2:SAP ASCS/SCS クラスター構成の新しい仮想名と TCP/IP アドレス][sap-ha-guide-figure-3047]

   _SAP ASCS/SCS クラスター構成の新しい仮想名と TCP/IP アドレス_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> 最初の SAP クラスター ノードのインストール

1. クラスター ノード A で、最初のクラスター ノード オプションを実行します。次のように選択します。

   * **ABAP システム**:**ASCS** インスタンス番号 **00**
   * **Java システム**:**SCS** インスタンス番号 **01**
   * **ABAP+Java システム**:**ASCS** インスタンス番号 **00** と **SCS** インスタンス番号 **01**

   > [!IMPORTANT]
   > Azure 内部ロード バランサーの負荷分散規則 (Basic SKU を使用する場合) の構成と、選択した SAP インスタンス番号が一致する必要があることに注意してください。

2. SAP の説明に従ってインストール手順を行います。 インストールの開始オプション [First Cluster Node]\(最初のクラスター ノード\) で、構成オプションとして [Cluster Shared Disk]\(クラスターの共有ディスク\) を選択します。

> [!TIP]
> SAP インストール ドキュメントでは、最初の ASCS/SCS クラスター ノードをインストールする方法が説明されています。

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> ASCS/SCS インスタンスの SAP プロファイルの変更

エンキュー レプリケーション サーバー 1 がある場合は、次に示すように SAP プロファイル パラメーター `enque/encni/set_so_keepalive` を追加します。 このプロファイル パラメーターにより、SAP ワーク プロセスとエンキュー サーバー間の接続が長時間にわたってアイドル状態のときに、接続が閉じられるのを防ぐことができます。 ERS2 の場合、SAP パラメーターは必要ありません。 

1. ERS1 を使用する場合は、このプロファイル パラメーターを SAP ASCS/SCS インスタンス プロファイルに追加します。

   ```
   enque/encni/set_so_keepalive = true
   ```

   ERS1 と ERS2 の両方について、`keepalive` OS パラメーターが SAP ノート [1410736](https://launchpad.support.sap.com/#/notes/1410736) の説明に従って設定されていることを確認します。   

2. SAP プロファイル パラメーターの変更を適用するには、SAP ASCS/SCS インスタンスを再起動します。

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> プローブ ポートの追加

内部ロード バランサーのプローブ機能を使用して、クラスター全体の構成が Azure Load Balancer で動作するようにします。 通常、Azure 内部ロード バランサーは、参加している仮想マシン間に受信ワークロードを均等に分散させます。

ただし、アクティブなインスタンスが 1 つだけであるため、一部のクラスター構成では動作しません。 他のインスタンスはパッシブであり、ワークロードを受け付けることができません。 プローブ機能は、Azure 内部ロード バランサーによって、アクティブなインスタンスが検出され、アクティブなインスタンスのみが対象とされる場合に役立ちます。  

> [!IMPORTANT]
> この構成例では、**ProbePort** は 620 **Nr** に設定されます。 番号が **00** の SAP ASCS インスタンスの場合は、620 **00** です。 SAP インスタンス番号と SAP SID に合わせて構成を調整する必要があります。

プローブ ポートを追加するには、次のいずれかのクラスター VM でこの PowerShell モジュールを実行します。

- SAP ASCS/SCS インスタンスの場合 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- クラスター化された ERS2 を使用する場合。 ERS1 については、クラスター化されていないため、プローブ ポートを構成する必要はありません。  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
   ```

 関数 `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` のコードは次のようになります。
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {

    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.

    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 

    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.

    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 

    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.

    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.

    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.

    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 

    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False

    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True

    #> 

        [CmdletBinding()]
        param(

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,

            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,

            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,

            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        )

        BEGIN{}

        PROCESS{
            try{                                      

                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 

                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "

                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

                Write-Output " "

                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 

                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5

                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName

                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "

                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
        }
        END {}
    }

   ```

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Windows ファイアウォール プローブ ポートを開く

両方のクラスター ノードで Windows ファイアウォール プローブ ポートを開きます。 Windows ファイアウォール プローブ ポートを開くには、次のスクリプトを使用します。 環境に合わせて PowerShell 変数を更新してください。  
ERS2 を使用する場合は、ERS2 プローブ ポートのファイアウォール ポートも開く必要があります。  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> データベース インスタンスのインストール

データベース インスタンスをインストールするには、SAP インストール ドキュメントに記載されている手順に従います。

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> 第 2 のクラスター ノードのインストール

第 2 のクラスター ノードをインストールするには、SAP インストール ガイドの手順に従います。

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP プライマリ アプリケーション サーバーのインストール

プライマリ アプリケーション サーバー (PAS) のインスタンス \<SID\>-di-0 を、PAS のホストとして指定した仮想マシンにインストールします。 Azure に対する依存関係はありません。 SIOS を使用する場合、DataKeeper に固有の設定はありません。

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP 追加アプリケーション サーバーのインストール

SAP アプリケーション サーバー インスタンスのホストとして指定したすべての仮想マシンに、SAP 追加アプリケーション サーバー (AAS) をインストールします。 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> SAP ASCS/SCS インスタンス フェールオーバーをテストする

このフェールオーバー テストの概要では、SAP ASCS がノード A でアクティブであることを前提としています。  

1. SAP システムがノード A からノード B に正常にフェールオーバーできることを確認します。次のいずれかの方法を選んで、クラスター ノード A からクラスター ノード B への SAP \<SID\> クラスター グループのフェールオーバーを開始します。
    - フェールオーバー クラスター マネージャー  
    - フェールオーバー クラスター PowerShell

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>

    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. Windows ゲスト オペレーティング システムでクラスター ノード A を再起動します。 これにより、ノード A からノード B への SAP \<SID\> クラスター グループの自動フェールオーバーが開始されます。  
3. Azure Portal からクラスター ノード A を再起動します。 これにより、ノード A からノード B への SAP \<SID\> クラスター グループの自動フェールオーバーが開始されます。  
4. Azure PowerShell を使ってクラスター ノード A を再起動します。 これにより、ノード A からノード B への SAP \<SID\> クラスター グループの自動フェールオーバーが開始されます。

5. 検証
   - フェールオーバー後に、SAP \<SID\> クラスター グループがクラスター ノード B で動作していることを確認します。 

      ![図 8:フェールオーバー クラスター マネージャーで SAP \<SID\> クラスター グループがクラスター ノード B で動作している][sap-ha-guide-figure-5002]

      _フェールオーバー クラスター マネージャーで SAP \<SID\> クラスター グループがクラスター ノード B で動作している_

   - フェールオーバー後に、共有ディスクがクラスター ノード B にマウントされていることを確認します。 
   - フェールオーバー後に、SIOS を使用している場合は、SIOS DataKeeper がクラスター ノード B のソース ボリューム ドライブ S からクラスター ノード A のターゲット ボリューム ドライブ S にデータをレプリケートしていることを確認します。 

      ![図 9:SIOS DataKeeper で、クラスター ノード B からクラスター ノード A にローカル ボリュームをレプリケートする][sap-ha-guide-figure-5003]

      _SIOS DataKeeper で、クラスター ノード B からクラスター ノード A にローカル ボリュームをレプリケートする_