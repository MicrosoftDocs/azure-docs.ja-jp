---
title: Azure で SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクに SAP NetWeaver HA をインストールする | Microsoft Docs
description: SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクに SAP NetWeaver HA をインストールする方法を説明します。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1a7d3d3a8f66cfbb3ed649ac645520f39cbb1e4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709009"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Azure で SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクに SAP NetWeaver HA をインストールする

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

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

この記事では、SAP ASCS/SCS インスタンスのクラスタリング用の Windows Server フェールオーバー クラスターとクラスター共有ディスクを使って、Azure に高可用性の SAP システムをインストールして構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

インストールを始める前に、次のドキュメントを確認してください。

* [アーキテクチャ ガイド: クラスター共有ディスクを使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する][sap-high-availability-guide-wsfc-shared-disk]

* [SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターと共有ディスクを使用して SAP HA 向けに Azure インフラストラクチャを準備する][sap-high-availability-infrastructure-wsfc-shared-disk]

DBMS のセットアップは使用する DBMS システムによって異なるため、この記事では説明はしません。 さまざまな DBMS ベンダーが Azure でサポートする機能を使用して、DBMS に関する高可用性の問題に対処していることを想定しています。 たとえば、SQL Server の Always On またはデータベース ミラーリング、Oracle データベースの Oracle Data Guard などがあります。 この記事のシナリオでは、DBMS の保護は強化していません。

異なる DBMS サービスが Azure のクラスター化された SAP ASCS または SCS 構成と対話する際に、特別な考慮事項はありません。

> [!NOTE]
> SAP NetWeaver ABAP システム、Java システム、ABAP + Java システムのインストール手順はほとんど同じです。 最も重要な違いは、SAP ABAP システムでは 1 つの ASCS インスタンスを使用することです。 SAP Java システムは 1 つの SCS インスタンスを使用します。 SAP ABAP + Java システムでは、1 つの ASCS インスタンスと、同じ Microsoft フェールオーバー クラスター グループで実行する 1 つの SCS インスタンスを使用します。 各 SAP NetWeaver インストール スタックのインストールの違いについて明確に説明します。 他のすべての部分は同じであると考えることができます。  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>高可用性 ASCS/SCS インスタンスでの SAP のインストール

> [!IMPORTANT]
> SIOS DataKeeper でミラー化されたボリュームには、ページ ファイルを配置しないでください。 DataKeeper はミラー化されたボリュームをサポートしていません。 ページ ファイルは、既定の場所である Azure Virtual Machines の一時ドライブ D のままにしておいてかまいません。 既にそこにない場合は、Azure Virtual Machines のドライブ D に Windows ページ ファイルを移動します。
>
>

高可用性 ASCS/SCS インスタンスでの SAP のインストールには、以下のタスクが含まれます。

* クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名を作成します。
* 最初の SAP クラスター ノードをインストールします。
* ASCS/SCS インスタンスの SAP プロファイルを変更します。
* プローブ ポートを追加します。
* Windows ファイアウォールでプローブ ポートを開きます。

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成

1. Windows DNS マネージャーで、ASCS/SCS インスタンスの仮想ホスト名の DNS エントリを作成します。

   > [!IMPORTANT]
   > ASCS/SCS インスタンスの仮想ホスト名に割り当てる IP アドレスは Azure Load Balancer (\<SID\>-lb-ascs) に割り当てた IP アドレスと同じでなければなりません。  
   >
   >

   SAP ASCS/SCS の仮想ホスト名 (pr1-ascs-sap) の IP アドレスは、Azure Load Balancer (pr1-lb-ascs) の IP アドレスと同じです。

   ![図 1:SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する][sap-ha-guide-figure-3046]

   _**図 1:** SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する_

2. 仮想ホスト名に割り当てる IP アドレスを定義するには、 **[DNS マネージャー]**  >  **[ドメイン]** の順に選びます。

   ![図 2:SAP ASCS/SCS クラスター構成の新しい仮想名と TCP/IP アドレス][sap-ha-guide-figure-3047]

   _**図 2:** SAP ASCS/SCS クラスター構成の新しい仮想名と TCP/IP アドレス_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> 最初の SAP クラスター ノードのインストール

