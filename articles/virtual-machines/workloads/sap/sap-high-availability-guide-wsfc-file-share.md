---
title: Azure のファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する | Microsoft Docs
description: Azure のファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する方法を説明します。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d26df6aeb09934408b9081ac077af52ffc24d66
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709057"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

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
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Azure のファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する

> ![Windows][Logo_Windows] Windows
>

Windows Server フェールオーバー クラスタリングは、Windows での高可用性の SAP ASCS/SCS インストールと DBMS の基盤です。

フェールオーバー クラスターとは、アプリケーションとサービスの可用性を高めるために連携する、1 + n 台の独立したサーバー (ノード) のグループです。 ノード障害が発生した場合、Windows Server フェールオーバー クラスタリングは、アプリケーションとサービスを提供するクラスターを正常な状態で維持するうえで許容できるエラーの数を計算します。 フェールオーバー クラスタリングを実現するために、さまざまなクォーラム モードを選択できます。

## <a name="prerequisites"></a>前提条件
この記事に記載されているタスクを開始する前に、次の記事を確認してください。

* [SAP NetWeaver のための Azure Virtual Machines 高可用性のアーキテクチャとシナリオ][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> ファイル共有を使う SAP ASCS/SCS インスタンスのクラスター化は、SAP Kernel 7.49 (およびそれ以降) を含む SAP NetWeaver 7.40 (およびそれ以降) についてサポートされています。
>


## <a name="windows-server-failover-clustering-in-azure"></a>Azure での Windows Server フェールオーバー クラスタリング

ベア メタル デプロイやプライベート クラウド デプロイと比較すると、Azure Virtual Machines では Windows Server フェールオーバー クラスタリングを構成するための追加手順が必要となります。 クラスターを構築するときは、SAP ASCS/SCS インスタンスに複数の IP アドレスと仮想ホスト名を設定する必要があります。

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Azure での名前解決とクラスターの仮想ホスト名

Azure クラウド プラットフォームには、フローティング IP アドレスのような仮想 IP アドレスを構成するオプションは用意されていません。 クラウド内のクラスター リソースに到達するために仮想 IP アドレスを設定する別のソリューションが必要となります。 

Azure Load Balancer サービスは、Azure に "*内部ロード バランサー*" を提供します。 内部ロード バランサーでは、クライアントはクラスターの仮想 IP アドレスを使用してクラスターにアクセスします。 

クラスター ノードを含むリソース グループに、内部ロード バランサーをデプロイします。 その後、内部ロード バランサーのプローブ ポートを使って、必要なすべてのポート フォワーディング規則を構成します。 クライアントは仮想ホスト名を使用して接続できます。 DNS サーバーは、クラスターの IP アドレスを解決します。 内部ロード バランサーは、クラスターのアクティブ ノードへのポート フォワーディングを処理します。

![図 1:共有ディスクを使用しない Azure の Windows Server フェールオーバー クラスタリング構成][sap-ha-guide-figure-1001]

_**図 1:** 共有ディスクを使用しない Azure の Windows Server フェールオーバー クラスタリング構成_

## <a name="sap-ascsscs-ha-with-file-share"></a>ファイル共有を使用する SAP ASCS/SCS HA

SAP によって、共有ディスクをクラスター化する方法に代わり、Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する新しい方法が開発されました。 クラスター共有ディスクを使う代わりに、SMB ファイル共有を使って SAP グローバル ホスト ファイルをデプロイできます。

> [!NOTE]
> SMB ファイル共有は、SAP ASCS/SCS インスタンスのクラスタリングにクラスター共有ディスクを使う方法に代わるものです。  
>

このアーキテクチャには次のような固有機能があります。

* SAP セントラル サービス (独自のファイル構造、およびメッセージとエンキュー プロセスを含む) が、SAP グローバル ホスト ファイルから分離されます。
* SAP セントラル サービスが、SAP ASCS/SCS インスタンスで実行されます。
* SAP ASCS/SCS インスタンスがクラスター化され、\<ASCS/SCS 仮想ホスト名\> 仮想ホスト名を使ってアクセスできます。
* SAP グローバル ファイルは SMB ファイル共有に配置され、\<SAP グローバル ホスト\> ホスト名\\\\&lt;SAP グローバル ホスト&gt;\sapmnt\\&lt;SID&gt;\SYS\. を使ってアクセスされます。
* SAP ASCS/SCS インスタンスは、両方のクラスター ノード上のローカル ディスクにインストールされます。
* \<ASCS/SCS 仮想ホスト名\> ネットワーク名は、&lt;SAP グローバル ホスト&gt; とは異なります。

![図 2:SMB ファイル共有を使う SAP ASCS/SCS HA のアーキテクチャ][sap-ha-guide-figure-8004]

_**図 2:** SMB ファイル共有を使う新しい SAP ASCS/SCS HA のアーキテクチャ_

SMB ファイル共有の前提条件:

* SMB 3.0 (またはそれ以降) プロトコル。
* Active Directory ユーザー グループと `computer$` コンピューター オブジェクトに Active Directory アクセス制御リスト (ACL) を設定する機能。
* ファイル共有で HA が有効になっている必要があります。
    * ファイルの格納に使われるディスクが単一障害点にならないこと。
    * サーバーまたは VM のダウンタイムによってファイル共有にダウンタイムが発生しないこと。

SAP \<SID\> のクラスター ロールには、クラスター共有ディスクまたは汎用ファイル共有クラスター リソースは含まれません。


![図 3:ファイル共有を使うための SAP \<SID\> クラスター ロール リソース][sap-ha-guide-figure-8005]

_**図 3:** ファイル共有を使うための SAP &lt;SID&gt; クラスター ロール リソース_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>SAPMNT ファイル共有として Azure の記憶域スペース ダイレクトを使うスケールアウト ファイル共有

スケールアウト ファイル共有を使って、SAP グローバル ホスト ファイルのホストと保護を行うことができます。 また、スケールアウト ファイル共有は、高可用性の SAPMNT ファイル共有サービスも提供します。

![図 4:SAP グローバル ホスト ファイルの保護に使われるスケールアウト ファイル共有][sap-ha-guide-figure-8006]

_**図 4:** SAP グローバル ホスト ファイルの保護に使われるスケールアウト ファイル共有_

> [!IMPORTANT]
> スケールアウト ファイル共有は、Microsoft Azure クラウドおよびオンプレミス環境で完全にサポートされています。
>

スケールアウト ファイル共有は、高可用性および水平方向にスケーラブルな SAPMNT ファイル共有を提供します。

記憶域スペース ダイレクトは、スケールアウト ファイル共有のための共有ディスクとして使われます。 記憶域スペース ダイレクトを使うと、ローカル記憶域を含むサーバーを使って高可用性でスケーラブルな記憶域を構築できます。 SAP グローバル ホスト ファイルのようなスケールアウト ファイル共有に使われる共有記憶域は、単一障害点ではありません。

> [!IMPORTANT]
>ディザスター リカバリーのセットアップを計画して "*いない*" 場合は、Azure での高可用性ファイル共有のソリューションとしてスケールアウト ファイル共有を使うことをお勧めします。
>

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Azure でのスケールアウト ファイル共有に対する SAP の前提条件

スケールアウト ファイル共有を使うには、システムが次の要件を満たしている必要があります。

* 1 つのスケールアウト ファイル共有に対して少なくとも 2 つのクラスター ノード。
* 各ノードには、少なくとも 2 つのローカル ディスクが必要です。
* パフォーマンス上の理由から、"*ミラーリング回復性*" を使う必要があります。
    * 2 つのクラスター ノードによるスケールアウト ファイル共有の双方向ミラーリング。
    * 3 つ以上のクラスター ノードによるスケールアウト ファイル共有の 3 方向ミラーリング。
* スケールアウト ファイル共有には 3 方向ミラーリングを行う 3 つ以上のクラスター ノードをお勧めします。
    このセットアップにより、2 つのクラスター ノードと 2 方向ミラーリングを使うスケールアウト ファイル共有のセットアップより、拡張性や回復性が向上します。
* Azure Premium ディスクを使う必要があります。
* Azure Managed Disks を使うことをお勧めします。
* Resilient File System (ReFS) を使ってボリュームをフォーマットすることをお勧めします。
    * 詳しくは、「[SAP Note 1869038 - SAP support for ReFs filesystem][1869038]」 (SAP Note 1869038 - SAP による ReFs ファイル システムのサポート) および「記憶域スペース ダイレクトのボリュームの計画」記事の「and the [Choosing the file system][planning-volumes-s2d-choosing-filesystem]ファイル システムの選択」をご覧ください。
    * [Microsoft KB4025334 の累積的な更新プログラム][kb4025334]を必ずインストールします。
* DS シリーズまたは DSv2 シリーズの Azure VM サイズを使うことができます。
* 記憶域スペース ダイレクトのディスク同期に必要な VM 間の高いネットワーク パフォーマンスを得るには、少なくとも "高" ネットワーク帯域幅の VM タイプを使う必要があります。
    詳しくは、[DSv2 シリーズ][dv2-series]and [DS-Series][ds-series]の仕様をご覧ください。
* 記憶域プールに未割り当ての容量を若干確保しておくことをお勧めします。 記憶域プールに未割り当て容量を残しておくと、ドライブで障害が発生した場合に "その場で" 修復するためのボリューム領域が用意されます。 これにより、データの安全性とパフォーマンスが向上します。  詳しくは、「[ボリュームのサイズの選択][choosing-the-size-of-volumes-s2d]」をご覧ください。
* スケールアウト ファイル共有の Azure VM は、専用の Azure 可用性セットにデプロイする必要があります。
* スケールアウト ファイル共有のネットワーク名 (\<SAP グローバル ホスト\> など) に対して、Azure 内部ロード バランサーを構成する必要はありません。 これは、SAP ASCS/SCS インスタンスの \<ASCS/SCS 仮想ホスト名\> または DBMS に対して行われます。 スケールアウト ファイル共有は、すべてのクラスター ノード間に負荷をスケールアウトします。 \<SAP グローバル ホスト\> は、すべてのクラスター ノードのローカル IP アドレスを使います。


> [!IMPORTANT]
> \<SAP グローバル ホスト\> を指し示している SAPMNT ファイル共有の名前を変更することはできません。 SAP は、共有名 "sapmnt" のみをサポートします。
>
> 詳しくは、「[SAP Note 2492395 - Can the share name sapmnt be changed?][2492395]」 (SAP Note 2492395 - 共有名 sapmnt を変更できますか) をご覧ください。

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>2 つのクラスターで SAP ASCS/SCS インスタンスとスケールアウト ファイル共有を構成する

1 つのクラスターに SAP ASCS/SCS インスタンスをデプロイし、専用の SAP \<SID\> クラスター ロールを設定できます。 この場合は、別のクラスターに別のクラスター ロールでスケールアウト ファイル共有を構成します。

> [!IMPORTANT]
>このシナリオの SAP ASCS/SCS インスタンスは、UNC パス \\\\&lt;SAP グローバル ホスト&gt;\sapmnt\\&lt;SID&gt;\SYS\. を使って SAP グローバル ホストにアクセスするように構成されます。
>

![図 5:2 つのクラスターにデプロイされた SAP ASCS/SCS インスタンスとスケールアウト ファイル共有][sap-ha-guide-figure-8007]

_**図 5:** 2 つのクラスターにデプロイされた SAP ASCS/SCS インスタンスとスケールアウト ファイル共有_

> [!IMPORTANT]
> Azure クラウドでは、SAP およびスケールアウト ファイル共有に使われる各クラスターを、専用の Azure 可用性セットにデプロイする必要があります。 このようにすると、クラスターの VM が基になっている Azure インフラストラクチャ全体に分散して配置されます。
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>クラスター共有ディスクとして SIOS DataKeeper を使う汎用ファイル共有


> [!IMPORTANT]
> 高可用性のファイル共有にはスケールアウト ファイル共有ソリューションをお勧めします。
>
> 高可用性のファイル共有にディザスター リカバリーも設定する場合は、クラスター共有ディスク用に汎用ファイル共有と SISO DataKeeper を使う必要があります。
>

汎用ファイル共有は、高可用性ファイル共有を実現するためのもう 1 つのオプションです。

この場合は、クラスター共有ディスクとして、サード パーティの SIOS ソリューションを使うことができます。

## <a name="next-steps"></a>次の手順

* [SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターとファイル共有を使用して SAP HA 向けに Azure インフラストラクチャを準備する][sap-high-availability-infrastructure-wsfc-file-share]
* [SAP ASCS/SCS インスタンス用の Windows フェールオーバー クラスターとファイル共有に SAP NetWeaver HA をインストールする][sap-high-availability-installation-wsfc-shared-disk]
* [Azure での UPD 記憶域の 2 ノードの記憶域スペース ダイレクト スケール アウト ファイル サーバーのデプロイ][deploy-sofs-s2d-in-azure]
* [Windows Server 2016 での記憶域スペース ダイレクト][s2d-in-win-2016]
* [詳細:記憶域スペース ダイレクトのボリューム][deep-dive-volumes-in-s2d]
