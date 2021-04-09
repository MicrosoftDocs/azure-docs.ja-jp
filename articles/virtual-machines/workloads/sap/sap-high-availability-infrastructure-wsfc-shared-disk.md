---
title: WSFC と共有ディスクを使用した SAP ASCS および SCS 向けの Azure インフラストラクチャ | Microsoft Docs
description: SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクを使って SAP HA 向けに Azure インフラストラクチャを準備する方法を説明します。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4218b4c00b79d78965eaf6e73028e63f52b1ff17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673624"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>SAP ASCS/SCS 用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP HA 向けに Azure インフラストラクチャを準備する

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

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
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows OS][Logo_Windows] Windows


この記事では、SAP ASCS インスタンスのクラスタリングのオプションとして "*クラスター共有ディスク*" を使うことで、Windows フェールオーバー クラスター上に高可用性の SAP ASCS/SCS システムをインストールして構成するための Azure インフラストラクチャを準備する手順について説明します。
"*クラスター共有ディスク*" の 2 つの代替手段については、次のドキュメントを参照してください。

- [Azure 共有ディスク](../../disks-shared.md)
- クラスター化された共有ディスクをシミュレートする、[SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) を使用したミラー化ストレージの作成 

示される構成は、SAP ワークロードの最適なネットワーク待機時間を実現するための [Azure 近接配置グループ (PPG)](./sap-proximity-placement-scenarios.md) に依存します。 このドキュメントでは、データベース層については説明していません。  

> [!NOTE]
> 近接配置グループは、Azure 共有ディスクを使用するための前提条件です。
 

## <a name="prerequisites"></a>前提条件

インストールを始める前に、次の記事を確認してください。