1. クラスター ノード A で、最初のクラスター ノード オプションを実行します。たとえば、pr1-ascs-0* ホストで実行します。
2. Azure 内部ロード バランサーの既定のポートを維持するには、以下を選択します。

   * **ABAP システム**:**ASCS** インスタンス番号 **00**
   * **Java システム**:**SCS** インスタンス番号 **01**
   * **ABAP+Java システム**:**ASCS** インスタンス番号 **00** と **SCS** インスタンス番号 **01**

   ABAP ASCS インスタンスに対して 00 以外、Java SCS インスタンスに対して 01 以外のインスタンス番号を使うには、最初に、Azure 内部ロード バランサーの既定の負荷分散規則を変更します。 詳しくは、「[Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則の変更][sap-ha-guide-8.9]」をご覧ください。

次のいくつかのタスクは、通常の SAP インストール ドキュメントには記載されていません。

> [!NOTE]
> SAP インストール ドキュメントでは、最初の ASCS/SCS クラスター ノードをインストールする方法が説明されています。
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> ASCS/SCS インスタンスの SAP プロファイルの変更

最初に、新しいプロファイル パラメーターを追加します。 このプロファイル パラメーターにより、SAP ワーク プロセスとエンキュー サーバー間の接続が長時間にわたってアイドル状態のときに、接続が閉じられるのを防ぐことができます。 「[SAP ASCS/SCS インスタンスの両方のクラスター ノードでのレジストリ エントリの追加][sap-ha-guide-8.11]」では、問題のシナリオについて説明されています。 また、一部の基本的な TCP/IP 接続パラメーターに対する 2 つの変更も行っています。 第 2 の手順では、接続が Azure 内部ロード バランサーのアイドルしきい値に達しないようにするために、`keep_alive` 信号を送信するようにエンキュー サーバーを構成する必要があります。

ASCS/SCS インスタンスの SAP プロファイルを変更するには

1. SAP ASCS/SCS インスタンス プロファイルに次のプロファイル パラメーターを追加します。

   ```
   enque/encni/set_so_keepalive = true
   ```
   このガイドの例では、パスは次のようになります。

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   たとえば、SAP SCS インスタンス プロファイルの対応するパスは次のようになります。

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. 変更を適用するには、SAP ASCS/SCS インスタンスを再起動します。

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> プローブ ポートの追加

内部ロード バランサーのプローブ機能を使用して、クラスター全体の構成が Azure Load Balancer で動作するようにします。 通常、Azure 内部ロード バランサーは、参加している仮想マシン間に受信ワークロードを均等に分散させます。

 ただし、アクティブなインスタンスが 1 つだけであるため、一部のクラスター構成では動作しません。 他のインスタンスはパッシブであり、ワークロードを受け付けることができません。 プローブ機能は、Azure 内部ロード バランサーがアクティブなインスタンスにのみワークロードを割り当てる場合に役に立ちます。 プローブ機能により、内部ロード バランサーはアクティブなインスタンスを検出して、そのインスタンスだけをワークロードのターゲットにすることができます。

プローブ ポートを追加するには

1. 次の PowerShell コマンドを実行して、現在の **ProbePort** の値を確認します。

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   クラスター構成内のいずれかの仮想マシンからコマンドを実行します。

2. プローブ ポートを定義します。 既定のプローブ ポート番号は 0 です。 この例では、プローブ ポート 62000 を使います。

   ![図 3:クラスター構成のプローブ ポートは既定で 0 に設定されている][sap-ha-guide-figure-3048]

   _**図 3:** 既定のクラスター構成のプローブ ポートは 0 に設定されている_

   ポート番号は、SAP Azure Resource Manager テンプレートで定義されています。 PowerShell でポート番号を割り当てることができます。

   SAP \<SID\> IP クラスター リソースの新しい ProbePort 値を設定するには、次の PowerShell スクリプトを実行し、環境の PowerShell 変数を更新します。

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   SAP \<SID\> クラスター ロールをオンラインにした後、**ProbePort** が新しい値に設定されていることを確認します。

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   スクリプトを実行した後、変更を有効にするために SAP クラスター グループを再起動するよう求められます。

   ![図 4:新しい値を設定した後でクラスター ポートをプローブする][sap-ha-guide-figure-3049]

   _**図 4:** 新しい値を設定した後でクラスター ポートをプローブする_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Windows ファイアウォール プローブ ポートを開く

両方のクラスター ノードで Windows ファイアウォール プローブ ポートを開きます。 Windows ファイアウォール プローブ ポートを開くには、次のスクリプトを使用します。 環境に合わせて PowerShell 変数を更新してください。

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** は **62000** に設定されています。 これで、ascsha-dbas などの他のホストから、ファイル共有 \\\ascsha-clsap\sapmnt にアクセスできるようになりました。

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> データベース インスタンスのインストール

