---
title: SAP ASCS/SCS 用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP HA 向けに Azure インフラストラクチャを準備する | Microsoft Docs
description: SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクを使って SAP HA 向けに Azure インフラストラクチャを準備する方法を説明します。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b4e107da9d8e5019ba51769d283f3faa34839380
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709256"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>SAP ASCS/SCS 用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP HA 向けに Azure インフラストラクチャを準備する

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

この記事では、SAP ASCS インスタンスのクラスタリングのオプションとして "*クラスター共有ディスク*" を使うことで、Windows フェールオーバー クラスター上に高可用性の SAP システムをインストールして構成するための Azure インフラストラクチャを準備する手順を説明します。

## <a name="prerequisites"></a>前提条件

インストールを始める前に、次の記事を確認してください。

* [アーキテクチャ ガイド: クラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Architectural Template 1 のインフラストラクチャを準備する
SAP 用の Azure Resource Manager テンプレートを使用すると、必要なリソースを簡単にデプロイできます。

Azure Resource Manager の 3 層テンプレートは、高可用性のシナリオもサポートします。 たとえば、Architectural Template 1 には 2 つのクラスターがあります。 各クラスターは、SAP ASCS/SCS および DBMS の SAP 単一障害点です。

この記事で説明しているサンプル シナリオの Azure Resource Manager テンプレートは、以下で入手できます。

* [Azure Marketplace イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Managed Disks を使った Azure Marketplace イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [カスタム イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Managed Disks を使ったカスタム イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Architectural Template 1 のインフラストラクチャを準備するには

- Azure Portal の **[パラメーター]** ウィンドウにある **[SYSTEMAVAILABILITY]\(システムの可用性\)** ボックスで、 **[HA]\(高可用性\)** を選びます。

  ![図 1:SAP 高可用性 Azure Resource Manager パラメーターを設定する][sap-ha-guide-figure-3000]

_**図 1:** SAP 高可用性 Azure Resource Manager パラメーターを設定する_


  テンプレートによって次のものが作成されます。

  * **仮想マシン**:
    * SAP アプリケーション サーバーの仮想マシン: \<SAP システムの SID\>-di-\<番号\>
    * ASCS/SCS クラスターの仮想マシン: \<SAP システムの SID\>-ascs-\<番号\>
    * DBMS クラスター: \<SAP システムの SID\>-db-\<番号\>

  * **すべての仮想マシンのネットワーク カードと、関連付けられている IP アドレス**:
    * \<SAP システムの SID\>-nic-di-\<番号\>
    * \<SAP システムの SID\>-nic-ascs-\<番号\>
    * \<SAP システムの SID\>-nic-db-\<番号\>

  * **Azure ストレージ アカウント (非管理対象ディスクのみ)** :

  * 次のものの**可用性グループ**:
    * SAP アプリケーション サーバーの仮想マシン: \<SAP システムの SID\>-avset di
    * SAP ASCS/SCS クラスターの仮想マシン: \<SAP システムの SID\>-avset-ascs
    * DBMS クラスターの仮想マシン: \<SAP システムの SID\>-avset-db

  * **Azure 内部ロード バランサー**:
    * ASCS/SCS インスタンスのすべてのポートと IP アドレス: \<SAP システムの SID\>-lb-ascs
    * SQL Server DBMS のすべてのポートと IP アドレス: \<SAP システムの SID\>-lb-db

  * **[ネットワーク セキュリティ グループ]** : \<SAP システムの SID\>-nsg-ascs-0  
    * \<SAP システムの SID\>-ascs-0 仮想マシンに対して開かれた外部リモート デスクトップ プロトコル (RDP) ポート

> [!NOTE]
> ネットワーク カードと Azure 内部ロード バランサーのすべての IP アドレスは、既定では動的です。 これらを、静的な IP アドレスに変更します。 この方法については、この記事の後の方で説明します。
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> 運用環境で使用するための企業ネットワーク接続 (クロスプレミス) を使用する仮想マシンのデプロイ
運用 SAP システムの場合、Azure VPN Gateway または Azure ExpressRoute を使って、[企業ネットワーク接続 (クロスプレミス)][planning-guide-2.2] を使う Azure Virtual Machines をデプロイします。

> [!NOTE]
> Azure Virtual Network インスタンスを使用できます。 仮想ネットワークとサブネットは既に作成および準備されています。
>
>

1. Azure Portal の **[パラメーター]** ウィンドウにある **[NEWOREXISTINGSUBNET]\(新規または既存のサブネット\)** ボックスで、 **[existing]\(既存\)** を選びます。
2. **[SUBNETID]\(サブネット ID\)** ボックスに、Azure 仮想マシンのデプロイ先となる準備済みの Azure ネットワークのサブネット ID の完全な文字列を追加します。
3. すべての Azure ネットワーク サブネットの一覧を取得するには、次の PowerShell コマンドを実行します。

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **[ID]** フィールドにサブネット ID の値が表示されます。
4. すべてのサブネット ID 値の一覧を取得するには、次の PowerShell コマンドを実行します。

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   サブネット ID は次のようになります。

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> テストおよびデモ用のクラウドのみの SAP インスタンスのデプロイ
クラウドのみのデプロイメント モデルで高可用性 SAP システムをデプロイすることができます。 この種類のデプロイは、主として、デモおよびテストのユース ケースに適しています。 運用環境での使用には適していません。

- Azure Portal の **[パラメーター]** ウィンドウにある **[NEWOREXISTINGSUBNET]\(新規または既存のサブネット\)** ボックスで、 **[new]\(新規\)** を選びます。 **[SUBNETID]** フィールドは空白のままにしておきます。

  SAP Azure Resource Manager テンプレートは、Azure Virtual Network とサブネットを自動的に作成します。

> [!NOTE]
> また、同じ Azure Virtual Network インスタンス内に Active Directory と DNS サービス用の専用仮想マシンを少なくとも 1 つデプロイする必要があります。 テンプレートではこれらの仮想マシンは作成されません。
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Architectural Template 2 のインフラストラクチャを準備する

この Azure Resource Manager テンプレートを SAP に使うと、SAP Architectural Template 2 に必要なインフラストラクチャ リソースのデプロイを簡略化するのに役立ちます。

このデプロイ シナリオの Azure Resource Manager テンプレートは以下で入手できます。

* [Azure Marketplace イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Managed Disks を使った Azure Marketplace イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [カスタム イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Managed Disks を使ったカスタム イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Architectural Template 3 のインフラストラクチャを準備する

マルチ SID 向けにインフラストラクチャを準備し、SAP を構成することができます。 たとえば、追加の SAP ASCS/SCS インスタンスを "*既存の*" クラスター構成に追加できます。 詳しくは、[Azure Resource Manager で SAP マルチ SID 構成を作成するために既存のクラスター構成に追加の SAP ASCS/SCS インスタンスを構成する][sap-ha-multi-sid-guide]方法に関するページをご覧ください。

新しいマルチ SID クラスターを作成する場合は、GitHub のマルチ SID [クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates)を使用できます。

新しいマルチ SID クラスターを作成するには、次の 3 つのテンプレートをデプロイする必要があります。

* [ASCS/SCS テンプレート](#ASCS-SCS-template)
* [データベース テンプレート](#database-template)
* [アプリケーション サーバー テンプレート](#application-servers-template)

以降のセクションでは、テンプレートと、テンプレートで指定する必要があるパラメーターの詳細について説明します。

### <a name="ASCS-SCS-template"></a> ASCS/SCS テンプレート

ASCS/SCS テンプレートは、複数の ASCS/SCS インスタンスをホストする Windows Server フェールオーバー クラスターの作成に使用できる 2 つの仮想マシンをデプロイします。

ASCS/SCS マルチ SID テンプレートを設定するには、[ASCS/SCS マルチ SID テンプレート][sap-templates-3-tier-multisid-xscs-marketplace-image] or [ASCS/SCS multi-SID template by using Managed Disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md] で、次のパラメーターの値を入力します。

- **[リソース プレフィックス]** : リソース プレフィックスを設定します。これは、デプロイ中に作成されるすべてのリソースのプレフィックスとして使われます。 リソースは 1 つの SAP システムのみに属するわけではないため、リソースのプレフィックスは 1 つの SAP システムの SID ではありません。  プレフィックスは、3 から 6 文字でなければなりません。
- **[スタックの種類]** :SAP システムのスタックの種類を選びます。 スタックの種類に応じて、Azure Load Balancer には、SAP システムごとに 1 つ (ABAP または Java のみ) または 2 つ (ABAP+ Java) のプライベート IP アドレスがあります。
- **[OS Type]\(OS の種類\)** :仮想マシンのオペレーティング システムを選びます。
- **[SAP システム数]** : このクラスターにインストールする SAP システムの数を選択します。
- **[System Availability]\(システムの可用性\)** : **[HA]** を選択します。
- **[管理ユーザー名] と [管理パスワード]** :マシンへのサインインに使用できる新しいユーザーを作成します。
- **[New Or Existing Subnet]\(新規または既存のサブネット\)** : 新しい仮想ネットワークとサブネットを作成するか、既存のサブネットを使うかを設定します。 オンプレミス ネットワークに接続している仮想ネットワークが既にある場合は、 **[existing (既存)]** を選択します。
- **[Subnet Id]\(サブネット ID\)** : VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 ID は、通常、次のようになります。

  /subscriptions/\<サブスクリプション ID\>/resourceGroups/\<リソース グループ名\>/providers/Microsoft.Network/virtualNetworks/\<仮想ネットワーク名\>/subnets/\<サブネット名\>

テンプレートは 1 つの Azure Load Balancer インスタンスをデプロイし、これにより、複数の SAP システムがサポートされます。

- ASCS インスタンスは、インスタンス番号 00、10、20 などで構成されます。
- SCS インスタンスは、インスタンス番号 01、11、21 などで構成されます。
- ASCS Enqueue Replication Server (ERS) (Linux のみ) インスタンスは、インスタンス番号 02、12、22 などで構成されます。
- SCS ERS (Linux のみ) インスタンスは、インスタンス番号 03、13、23 などで構成されます。

ロード バランサーには、1 つの VIP (Linux の場合は 2 つ) が含まれています。1 つは ASCS/SCS 用の VIP、1 つは ERS 用の VIP (Linux のみ) です。

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> SAP ASCS/SCS のポート
次の一覧には、すべての負荷分散規則が含まれています (x は、1、2、3 などの SAP システムの番号です)。
- すべての SAP システム用の Windows 固有のポート:445、5985
- ASCS ポート (インスタンス番号 x0):32x0、36x0、39x0、81x0、5x013、5x014、5x016
- SCS ポート (インスタンス番号 x1):32x1、33x1、39x1、81x1、5x113、5x114、5x116
- Linux 上の ASCS ERS ポート (インスタンス番号 x2):33x2、5x213、5x214、5x216
- Linux 上の SCS ERS ポート (インスタンス番号 x3):33x3、5x313、5x314、5x316

ロード バランサーは、次のプローブ ポートを使うように構成されます (x は 1、2、3 などの SAP システムの番号です)。
- ASCS/SCS 内部ロード バランサー プローブ ポート:620x0
- ERS 内部ロード バランサー プローブ ポート (Linux のみ):621x2

### <a name="database-template"></a> データベース テンプレート

データベース テンプレートは、1 つの SAP システムのリレーショナル データベース管理システム (RDBMS) のインストールに使用できる 1 つまたは 2 つの仮想マシンをデプロイします。 たとえば、5 つの SAP システムに ASCS/SCS テンプレートをデプロイする場合は、このテンプレートを 5 回デプロイする必要があります。

データベース マルチ SID テンプレートを設定するには、[データベース マルチ SID テンプレート][sap-templates-3-tier-multisid-db-marketplace-image] or [database multi-SID template by using Managed Disks][sap-templates-3-tier-multisid-db-marketplace-image-md] で、次のパラメーターの値を入力します。

- **[Sap System Id]\(SAP システム ID\)** : インストールする SAP システムの SAP システム ID を入力します。 この ID は、デプロイされるリソースのプレフィックスとして使われます。
- **[OS Type]\(OS の種類\)** :仮想マシンのオペレーティング システムを選びます。
- **[Dbtype]\(データベースの種類\)** : クラスターにインストールするデータベースの種類を選びます。 Microsoft SQL Server をインストールする場合は、 **[SQL]** を選択します。 仮想マシンに SAP HANA をインストールする予定の場合は、 **[HANA]** を選択します。 正しいオペレーティング システムの種類を選択したことを確認します。 SQL の場合は **Windows** を選び、HANA の場合は Linux ディストリビューションを選びます。 仮想マシンに接続されている Azure Load Balancer は、選んだデータベースの種類をサポートするように構成されます。
  * **[SQL]** : ロード バランサーは、ポート 1433 で負荷分散を行います。 SQL Server Always On セットアップにはこのポートを使ってください。
  * **[HANA]** : ロード バランサーは、ポート 35015 および 35017 で負荷分散を行います。 インスタンス番号が **50** の SAP HANA をインストールしてください。
  ロード バランサーはプローブ ポート 62550 を使用します。
- **[Sap System Size]\(SAP システムのサイズ\)** :新しいシステムで提供する SAPS の数を設定します。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
- **[System Availability]\(システムの可用性\)** : **[HA]** を選択します。
- **[管理ユーザー名] と [管理パスワード]** :マシンへのサインインに使用できる新しいユーザーを作成します。
- **[Subnet Id]\(サブネット ID\)** : ASCS/SCS テンプレートのデプロイ時に使ったサブネットの ID または ASCS/SCS テンプレートのデプロイの一部として作成されたサブネットの ID を入力します。

### <a name="application-servers-template"></a> アプリケーション サーバー テンプレート

アプリケーション サーバー テンプレートは、1 つの SAP システムの SAP アプリケーション サーバー インスタンスとして使用できる 2 つ以上の仮想マシンをデプロイします。 たとえば、5 つの SAP システムに ASCS/SCS テンプレートをデプロイする場合は、このテンプレートを 5 回デプロイする必要があります。

アプリケーション サーバー マルチ SID テンプレートを設定するには、[アプリケーション サーバー マルチ SID テンプレート][sap-templates-3-tier-multisid-apps-marketplace-image] or [application servers multi-SID template  by using Managed Disks][sap-templates-3-tier-multisid-apps-marketplace-image-md] で、次のパラメーターの値を入力します。

  -  **[Sap System Id]\(SAP システム ID\)** : インストールする SAP システムの SAP システム ID を入力します。 この ID は、デプロイされるリソースのプレフィックスとして使われます。
  -  **[OS Type]\(OS の種類\)** :仮想マシンのオペレーティング システムを選びます。
  -  **[Sap System Size]\(SAP システムのサイズ\)** :新しいシステムで提供する SAPS の数です。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
  -  **[System Availability]\(システムの可用性\)** : **[HA]** を選択します。
  -  **[管理ユーザー名] と [管理パスワード]** :マシンへのサインインに使用できる新しいユーザーを作成します。
  -  **[Subnet Id]\(サブネット ID\)** : ASCS/SCS テンプレートのデプロイ時に使ったサブネットの ID または ASCS/SCS テンプレートのデプロイの一部として作成されたサブネットの ID を入力します。


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure Virtual Network
このガイドの例では、Azure Virtual Network インスタンスのアドレス空間は 10.0.0.0/16 です。 Subnet という名前のサブネットが 1 つあり、そのアドレス範囲は 10.0.0.0/24 です。 すべての仮想マシンと内部ロード バランサーは、この仮想ネットワークにデプロイされます。

> [!IMPORTANT]
> ゲスト オペレーティング システム内ではネットワーク設定をいっさい変更しないでください。 これには、IP アドレス、DNS サーバー、およびサブネットが含まれます。 ネットワークの設定はすべて Azure で構成します。 動的ホスト構成プロトコル (DHCP) サービスが設定を他に伝達します。
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP アドレス

必要な DNS IP アドレスを設定するには、次の手順を実行します。

1. Azure Portal の **[DNS サーバー]** ウィンドウで、仮想ネットワークの **[DNS サーバー]** オプションが **[カスタム DNS]** に設定されていることを確認します。
2. ネットワークの種類に基づいて設定を選択します。 詳細については、次のリソースを参照してください。
   * [企業ネットワーク接続 (クロスプレミス)][planning-guide-2.2]:オンプレミスの DNS サーバーの IP アドレスを追加します。  
   Azure で実行されている仮想マシンにオンプレミスの DNS サーバーを拡張できます。 その場合、DNS サービスを実行する Azure Virtual Machines の IP アドレスを追加できます。
   * Azure 内の分離された VM デプロイの場合:DNS サーバーとして機能する別の仮想マシンを同じ Virtual Network インスタンスにデプロイします。 DNS サービスを実行するように設定した Azure Virtual Machines の IP アドレスを追加します。

   ![図 2:Azure Virtual Network 用に DNS サーバーを構成する][sap-ha-guide-figure-3001]

   _**図 2:** Azure Virtual Network 用に DNS サーバーを構成する_

   > [!NOTE]
   > DNS サーバーの IP アドレスを変更した場合、変更を適用して新しい DNS サーバーに反映するには、Azure Virtual Machines を再起動する必要があります。
   >
   >

このガイドの例では、DNS サービスは次の Windows 仮想マシンにインストールされ、構成されています。

| 仮想マシンの役割 | 仮想マシンのホスト名 | ネットワーク カード名 | 静的 IP アドレス |
| --- | --- | --- | --- |
| 第 1 の DNS サーバー |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| 第 2 の DNS サーバー |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> クラスター化された SAP ASCS/SCS インスタンスとクラスター化された DBMS インスタンスのホスト名と静的 IP アドレス

オンプレミスのデプロイの場合、次の予約されたホスト名と IP アドレスが必要です。

| 仮想ホスト名の役割 | 仮想ホスト名 | 仮想静的 IP アドレス |
| --- | --- | --- |
| SAP ASCS/SCS の第 1 のクラスター仮想ホスト名 (クラスター管理用) |pr1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS インスタンスの仮想ホスト名 |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS の第 2 のクラスター仮想ホスト名 (クラスター管理用) |pr1-dbms-vir |10.0.0.32 |

クラスターを作成するときに、クラスター自体を管理する仮想ホスト名 pr1-ascs-vir と pr1-dbms-vir および関連する IP アドレスを作成します。 その方法については、「[クラスター構成内のクラスター ノードの収集][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]」を参照してください。

他の 2 つの仮想ホスト名 pr1-ascs-sap と pr1-dbms-sap および関連する IP アドレスは、DNS サーバーで手動作成できます。 クラスター化された SAP ASCS/SCS インスタンスおよびクラスター化された DBMS インスタンスは、これらのリソースを使用します。 その方法については、「[クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成][sap-ha-guide-9.1.1]」を参照してください。

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> SAP 仮想マシンの静的 IP アドレスの設定
クラスターで使用する仮想マシンをデプロイした後は、すべての仮想マシンに静的 IP アドレスを設定する必要があります。 これは、ゲスト オペレーティング システムではなく、Azure Virtual Network の構成で行います。

1. Azure Portal で、 **[リソース グループ]**  >  **[ネットワーク カード]**  >  **[設定]**  >  **[IP アドレス]** の順に選択します。
2. **[IP アドレス]** ウィンドウの **[割り当て]** で **[静的]** を選びます。 **[IP アドレス]** ボックスに、使用する IP アドレスを入力します。

   > [!NOTE]
   > ネットワーク カードの IP アドレスを変更する場合は、Azure Virtual Machines を再起動して変更を適用する必要があります。  
   >
   >

   ![図 3:各仮想マシンのネットワーク カードの静的 IP アドレスを設定する][sap-ha-guide-figure-3002]

   _**図 3:** 各仮想マシンのネットワーク カードの静的 IP アドレスを設定する_

   すべてのネットワーク インターフェイス、つまり Active Directory または DNS サービスに使う仮想マシンを含むすべての仮想マシンについて、この手順を繰り返します。

このガイドの例では、次の仮想マシンと静的 IP アドレスを使用します。

| 仮想マシンの役割 | 仮想マシンのホスト名 | ネットワーク カード名 | 静的 IP アドレス |
| --- | --- | --- | --- |
| 1 つ目の SAP アプリケーション サーバー インスタンス |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| 2 つ目の SAP アプリケーション サーバー インスタンス |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| 最後の SAP アプリケーション サーバー インスタンス |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| ASCS/SCS インスタンスの第 1 のクラスター ノード |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| ASCS/SCS インスタンスの第 2 のクラスター ノード |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| DBMS インスタンスの第 1 のクラスター ノード |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| DBMS インスタンスの第 2 のクラスター ノード |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Azure 内部ロード バランサーの静的 IP アドレスの設定

SAP Azure Resource Manager テンプレートでは、SAP ASCS/SCS インスタンス クラスターと DBMS クラスターで使用される Azure 内部ロード バランサーが作成されます。

> [!IMPORTANT]
> SAP ASCS/SCS の仮想ホスト名の IP アドレスは、SAP ASCS/SCS 内部ロード バランサー pr1-lb-ascs の IP アドレスと同じです。
> DBMS の仮想名の IP アドレスは、DBMS 内部ロード バランサー pr1-lb-dbms の IP アドレスと同じです。
>
>

Azure 内部ロード バランサーの静的 IP アドレスを設定するには

1. 初期デプロイでは、内部ロード バランサーの IP アドレスが**動的**に設定されます。 Azure Portal の **[IP アドレス]** ウィンドウの **[割り当て]** で **[静的]** を選びます。
2. 内部ロード バランサー **pr1-lb-ascs** の IP アドレスを、SAP ASCS/SCS インスタンスの仮想ホスト名の IP アドレスに設定します。
3. 内部ロード バランサー **pr1-lb-dbms** の IP アドレスを、DBMS インスタンスの仮想ホスト名の IP アドレスに設定します。

   ![図 4:SAP ASCS/SCS インスタンスの内部ロード バランサーの静的 IP アドレスを設定する][sap-ha-guide-figure-3003]

   _**図 4:** SAP ASCS/SCS インスタンスの内部ロード バランサーの静的 IP アドレスを設定する_

この例で使用する 2 つの Azure 内部ロード バランサーと静的 IP アドレスは次のとおりです。

| Azure 内部ロード バランサーの役割 | Azure 内部ロード バランサーの名前 | 静的 IP アドレス |
| --- | --- | --- |
| SAP ASCS/SCS インスタンスの内部ロード バランサー |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS の内部ロード バランサー |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則

SAP Azure Resource Manager テンプレートによって、必要なポートが作成されます。
* 既定のインスタンス番号が 00 である ABAP ASCS インスタンス
* 既定のインスタンス番号が 01 である Java SCS インスタンス

SAP ASCS/SCS インスタンスをインストールするときは、ABAP ASCS インスタンスには既定のインスタンス番号 00 を使用し、Java SCS インスタンスには既定のインスタンス番号 01 を使用する必要があります。

次に、SAP NetWeaver ポートに必要な内部負荷分散エンドポイントを作成します。

必要な内部負荷分散エンドポイントを作成するには、まず、SAP NetWeaver ABAP ASCS ポート用に次の負荷分散エンドポイント作成します。

| サービス/負荷分散規則名 | 既定のポート番号 | (インスタンス番号が 00 の ASCS インスタンス) (インスタンス番号が 10 の ERS) の具体的なポート |
| --- | --- | --- |
| エンキュー サーバー/*lbrule3200* |32\<インスタンス番号\> |3200 |
| ABAP メッセージ サーバー/*lbrule3600* |36\<インスタンス番号\> |3600 |
| 内部 ABAP メッセージ/*lbrule3900* |39\<インスタンス番号\> |3900 |
| メッセージ サーバー HTTP/*Lbrule8100* |81\<インスタンス番号\> |8100 |
| SAP 起動サービス ASCS HTTP/*Lbrule50013* |5\<インスタンス番号\>13 |50013 |
| SAP 起動サービス ASCS HTTPS/*Lbrule50014* |5\<インスタンス番号\>14 |50014 |
| エンキュー レプリケーション/*Lbrule50016* |5\<インスタンス番号\>16 |50016 |
| SAP 起動サービス ERS HTTP/*Lbrule51013* |5\<インスタンス番号\>13 |51013 |
| SAP 起動サービス ERS HTTP/*Lbrule51014* |5\<インスタンス番号\>14 |51014 |
| Windows リモート管理 (WinRM)/*Lbrule5985* | |5985 |
| ファイル共有/*Lbrule445* | |445 |

**表 1:** SAP NetWeaver ABAP ASCS インスタンスのポート番号

次に、SAP NetWeaver Java SCS ポート用に次の負荷分散エンドポイントを作成します。

| サービス/負荷分散規則名 | 既定のポート番号 | (インスタンス番号が 01 の SCS インスタンス) (インスタンス番号が 11 の ERS) の具体的なポート |
| --- | --- | --- |
| エンキュー サーバー/*lbrule3201* |32\<インスタンス番号\> |3201 |
| ゲートウェイ サーバー/*lbrule3301* |33\<インスタンス番号\> |3301 |
| Java メッセージ サーバー/*lbrule3900* |39\<インスタンス番号\> |3901 |
| メッセージ サーバー HTTP/*Lbrule8101* |81\<インスタンス番号\> |8101 |
| SAP 起動サービス SCS HTTP/*Lbrule50113* |5\<インスタンス番号\>13 |50113 |
| SAP 起動サービス SCS HTTPS/*Lbrule50114* |5\<インスタンス番号\>14 |50114 |
| エンキュー レプリケーション/*Lbrule50116* |5\<インスタンス番号\>16 |50116 |
| SAP 起動サービス ERS HTTP/*Lbrule51113* |5\<インスタンス番号\>13 |51113 |
| SAP起動サービス ERS HTTP/*Lbrule51114* |5\<インスタンス番号\>14 |51114 |
| WinRM/*Lbrule5985* | |5985 |
| ファイル共有/*Lbrule445* | |445 |

**表 2:** SAP NetWeaver Java SCS インスタンスのポート番号

![図 5:Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則][sap-ha-guide-figure-3004]

_**図 5:** Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則_

ロード バランサー pr1-lb-dbms の IP アドレスを、DBMS インスタンスの仮想ホスト名の IP アドレスに設定します。

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則の変更

SAP ASCS または SCS インスタンスに別の番号を使う場合は、それらのポートの名前と値を既定値から変更する必要があります。

1. Azure Portal で、 **\<[\>** SID > -lb-ascs ロード バランサー] **[負荷分散規則]** の順に選びます。
2. SAP ASCS または SCS インスタンスに属するすべての負荷分散規則について、以下の値を変更します。

   * Name
   * Port
   * バックエンド ポート

   たとえば、既定の ASCS インスタンス番号を 00 から 31 に変更する場合は、表 1 に記載されているすべてのポートについて変更する必要があります。

   ポート *lbrule3200* の更新の例を次に示します。

   ![図 6:Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則を変更する][sap-ha-guide-figure-3005]

   _**図 6:** Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則を変更する_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> ドメインへの Windows 仮想マシンの追加

仮想マシンに静的 IP アドレスを割り当てた後、ドメインに仮想マシンを追加します。

![図 7:ドメインに仮想マシンを追加する][sap-ha-guide-figure-3006]

_**図 7:** ドメインに仮想マシンを追加する_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> SAP ASCS/SCS インスタンスの両方のクラスター ノードでのレジストリ エントリの追加

Azure Load Balancer は、設定されている時間 (アイドル タイムアウト) だけ接続がアイドル状態になっていると接続を閉じる内部ロード バランサーを備えています。 ダイアログ インスタンスの SAP ワーク プロセスは、最初のエンキュー/デキュー要求の送信が必要になるとすぐに、SAP エンキュー プロセスへの接続を開きます。 通常、これらの接続は、ワーク プロセスまたはエンキュー プロセスが再起動するまで確立されたままになります。 しかし、接続が一定時間アイドル状態の場合、Azure 内部ロード バランサーによって接続は閉じられます。 SAP ワーク プロセスはエンキュー プロセスへの接続が存在しなくなっていると再確立するため、これが問題になることはありません。 これらのアクティビティは SAP プロセスの開発者トレースに記録されますが、これらのトレースでは大量の追加コンテンツが作成されます。 そのため、両方のクラスター ノードで TCP/IP の `KeepAliveTime` と `KeepAliveInterval` を変更することをお勧めします。 TCP/IP パラメーターでのこれらの変更と、後で説明する SAP プロファイル パラメーターを組み合わせます。

SAP ASCS/SCS インスタンスの両方のクラスター ノードでレジストリ エントリを追加するには、まず、SAP ASCS/SCS の両方の Windows クラスター ノードに次の Windows レジストリ エントリを追加します。

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 変数名 |`KeepAliveTime` |
| 変数の型 |REG_DWORD (Decimal) |
| 値 |120000 |
| ドキュメントへのリンク |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**表 3:** 第 1 の TCP/IP パラメーターを変更する

次に、SAP ASCS/SCS の両方の Windows クラスター ノードで次の Windows レジストリ エントリを追加します。

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 変数名 |`KeepAliveInterval` |
| 変数の型 |REG_DWORD (Decimal) |
| 値 |120000 |
| ドキュメントへのリンク |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**表 4:** 第 2 の TCP/IP パラメーターを変更する

変更を適用するには、両方のクラスター ノードを再起動します。

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> SAP ASCS/SCS インスタンス用の Windows Server フェールオーバー クラスターをセットアップする

SAP ASCS/SCS インスタンス用の Windows Server フェールオーバー クラスターのセットアップには、以下のタスクが含まれます。

- クラスター構成内のクラスター ノードを収集します。
- クラスターのファイル共有監視を構成します。

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> クラスター構成内のクラスター ノードの収集

1. 役割と機能の追加ウィザードで、両方のクラスター ノードにフェールオーバー クラスタリングを追加します。
2. フェールオーバー クラスター マネージャーを使用して、フェールオーバー クラスターをセットアップします。 フェールオーバー クラスター マネージャーで、 **[クラスターの作成]** を選び、第 1 のクラスター (ノード A) の名前だけを追加します。 第 2 のノードはまだ追加しないでください。第 2 のノードは後の手順で追加します。

   ![図 8:第 1 のクラスター ノードのサーバー名または仮想マシン名を追加する][sap-ha-guide-figure-3007]

   _**図 8:** 第 1 のクラスター ノードのサーバー名または仮想マシン名を追加する_

3. クラスターのネットワーク名 (仮想ホスト名) を入力します。

   ![図 9:クラスター名を入力する][sap-ha-guide-figure-3008]

   _**図 9:** クラスター名を入力する_

4. クラスターを作成した後、クラスター検証テストを実行します。

   ![図 10:クラスター検証チェックを実行する][sap-ha-guide-figure-3009]

   _**図 10:** クラスター検証チェックを実行する_

   プロセスのこの時点では、ディスクに関する警告は無視してかまいません。 ファイル共有監視と SIOS 共有ディスクは後で追加します。 この段階では、クォーラムについて心配する必要はありません。

   ![図 11:クォーラム ディスクが見つからない][sap-ha-guide-figure-3010]

   _**図 11:** クォーラム ディスクが見つからない_

   ![図 12:コア クラスター リソースに新しい IP アドレスが必要である][sap-ha-guide-figure-3011]

   _**図 12:** コア クラスター リソースに新しい IP アドレスが必要である_

5. コア クラスター サービスの IP アドレスを変更します。 サーバーの IP アドレスが仮想マシン ノードの 1 つを指しているため、コア クラスター サービスの IP アドレスを変更するまではクラスターを開始できません。 この操作は、コア クラスター サービスの IP リソースの **[プロパティ]** ページで行います。

   たとえば、クラスターの仮想ホスト名 pr1-ascs-vir の IP アドレス (このガイドの例では 10.0.0.42) を割り当てる必要があります。

   ![図 13:[プロパティ] ダイアログ ボックスで IP アドレスを変更する][sap-ha-guide-figure-3012]

   _**図 13:**  **[プロパティ]** ダイアログ ボックスで IP アドレスを変更する_

   ![図 14:このクラスター用に予約された IP アドレスを割り当てる][sap-ha-guide-figure-3013]

   _**図 14:** このクラスター用に予約された IP アドレスを割り当てる_

6. クラスター仮想ホスト名をオンラインにします。

   ![図 15: 正しい IP アドレスでコア クラスター サービスが起動して実行されている][sap-ha-guide-figure-3014]

   _**図 15:** 正しい IP アドレスでコア クラスター サービスが起動して実行されている_

7. 2 つ目のクラスター ノードを追加します。

   コア クラスター サービスが起動して実行されているので、第 2 のクラスター ノードを追加できます。

   ![図 16: 2 番目のクラスター ノードを追加する][sap-ha-guide-figure-3015]

   _**図 16:** 第 2 のクラスター ノードを追加する_

8. 2 つ目のクラスター ノード ホストの名前を入力します。

   ![図 17:第 2 のクラスター ノード ホストの名前を入力する][sap-ha-guide-figure-3016]

   _**図 17:** 第 2 のクラスター ノード ホストの名前を入力する_

   > [!IMPORTANT]
   > **[使用可能な記憶域をすべてクラスターに追加する]** チェック ボックスがオンになって "*いない*" ことを確認してください。  
   >
   >

   ![図 18:このチェック ボックスはオンにしない][sap-ha-guide-figure-3017]

   _**図 18:** このチェック ボックスはオンに*しない*_

   クォーラムとディスクに関する警告は無視して構いません。 「[SAP ASCS/SCS クラスター共有ディスクのための SIOS DataKeeper Cluster Edition のインストール][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]」で説明されているように、クォーラムと共有ディスクは後で設定します。

   ![図 19:ディスク クォーラムに関する警告を無視する][sap-ha-guide-figure-3018]

   _**図 19:** ディスク クォーラムに関する警告を無視する_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> クラスターのファイル共有監視の構成

クラスターのファイル共有監視の構成には、以下のタスクが含まれます。

- ファイル共有を作成します。
- フェールオーバー クラスター マネージャーでファイル共有監視クォーラムを設定します。

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> ファイル共有の作成

1. クォーラム ディスクではなく、ファイル共有監視を選択します。 SIOS DataKeeper は、このオプションをサポートしています。

   この記事の例では、ファイル共有監視は Azure で実行されている Active Directory または DNS サーバー上にあります。 ファイル共有監視の名前は domcontr-0 です。 Azure への VPN 接続を構成したので (VPN Gateway または Azure ExpressRoute 経由)、Active Directory または DNS サービスはオンプレミスであり、ファイル共有監視の実行には適していません。

   > [!NOTE]
   > Active Directory または DNS サービスがオンプレミスのみで実行している場合は、オンプレミスで実行している Active Directory または DNS Windows オペレーティング システムには、ファイル共有監視を構成しないでください。 Azure で実行しているクラスター ノードとオンプレミスの Active Directory または DNS の間のネットワーク待機時間が長すぎて、接続の問題が発生する可能性があります。 ファイル共有監視は、クラスター ノードの近くで動いている Azure Virtual Machines に構成する必要があります。  
   >
   >

   クォーラム ドライブには、1,024 MB 以上の空き領域が必要です。 クォーラム ドライブでは空き領域を 2,048 MB にすることをお勧めします。

2. クラスター名オブジェクトを追加します。

   ![図 20:クラスター名オブジェクトの共有に対するアクセス許可を割り当てる][sap-ha-guide-figure-3019]

   _**図 20:** クラスター名オブジェクトの共有に対するアクセス許可を割り当てる_

   アクセス許可には、クラスター名オブジェクト (この例では pr1-ascs-vir$) の共有内のデータを変更する権限を必ず含めます。

3. クラスター名オブジェクトをリストに追加するには、 **[追加]** を選択します。 図 22 に示されているものに加えて、コンピューター オブジェクトをチェックするようにフィルターを変更します。

   ![図 21: コンピューターを含むようにオブジェクトの種類を変更する][sap-ha-guide-figure-3020]

   _**図 21:** コンピューターを含むように**オブジェクトの種類**を変更する_

   ![図 22:[コンピューター] チェック ボックスをオンにする][sap-ha-guide-figure-3021]

   _**図 22:**  **[コンピューター]** チェック ボックスをオンにする_

4. 図 21 に示すように、クラスター名オブジェクトを入力します。 レコードは既に作成されているので、図 20 に示すようにアクセス許可を変更できます。

5. 共有の **[セキュリティ]** タブを選択し、クラスター名オブジェクトのアクセス許可をさらに詳しく設定します。

   ![図 23:ファイル共有クォーラムでクラスター名オブジェクトのセキュリティ属性を設定する][sap-ha-guide-figure-3022]

   _**図 23:** ファイル共有クォーラムでクラスター名オブジェクトのセキュリティ属性を設定する_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> フェールオーバー クラスター マネージャーでのファイル共有監視クォーラムの設定

1. クラスター クォーラム構成ウィザードを開きます。

   ![図 24:クラスター クォーラム設定の構成ウィザードを開始する][sap-ha-guide-figure-3023]

   _**図 24:** クラスター クォーラム設定の構成ウィザードを開始する_

2. **[クォーラム構成オプションの選択]** ページで、 **[クォーラム監視を選択する]** を選びます。

   ![図 25:選択できるクォーラム構成][sap-ha-guide-figure-3024]

   _**図 25:** 選択できるクォーラム構成_

3. **[クォーラム監視の選択]** ページで、 **[ファイル共有監視を構成する]** を選択します。

   ![図 26:ファイル共有監視を選択する][sap-ha-guide-figure-3025]

   _**図 26:** ファイル共有監視を選択する_

4. ファイル共有の UNC パスを入力します (この例では \\domcontr-0\FSW)。 **[次へ]** を選択して、行うことができる変更の一覧を表示します。

   ![図 27:監視共有のファイル共有の場所を定義する][sap-ha-guide-figure-3026]

   _**図 27:** 監視共有のファイル共有の場所を定義する_

5. 必要な変更を選択し、 **[次へ]** を選択します。 図 28 に示すように、クラスター構成が正常に再構成されている必要があります。  

   ![図 28:クラスターが再構成されたことを確認する][sap-ha-guide-figure-3027]

   _**図 28:** クラスターが再構成されたことを確認する_

Windows フェールオーバー クラスターが正常にインストールされた後、フェールオーバーの検出が Azure の状態に合うように、いくつかのしきい値を変更する必要があります。 変更するパラメーターについては、「[Tuning failover cluster network thresholds][tuning-failover-cluster-network-thresholds]」(フェールオーバー クラスター ネットワークのしきい値の調整) をご覧ください。 ASCS/SCS 用の Windows クラスター構成を構築する 2 つの VM が同じサブネットにあることを前提とした場合、次のようにパラメーターの値に変更します。

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

これらの設定はお客様とテストしたものであり、適切な妥協が提供されます。 十分な回復力がある一方で、SAP ソフトウェアの実際のエラー状態またはノードや VM の障害において十分に高速なフェールオーバーも提供します。

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS クラスター共有ディスクのための SIOS DataKeeper Cluster Edition のインストール

これで、Azure で動作する Windows Server フェールオーバー クラスタリング構成が完了しました。 SAP ASCS/SCS インスタンスをインストールするには、共有ディスク リソースが必要です。 Azure では必要な共有ディスク リソースを作成できません。 サード パーティ製ソリューションの SIOS DataKeeper Cluster Edition を使って、共有ディスク リソースを作成できます。

SAP ASCS/SCS クラスター共有ディスク用の SIOS DataKeeper Cluster Edition のインストールには、次のタスクが含まれます。

- Microsoft .NET Framework 3.5 を追加します。
- SIOS DataKeeper をインストールします。
- SIOS DataKeeper をセットアップします。

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> .NET Framework 3.5 の追加
Windows Server 2012 R2 では、.NET Framework 3.5 は自動的に有効化またはインストールされません。 SIOS DataKeeper をインストールするすべてのノードに .NET が必要なので、クラスター内のすべての仮想マシンのゲスト オペレーティング システムに .NET Framework 3.5 をインストールする必要があります。

.NET Framework 3.5 は 2 とおりの方法で追加できます。

- 図 29 に示すように、Windows の役割と機能の追加ウィザードを使います。

  ![図 29: 役割と機能の追加ウィザードを使って .NET Framework 3.5 をインストールする][sap-ha-guide-figure-3028]

  _**図 29:** 役割と機能の追加ウィザードを使って .NET Framework 3.5 をインストールする_

  ![図 30: 役割と機能の追加ウィザードを使って .NET Framework 3.5 をインストールするときのインストール進行状況バー][sap-ha-guide-figure-3029]

  _**図 30:** 役割と機能の追加ウィザードを使って .NET Framework 3.5 をインストールするときのインストール進行状況バー_

- dism.exe コマンドライン ツールを使います。 この種のインストールでは、Windows インストール メディアの SxS ディレクトリにアクセスする必要があります。 管理者特権のコマンド プロンプトで、次のコマンドを入力します。

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> SIOS DataKeeper のインストール

クラスターの各ノードに SIOS DataKeeper Cluster Edition をインストールします。 SIOS DataKeeper で仮想共有記憶域を作成するには、同期されたミラーを作成し、クラスター共有記憶域をシミュレートします。

SIOS ソフトウェアをインストールする前に、DataKeeperSvc ドメイン ユーザーを作成します。

> [!NOTE]
> DataKeeperSvc ドメイン ユーザーを、両方のクラスター ノードのローカルの Administrator グループに追加します。
>
>

SIOS DataKeeper をインストールするには

1. SIOS ソフトウェアを両方のクラスター ノードにインストールします。

   ![SIOS インストーラー][sap-ha-guide-figure-3030]

   ![図 31:SIOS DataKeeper のインストールの最初のページ][sap-ha-guide-figure-3031]

   _**図 31:** SIOS DataKeeper のインストールの最初のページ_

2. ダイアログ ボックスで **[はい]** を選択します。

   ![図 32:DataKeeper からサービスを無効にすることが通知される][sap-ha-guide-figure-3032]

   _**図 32:** DataKeeper からサービスを無効にすることが通知される_

3. ダイアログ ボックスでは、 **[Domain or Server account]\(ドメインまたはサーバーのアカウント\)** を選択することをお勧めします。

   ![図 33:SIOS DataKeeper のユーザー選択][sap-ha-guide-figure-3033]

   _**図 33:** SIOS DataKeeper のユーザー選択_

4. SIOS DataKeeper 用に作成したドメイン アカウントのユーザー名とパスワードを入力します。

   ![図 34:SIOS DataKeeper インストールのドメイン ユーザー名とパスワードを入力する][sap-ha-guide-figure-3034]

   _**図 34:** SIOS DataKeeper インストールのドメイン ユーザー名とパスワードを入力する_

5. 図 35 に示すように、SIOS DataKeeper インスタンスのライセンス キーをインストールします。

   ![図 35:SIOS DataKeeper ライセンス キーを入力する][sap-ha-guide-figure-3035]

   _**図 35:** SIOS DataKeeper ライセンス キーを入力する_

6. メッセージが表示されたら、仮想マシンを再起動します。

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> SIOS DataKeeper のセットアップ

両方のノードに SIOS DataKeeper をインストールした後、構成を開始します。 この構成の目的は、各仮想マシンに接続された追加ディスク間で同期データ レプリケーションを実現することです。

1. DataKeeper の管理および構成ツールを起動し、 **[Connect to Server (サーバーに接続)]** を選択します

   ![図 36:SIOS DataKeeper の管理および構成ツール][sap-ha-guide-figure-3036]

   _**図 36:** SIOS DataKeeper の管理および構成ツール_

2. 管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードを入力します。

   ![図 37:管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードの情報を入力する][sap-ha-guide-figure-3037]

   _**図 37:** 管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードの情報を入力する_

3. 2 つのノード間のレプリケーション ジョブを作成します。

   ![図 38:レプリケーション ジョブを作成する][sap-ha-guide-figure-3038]

   _**図 38:** レプリケーション ジョブを作成する_

   ウィザードでレプリケーション ジョブの作成手順が示されます。

4. レプリケーション ジョブの名前を定義します。

   ![図 39:レプリケーション ジョブの名前を定義する][sap-ha-guide-figure-3039]

   _**図 39:** レプリケーション ジョブの名前を定義する_

   ![図 40:現在のソース ノードとして使用するノードの基本データを定義する][sap-ha-guide-figure-3040]

   _**図 40:** 現在のソース ノードとして使用するノードの基本データを定義する_

5. ターゲット ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義します。

   ![図 41: 現在のターゲット ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義する][sap-ha-guide-figure-3041]

   _**図 41:** 現在のターゲット ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義する_

6. 圧縮アルゴリズムを定義します。 この例では、レプリケーション ストリームを圧縮することをお勧めします。 特に再同期の状況では、レプリケーション ストリームを圧縮すると、再同期時間が大幅に短縮されます。 圧縮では仮想マシンの CPU および RAM リソースが使用されます。 圧縮率を大きくするほど、使われる CPU リソースの量も増えます。 この設定は後で調整できます。

7. 確認する必要があるもう 1 つの設定は、レプリケーションを非同期と同期のどちらで行うかです。 SAP ASCS/SCS の構成を保護する場合は、同期レプリケーションを使う必要があります。  

   ![図 42:レプリケーションの詳細を定義する][sap-ha-guide-figure-3042]

   _**図 42:** レプリケーションの詳細を定義する_

8. レプリケーション ジョブによってレプリケートされたボリュームを、Windows Server フェールオーバー クラスター構成に共有ディスクとして示すかどうかを定義します。 SAP ASCS/SCS の構成では、Windows クラスターがレプリケートされたボリュームをクラスター ボリュームとして使用できる共有ディスクとして認識するように、 **[はい]** を選択します。

   ![図 43:[はい] を選択し、レプリケートされたボリュームをクラスター ボリュームとして設定する][sap-ha-guide-figure-3043]

   _**図 43:**  **[はい]** を選択し、レプリケートされたボリュームをクラスター ボリュームとして設定する_

   ボリュームが作成された後、DataKeeper の管理および構成ツールではレプリケーション ジョブがアクティブであると表示されます。

   ![図 44:SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリングがアクティブになっている][sap-ha-guide-figure-3044]

   _**図 44:** SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリングがアクティブになっている_

   図 45 のように、フェールオーバー クラスター マネージャーでディスクが DataKeeper ディスクとして表示されるようになりました。

   ![図 45:DataKeeper がレプリケートしたディスクがフェールオーバー クラスター マネージャーに表示される][sap-ha-guide-figure-3045]

   _**図 45:** DataKeeper がレプリケートしたディスクがフェールオーバー クラスター マネージャーに表示される_

## <a name="next-steps"></a>次の手順

* [SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP NetWeaver HA をインストールする][sap-high-availability-installation-wsfc-shared-disk]