* [アーキテクチャ ガイド: クラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>ASCS VM を作成する

SAP ASCS/SCS クラスターの場合、Azure 可用性セットに 2 つの VM をデプロイします。 VM は同じ近接配置グループにデプロイします。 VM がデプロイされたら、次のようにします。  
- SAP ASCS/SCS インスタンスの Azure 内部ロード バランサーを作成する 
- AD ドメインに Windows VM を追加する

示されるシナリオのホスト名と IP アドレスは次のとおりです。

| ホスト名の役割 | ホスト名 | 静的 IP アドレス | 可用性セット | 近接配置グループ |
| --- | --- | --- |---| ---|
| 最初のクラスター ノードの ASCS/SCS クラスター |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| 2 番目のクラスター ノードの ASCS/SCS クラスター |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| クラスター ネットワーク名 | pr1clust |10.0.0.42 (Win 2016 クラスターの場合 **のみ**) | 該当なし | 該当なし |
| ASCS クラスター ネットワーク名 | pr1-ascscl |10.0.0.43 | 該当なし | 該当なし |
| ERS クラスター ネットワーク名 (ERS2 の場合 **のみ**) | pr1-erscl |10.0.0.44 | 該当なし | 該当なし |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure 内部ロード バランサーを作成する

SAP ASCS、SAP SCS、および新しい SAP ERS2 により、仮想ホスト名と仮想 IP アドレスが使用されます。 Azure 上で仮想 IP アドレスを使用するには、[ロード バランサー](../../../load-balancer/load-balancer-overview.md)が必要です。 [Standard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) の使用を強くお勧めします。 

> [!IMPORTANT]
> フローティング IP は、負荷分散シナリオの NIC セカンダリ IP 構成ではサポートされていません。 詳細については、[Azure Load Balancer の制限事項](../../../load-balancer/load-balancer-multivip-overview.md#limitations)に関する記事を参照してください。 VM に追加の IP アドレスが必要な場合は、2 つ目の NIC をデプロイします。    


次の一覧には、(A)SCS および ERS ロード バランサーの構成が示されています。 SAP ASCS と ERS2 の両方の構成は、同じ Azure ロード バランサーで実行されています。  

**(A)SCS**
- フロントエンドの構成
    - 静的 ASCS/SCS IP アドレス **10.0.0.43**
- バックエンドの構成  
    (A)SCS および ERS クラスターに含める必要があるすべての仮想マシンを追加します。 この例では、VM の **pr1-ascs-0-10** と **pr1-ascs-0-ascs-11** です。
- プローブ ポート
    - ポート 620 **nr** プロトコル (TCP)、間隔 (5)、異常しきい値 (2) の既定のオプションはそのままにしておきます
- 負荷分散規則
    - Standard Load Balancer を使用する場合は、 [HA ポート] を選択します
    - Basic Load Balancer を使用する場合は、次のポートの負荷分散規則を作成します
        - 32 **nr** TCP
        - 36 **nr** TCP
        - 39 **nr** TCP
        - 81 **nr** TCP
        - 5 **nr** 13 TCP
        - 5 **nr** 14 TCP
        - 5 **nr** 16 TCP

    - [アイドル タイムアウト (分)] が最大値の 30 に設定され、[フローティング IP (ダイレクト サーバー リターン)] が有効になっていることを確認します。

**ERS2**

エンキュー レプリケーション サーバー 2 (ERS2) もクラスター化されているため、上記の SAP ASCS/SCS IP に加え、ERS2 仮想 IP アドレスも Azure ILB で構成する必要があります。 このセクションは、エンキュー レプリケーションサーバー 2 アーキテクチャを使用している場合にのみ適用されます。  
- 2 番目のフロントエンドの構成
    - 静的 SAP ERS2 IP アドレス **10.0.0.44**

- バックエンドの構成  
  VM は ILB バックエンド プールに既に追加されています。  

- 2 番目のプローブ ポート
    - ポート 621 **nr**  
    プロトコル (TCP)、間隔 (5)、異常しきい値 (2) の既定のオプションはそのままにしておきます

- 2 番目の負荷分散規則
    - Standard Load Balancer を使用する場合は、 [HA ポート] を選択します
    - Basic Load Balancer を使用する場合は、次のポートの負荷分散規則を作成します
        - 32 **nr** TCP
        - 33 **nr** TCP
        - 5 **nr** 13 TCP
        - 5 **nr** 14 TCP
        - 5 **nr** 16 TCP

    - [アイドル タイムアウト (分)] が最大値の 30 に設定され、[フローティング IP (ダイレクト サーバー リターン)] が有効になっていることを確認します。


> [!TIP]
> [Azure 共有ディスクを使用した SAP ASCS/SCS インスタンス用の WSFC の Azure Resource Manager テンプレート](https://github.com/robotechredmond/301-shared-disk-sap)を使うと、ERS1 で 1 つの SAP SID に Azure 共有ディスクを使用して、インフラストラクチャの準備を自動化することができます。  
> Azure ARM テンプレートにより、2 つの Windows 2019 または 2016 VM が作成され、Azure 共有ディスクが作成されて VM に接続されます。 Azure 内部ロード バランサーも作成され、構成されます。 詳細については、ARM テンプレートを参照してください。 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> ASCS/SCS インスタンスの両方のクラスター ノードにレジストリ エントリを追加する

一定の期間、接続がアイドル状態で、アイドル タイムアウトを超えた場合、Azure Load Balancer によって接続が閉じられる可能性があります。 SAP ワーク プロセスで、最初のエンキュー/デキュー要求の送信が必要になるとすぐに、SAP エンキュー プロセスへの接続が開かれます。 これらの接続が中断されないようにするには、両方のクラスター ノードで TCP/IP KeepAliveTime と KeepAliveInterval の値を変更します。 ERS1 を使用する場合は、この記事の後半で説明されているように、SAP プロファイル パラメーターを追加する必要もあります。
両方のクラスター ノードで、次のレジストリ エントリを変更する必要があります。

- KeepAliveTime
- KeepAliveInterval

| Path| 変数名 | 変数の型  | 値 | ドキュメント |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD (Decimal) |120000 |[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD (Decimal) |120000 |[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


変更を適用するには、両方のクラスター ノードを再起動します。
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> ドメインに Windows VM を追加する
仮想マシンに静的 IP アドレスを割り当てた後、ドメインに仮想マシンを追加します。 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Windows フェールオーバー クラスターをインストールして構成する 

### <a name="install-the-windows-failover-cluster-feature"></a>Windows フェールオーバー クラスター機能をインストールする

クラスター ノードのいずれかでこのコマンドを実行します。

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

機能のインストールが完了したら、両方のクラスター ノードを再起動します。  

### <a name="test-and-configure-windows-failover-cluster"></a>Windows Server フェールオーバー クラスターをテストして構成する 

Windows 2019 では、クラスターにより、Azure で実行されていることが自動的に認識され、クラスター管理 IP の既定のオプションとして、分散ネットワーク名が使用されます。 したがって、任意のクラスター ノードのローカル IP アドレスが使用されます。 そのため、クラスターには専用の (仮想) ネットワーク名は必要ありません。また、Azure 内部ロード バランサーでこの IP アドレスを構成する必要もありません。

詳細については、「[Windows Server 2019 フェールオーバー クラスタリングの新機能](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029)」を参照してください。クラスター ノードのいずれかでこのコマンドを実行します。

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>クラスター クラウド クォーラムを構成する
Windows Server 2016 または 2019 を使用するときは、クラスター クォーラムとして、[Azure クラウド監視](/windows-server/failover-clustering/deploy-cloud-witness)を構成することをお勧めします。

クラスター ノードのいずれかでこのコマンドを実行します。

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>Windows フェールオーバー クラスターのしきい値の調整
 
Windows フェールオーバー クラスターを正しくインストールした後、Azure にデプロイされたクラスターに合わせて、一部のしきい値を調整する必要があります。 変更するパラメーターについては、「[Tuning failover cluster network thresholds](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)」(フェールオーバー クラスター ネットワークのしきい値の調整) をご覧ください。 ASCS/SCS 用の Windows クラスター構成を構築する 2 つの VM が同じサブネットにあることを前提とした場合、次のようにパラメーターの値に変更します。
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

これらの設定はお客様とテストしたものであり、適切な妥協が提供されます。 十分な回復力がある一方で、SAP ワークロードの実際のエラー状態または VM の障害において十分に高速なフェールオーバーも提供されます。  

## <a name="configure-azure-shared-disk"></a>Azure 共有ディスクを構成する
このセクションは、Azure 共有ディスクを使用している場合にのみ適用されます。 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>PowerShell を使用して Azure 共有ディスクを作成および接続する
クラスター ノードのいずれかでこのコマンドを実行します。 リソース グループ、Azure リージョン、SAPSID などの値を調整する必要があります。  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>PowerShell を使用して共有ディスクをフォーマットする
1. ディスク番号を取得します。 クラスター ノードのいずれかでこれらの PowerShell コマンドを実行します。

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. ディスクをフォーマットします。 この例では、ディスク番号 2 です。 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. ディスクがクラスター ディスクとして表示されるようになったことを確認します。  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. クラスターにディスクを登録します。  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS クラスター共有ディスク用の SIOS DataKeeper Cluster Edition
このセクションは、サード パーティ製ソフトウェアの SIOS DataKeeper Cluster Edition を使用して、クラスター共有ディスクをシミュレートするミラー化されたストレージを作成する場合にのみ適用されます。  

これで、Azure に機能する Windows Server フェールオーバー クラスタリング構成が完了しました。 SAP ASCS/SCS インスタンスをインストールするには、共有ディスク リソースが必要です。 オプションの 1 つは、SIOS DataKeeper Cluster Edition を使用することです。これは、共有ディスク リソースを作成するために使用できるサードパーティ製のソリューションです。  

SAP ASCS/SCS クラスター共有ディスク用の SIOS DataKeeper Cluster Edition のインストールには、次のタスクが含まれます。
- 必要に応じて、Microsoft .NET Framework を追加します。 最新の .NET Framework の要件については、[SIOS のドキュメント](https://us.sios.com/products/datakeeper-cluster/) を参照してください 
-  SIOS DataKeeper のインストール
- SIOS DataKeeper を構成する

### <a name="install-sios-datakeeper"></a> SIOS DataKeeper のインストール
クラスターの各ノードに SIOS DataKeeper Cluster Edition をインストールします。 SIOS DataKeeper で仮想共有記憶域を作成するには、同期されたミラーを作成し、クラスター共有記憶域をシミュレートします。

SIOS ソフトウェアをインストールする前に、DataKeeperSvc ドメイン ユーザーを作成します。

> [!NOTE]
> DataKeeperSvc ドメイン ユーザーを、両方のクラスター ノードのローカルの Administrator グループに追加します。
>

1. SIOS ソフトウェアを両方のクラスター ノードにインストールします。

   ![SIOS インストーラー][sap-ha-guide-figure-3030]

   ![図 31:SIOS DataKeeper のインストールの最初のページ][sap-ha-guide-figure-3031]

   _SIOS DataKeeper のインストールの最初のページ_

2. ダイアログ ボックスで **[はい]** を選択します。

   ![図 32:DataKeeper からサービスを無効にすることが通知される][sap-ha-guide-figure-3032]

   _DataKeeper からサービスを無効にすることが通知される_

3. ダイアログ ボックスでは、 **[Domain or Server account]\(ドメインまたはサーバーのアカウント\)** を選択することをお勧めします。

   ![図 33:SIOS DataKeeper のユーザー選択][sap-ha-guide-figure-3033]

   _SIOS DataKeeper のユーザー選択_

4. SIOS DataKeeper 用に作成したドメイン アカウントのユーザー名とパスワードを入力します。

   ![図 34:SIOS DataKeeper インストールのドメイン ユーザー名とパスワードを入力する][sap-ha-guide-figure-3034]

   _SIOS DataKeeper インストールのドメイン ユーザー名とパスワードを入力する_

5. 図 35 に示すように、SIOS DataKeeper インスタンスのライセンス キーをインストールします。

   ![図 35:SIOS DataKeeper ライセンス キーを入力する][sap-ha-guide-figure-3035]

   _SIOS DataKeeper ライセンス キーを入力する_

6. メッセージが表示されたら、仮想マシンを再起動します。

### <a name="configure-sios-datakeeper"></a>SIOS DataKeeper を構成する
両方のノードに SIOS DataKeeper をインストールした後、構成を開始します。 この構成の目的は、各仮想マシンに接続された追加ディスク間で同期データ レプリケーションを実現することです。

1. DataKeeper の管理および構成ツールを起動し、 **[Connect to Server (サーバーに接続)]** を選択します

   ![図 36:SIOS DataKeeper の管理および構成ツール][sap-ha-guide-figure-3036]

   _SIOS DataKeeper の管理および構成ツール_

2. 管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードを入力します。

   ![図 37:管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードの情報を入力する][sap-ha-guide-figure-3037]

   _管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードの情報を入力する_

3. 2 つのノード間のレプリケーション ジョブを作成します。

   ![図 38:レプリケーション ジョブを作成する][sap-ha-guide-figure-3038]

   _レプリケーション ジョブを作成する_

   ウィザードでレプリケーション ジョブの作成手順が示されます。

4. レプリケーション ジョブの名前を定義します。

   ![図 39:レプリケーション ジョブの名前を定義する][sap-ha-guide-figure-3039]

   _レプリケーション ジョブの名前を定義する_

   ![図 40:現在のソース ノードとして使用するノードの基本データを定義する][sap-ha-guide-figure-3040]

   _現在のソース ノードとして使用するノードの基本データを定義する_

5. ターゲット ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義します。

   ![図 41: 現在のターゲット ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義する][sap-ha-guide-figure-3041]

   _現在のターゲット ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義する_

6. 圧縮アルゴリズムを定義します。 この例では、レプリケーション ストリームを圧縮することをお勧めします。 特に再同期の状況では、レプリケーション ストリームを圧縮すると、再同期時間が大幅に短縮されます。 圧縮では仮想マシンの CPU および RAM リソースが使用されます。 圧縮率を大きくするほど、使われる CPU リソースの量も増えます。 この設定は後で調整できます。

7. 確認する必要があるもう 1 つの設定は、レプリケーションを非同期と同期のどちらで行うかです。 SAP ASCS/SCS の構成を保護する場合は、同期レプリケーションを使う必要があります。  

   ![図 42:レプリケーションの詳細を定義する][sap-ha-guide-figure-3042]

   _レプリケーションの詳細を定義する_

8. レプリケーション ジョブによってレプリケートされたボリュームを、Windows Server フェールオーバー クラスター構成に共有ディスクとして示すかどうかを定義します。 SAP ASCS/SCS の構成では、Windows クラスターがレプリケートされたボリュームをクラスター ボリュームとして使用できる共有ディスクとして認識するように、 **[はい]** を選択します。

   ![図 43:[はい] を選択し、レプリケートされたボリュームをクラスター ボリュームとして設定する][sap-ha-guide-figure-3043]

   _**[はい]** を選択し、レプリケートされたボリュームをクラスター ボリュームとして設定する_

   ボリュームが作成された後、DataKeeper の管理および構成ツールではレプリケーション ジョブがアクティブであると表示されます。

   ![図 44:SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリングがアクティブになっている][sap-ha-guide-figure-3044]

   _SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリングがアクティブになっている_

   図 45 のように、フェールオーバー クラスター マネージャーでディスクが DataKeeper ディスクとして表示されるようになりました。

   ![図 45:DataKeeper がレプリケートしたディスクがフェールオーバー クラスター マネージャーに表示される][sap-ha-guide-figure-3045]

   _DataKeeper がレプリケートしたディスクがフェールオーバー クラスター マネージャーに表示される_


## <a name="next-steps"></a>次のステップ

* [SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP NetWeaver HA をインストールする][sap-high-availability-installation-wsfc-shared-disk]