データベース インスタンスをインストールするには、SAP インストール ドキュメントに記載されている手順に従います。

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> 第 2 のクラスター ノードのインストール

第 2 のクラスター ノードをインストールするには、SAP インストール ガイドの手順に従います。

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> SAP ERS Windows サービスのインスタンスのスタートアップの種類の変更

両方のクラスター ノードで、SAP ERS Windows サービスの開始の種類を **[自動 (遅延開始)]** に変更します。

![図 5:SAP ERS インスタンスのサービスの種類を遅延自動に変更する][sap-ha-guide-figure-3050]

_**図 5:** SAP ERS インスタンスのサービスの種類を遅延自動に変更する_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP プライマリ アプリケーション サーバーのインストール

プライマリ アプリケーション サーバー (PAS) のインスタンス \<SID\>-di-0 を、PAS のホストとして指定した仮想マシンにインストールします。 Azure に対する依存関係はありません。 DataKeeper に固有の設定はありません。

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP 追加アプリケーション サーバーのインストール

SAP アプリケーション サーバー インスタンスのホストとして指定したすべての仮想マシンに、SAP 追加アプリケーション サーバー (AAS) をインストールします。 たとえば、\<SID\>-di-1 to \<SID\>-di-&lt;n&gt; にします。

> [!NOTE]
> これで、高可用性 SAP NetWeaver システムのインストールが完了します。 次に、フェールオーバーのテストに進みます。
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> SAP ASCS/SCS インスタンスのフェールオーバーと SIOS レプリケーションのテスト
フェールオーバー クラスター マネージャーと SIOS DataKeeper の管理および構成ツールを使用して、SAP ASCS/SCS インスタンスのフェールオーバーと SIOS ディスク レプリケーションを簡単にテストおよび監視できます。

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS インスタンスがクラスター ノード A で動作している状態

SAP PR1 クラスター グループが、クラスター ノード A (たとえば pr1-ascs-0) で動作しています。 SAP PR1 クラスター グループに含まれる共有ディスク ドライブ S を、クラスター ノード A に割り当てます。ASCS/SCS インスタンスもディスク ドライブ S を使います。 

![図 6:フェールオーバー クラスター マネージャー:SAP \<SID\> クラスター グループがクラスター ノード A で動作している][sap-ha-guide-figure-5000]

_**図 6:** フェールオーバー クラスター マネージャー:SAP \<SID\> クラスター グループがクラスター ノード A で動作している_

SIOS DataKeeper の管理および構成ツールで、共有ディスクのデータがクラスター ノード A (例: pr1-ascs-0 [10.0.0.40]) のソース ボリューム ドライブ S から、クラスター ノード B (例: pr1-ascs-1 [10.0.0.41]) のターゲット ボリューム ドライブ S に同期的にレプリケートされることを確認できます。

![図 7:SIOS DataKeeper で、クラスター ノード A からクラスター ノード B にローカル ボリュームをレプリケートする][sap-ha-guide-figure-5001]

_**図 7:** SIOS DataKeeper で、クラスター ノード A からクラスター ノード B にローカル ボリュームをレプリケートする_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> ノード A からノード B へのフェールオーバー

1. 次のいずれかの方法を選んで、クラスター ノード A からクラスター ノード B への SAP \<SID\> クラスター グループのフェールオーバーを開始します。
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

   フェールオーバーの後、SAP \<SID\> クラスター グループは、クラスター ノード B (たとえば pr1-ascs-1) で動作しています。

   ![図 8:フェールオーバー クラスター マネージャーで SAP \<SID\> クラスター グループがクラスター ノード B で動作している][sap-ha-guide-figure-5002]

   _**図 8**: フェールオーバー クラスター マネージャーで SAP \<SID\> クラスター グループがクラスター ノード B で動作している_

   現在、共有ディスクはクラスター ノード B にマウントされています。SIOS DataKeeper は、クラスター ノード B (例: pr1-ascs-1 [10.0.0.41]) のソース ボリューム ドライブ S から、クラスター ノード A (例: pr1-ascs-0 [10.0.0.40]) のターゲット ボリューム ドライブ S に、データをレプリケートします。

   ![図 9:SIOS DataKeeper で、クラスター ノード B からクラスター ノード A にローカル ボリュームをレプリケートする][sap-ha-guide-figure-5003]

   _**図 9:** SIOS DataKeeper で、クラスター ノード B からクラスター ノード A にローカル ボリュームをレプリケートする_
