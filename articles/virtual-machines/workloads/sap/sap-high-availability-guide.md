---
title: SAP NetWeaver のための Azure Virtual Machines 高可用性 | Microsoft Docs
description: Azure Virtual Machines (VM) 上の SAP NetWeaver の高可用性ガイド
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
ms.openlocfilehash: cb468a735a9e1f5cca899090df0125013b9b8d4c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709319"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>SAP NetWeaver のための Azure Virtual Machines 高可用性

[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

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



Azure Virtual Machines は、最短時間で、時間のかかる調達サイクルなしに、コンピューティング リソース、ストレージ リソース、およびネットワーク リソースを入手する必要のある組織向けのソリューションです。 Azure Virtual Machines を使用すると、SAP NetWeaver ベースの ABAP、Java、ABAP+Java スタックなどの従来のアプリケーションをデプロイできます。 オンプレミスのリソースを追加することなく、信頼性と可用性を高めます。 Azure Virtual Machines はクロスプレミス接続をサポートしているので、Azure Virtual Machines を組織のオンプレミスのドメイン、プライベート クラウド、SAP システム ランドスケープに統合できます。

この記事では、Azure Resource Manager デプロイ モデルを使用して Azure に高可用性 SAP システムをデプロイする手順について説明します。 主要なタスクは次のとおりです。

* 「[リソース][sap-ha-guide-2]」セクションの一覧で、適切な SAP Note とインストール ガイドを探します。 この記事は SAP インストール ドキュメントと SAP Note を補完するものであり、これらは特定のプラットフォームに SAP ソフトウェアをインストールしてデプロイするときの主要なリソースです。
* Azure Resource Manager デプロイ モデルと Azure クラシック デプロイ モデルの違いについて理解します。
* Azure のデプロイに適したモデルを選択できるように、Windows Server フェールオーバー クラスタリングのクォーラム モードについて理解します。
* Azure サービスでの Windows Server フェールオーバー クラスタリングの共有記憶域について理解します。
* Advanced Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) やデータベース管理システム (DBMS) のような単一障害点コンポーネントおよび SAP アプリケーション サーバーのような冗長コンポーネントを Azure で保護するために役立つ方法を理解します。
* Azure Resource Manager を使用して Azure の Windows Server フェールオーバー クラスタリング クラスターに高可用性 SAP システムをインストールして構成する手順の例に従います。
* Azure で Windows Server フェールオーバー クラスタリングを使用するために必要な追加の手順を理解します。ただし、オンプレミスのデプロイでは必要ありません。

デプロイと構成を簡単にするため、この記事では、SAP の 3 層高可用性 Resource Manager テンプレートを使用します。 このテンプレートを使用すると、高可用性 SAP システムに必要なインフラストラクチャ全体を自動的にデプロイできます。 また、インフラストラクチャは SAP システムの SAP Application Performance Standard (SAPS) サイジングもサポートします。

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> 前提条件
始める前に、次のセクションで説明されている前提条件が満たされていることを確認してください。 また、「[リソース][sap-ha-guide-2]」セクションに一覧表示されているすべてのリソースをご確認ください。

この記事では、[Managed Disks を使用した 3 層 SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/) 用の Azure Resource Manager テンプレートを使用します。 SAP Azure Resource Manager テンプレートの概要については、「[Running SAP Applications on the Microsoft Platform](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)」(Microsoft プラットフォームでの SAP アプリケーションの実行) を参照してください。

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> リソース
Azure での SAP のデプロイについては、以下の記事で説明されています。

* [SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP NetWeaver のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [SAP NetWeaver のための Azure Virtual Machines 高可用性 (このガイド)][sap-ha-guide]

> [!NOTE]
> 可能な限り、参照する SAP インストール ガイドへのリンクを提示しています ([SAP インストール ガイド][sap-installation-guides]を参照)。 インストール プロセスに関する前提条件と情報については、SAP NetWeaver のインストール ガイドをよく読むことをお勧めします。 この記事では、Virtual Machines で使用できる SAP NetWeaver ベースのシステムに関するタスクについてのみ説明します。
>
>

次の SAP Note は、Azure での SAP のトピックに関連するものです。

| Note 番号 | タイトル |
| --- | --- |
| [1928533] |SAP Applications on Azure:Supported Products and Sizing (Azure 上の SAP アプリケーション: サポートされている製品とサイズ) |
| [2015553] |SAP on Microsoft Azure:Support Prerequisites (Microsoft Azure 上の SAP: サポートの前提条件) |
| [1999351] |Enhanced Azure Monitoring for SAP (Azure Monitoring for SAP の強化) |
| [2178632] |Key Monitoring Metrics for SAP on Microsoft Azure (Microsoft Azure 上の SAP 用の主要な監視メトリック) |
| [1999351] |Virtualization on Windows:Enhanced Monitoring (Azure を使用した Linux での仮想化: 拡張された監視機能) |
| [2243692] |Use of Azure Premium SSD Storage for SAP DBMS Instance (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用) |

一般的な既定の制限事項や上限など、[Azure サブスクリプションの制限事項][azure-subscription-service-limits-subscription]について理解しておいてください。

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Azure Resource Manager デプロイメント モデルと Azure クラシック デプロイメント モデルでの高可用性 SAP
Azure Resource Manager デプロイ モデルと Azure クラシック デプロイ モデルでは、次の点が異なります。

- リソース グループ
- Azure リソース グループに対する Azure 内部ロード バランサーの依存関係
- SAP マルチ SID シナリオのサポート

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a>リソース グループ
Azure Resource Manager では、リソース グループを使用して Azure サブスクリプション内のすべてのアプリケーション リソースを管理できます。 統合されたアプローチでは、リソース グループ内のすべてのリソースのライフ サイクルが同じになります。 たとえば、すべてのリソースは、同時に作成され、同時に削除されます。 [リソース グループ](../../../azure-resource-manager/resource-group-overview.md#resource-groups)に関する詳細情報を参照してください。

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure リソース グループに対する Azure 内部ロード バランサーの依存関係

Azure クラシック デプロイ モデルでは、Azure 内部ロード バランサー (Azure Load Balancer サービス) とクラウド サービスの間に依存関係があります。 どの内部ロード バランサーにも 1 つのクラウド サービスが必要です。

Azure Resource Manager では、すべての Azure リソースを Azure リソース グループに配置する必要があり、これは Azure Load Balancer についても有効です。 ただし、Azure Load Balancer ごとに 1 つの Azure リソース グループを割り当てる必要はありません (たとえば、1 つの Azure リソース グループに複数の Azure Load Balancer を含めることができます)。 環境は、よりシンプルで柔軟性が高くなります。 

### <a name="support-for-sap-multi-sid-scenarios"></a>SAP マルチ SID シナリオのサポート

Azure Resource Manager では、1 つのクラスターに複数の SAP システム識別子 (SID) ASCS/SCS インスタンスをインストールすることができます。 各 Azure 内部ロード バランサーで複数の IP アドレスがサポートされているため、マルチ SID インスタンスが可能になります。

Azure クラシック デプロイ モデルを使用するには、[Azure での SAP NetWeaver に関する「Clustering SAP ASCS/SCS instances by using Windows Server Failover Clustering in Azure with SIOS DataKeeper (Azure での Windows Server フェールオーバー クラスタリングと SIOS DataKeeper を使用した SAP ASCS/SCS インスタンスのクラスタ化)」](https://go.microsoft.com/fwlink/?LinkId=613056)に記載されている手順に従ってください。

> [!IMPORTANT]
> SAP のインストールには、Azure Resource Manager デプロイ モデルを使用することを強くお勧めします。 クラシック デプロイ モデルにはない多くの利点があります。 Azure のデプロイメント モデルの詳細については、[こちら][virtual-machines-azure-resource-manager-architecture-benefits-arm]をご覧ください。   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server フェールオーバー クラスタリング
Windows Server フェールオーバー クラスタリングは、Windows での高可用性の SAP ASCS/SCS インストールと DBMS の基盤です。

フェールオーバー クラスターとは、アプリケーションとサービスの可用性を高めるために連携する、1 + n 台の独立したサーバー (ノード) のグループです。 ノード障害が発生した場合、Windows Server フェールオーバー クラスタリングは、アプリケーションとサービスを提供するクラスターを正常な状態で維持するうえで許容できるエラーの数を計算します。 フェールオーバー クラスタリングを実現するために、さまざまなクォーラム モードを選択できます。

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> クォーラム モード
Windows Server フェールオーバー クラスタリングを使用するときは、次の 4 つのクォーラム モードから選択できます。

* **ノード マジョリティ**。 クラスターの各ノードが投票できます。 クラスターは、投票の過半数を得た場合にのみ機能します。 ノードの数が奇数のクラスターでは、このオプションをお勧めします。 たとえば、7 ノードのクラスターでは 3 つのノードで障害が発生してもよく、クラスターは過半数を得て実行を続けます。  
* **ノードおよびディスク マジョリティ**。 各ノードとクラスター記憶域の指定されたディスク (ディスク監視) が投票できます。ただし、これらが使用可能であり、通信している必要があります。 クラスターは、投票数が過半数の場合にのみ機能します。 このモードは、ノード数が偶数のクラスター環境で有効です。 半数のノードとディスクがオンラインの場合、クラスターは正常な状態を維持します。
* **ノードおよびファイル共有マジョリティ**。 各ノードと、管理者が作成した指定ファイル共有 (ファイル共有監視) が投票できます。ノードとファイル共有が使用可能であるかどうかや、通信しているかどうかは問いません。 クラスターは、投票数が過半数の場合にのみ機能します。 このモードは、ノード数が偶数のクラスター環境で有効です。 ノードおよびディスク マジョリティ モードと似ていますが、監視ディスクではなく監視ファイル共有を使用します。 このモードは、実装は簡単ですが、ファイル共有自体が高可用性でない場合、単一障害点になる可能性があります。
* **マジョリティなし:ディスクのみ**。 1 つのノードが使用可能であり、クラスター記憶域内の特定のディスクと通信していれば、クラスターはクォーラムを獲得します。 また、そのディスクと通信しているノードだけがクラスターに参加できます。 このモードは使用しないことをお勧めします。
 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server フェールオーバー クラスタリング オンプレミス
図 1 は、2 ノードのクラスターを示しています。 ノード間のネットワーク接続で障害が発生し、ノードは両方とも稼働している場合、クォーラム ディスクまたはファイル共有により、クラスターのアプリケーションとサービスの提供を続けるノードが決まります。 クォーラム ディスクまたはファイル共有にアクセスできるノードが、サービスを維持するノードです。

この例は 2 ノード クラスターを使用しているため、ノードおよびファイル共有マジョリティ クォーラム モードを使用します。 ノードおよびディスク マジョリティも有効な選択肢です。 運用環境では、クォーラム ディスクを使用することをお勧めします。 ネットワークおよび記憶域システムのテクノロジを使用して、高可用性にすることができます。

![図 1:Azure での SAP ASCS/SCS の Windows Server フェールオーバー クラスタリング構成の例][sap-ha-guide-figure-1000]

_**図 1:** Azure での SAP ASCS/SCS の Windows Server フェールオーバー クラスタリング構成の例_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> 共有記憶域
図 1 では、2 ノードの共有記憶域クラスターも示されています。 オンプレミスの共有記憶域クラスターでは、クラスター内のすべてのノードが共有記憶域を検出します。 ロック メカニズムがデータを破損から防ぎます。 別のノードで障害が発生したかどうかをすべてのノードが検出できます。 一方のノードで障害が発生すると、もう一方のノードがストレージ リソースの所有権を取得し、サービスの可用性を確保します。

> [!NOTE]
> SQL Server のような一部の DBMS アプリケーションでは、高可用性のために共有ディスクは必要ありません。 SQL Server Always On は、DBMS のデータとログ ファイルを、クラスター ノードのローカル ディスクから別のクラスター ノードのローカル ディスクにレプリケートします。 その場合、Windows クラスター構成に共有ディスクは不要です。
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> ネットワークと名前解決
クライアント コンピューターは、仮想 IP アドレスと DNS サーバーが提供する仮想ホスト名を使用して、クラスターに接続します。 オンプレミスのノードと DNS サーバーは、複数の IP アドレスを処理できます。

標準的なセットアップでは、次のような複数のネットワーク接続が使用されます。

* 記憶域への専用接続
* ハートビートのためのクラスター内ネットワーク接続
* クライアントがクラスターへの接続に使用するパブリック ネットワーク

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Azure での Windows Server フェールオーバー クラスタリング
ベア メタル デプロイやプライベート クラウド デプロイと比較すると、Azure Virtual Machines では Windows Server フェールオーバー クラスタリングを構成するための追加手順が必要となります。 共有クラスター ディスクを構築するときは、SAP ASCS/SCS インスタンスに複数の IP アドレスと仮想ホスト名を設定する必要があります。

この記事では、重要な概念と、Azure で SAP 高可用性セントラル サービス クラスターを構築するために必要な追加の手順について説明します。 サード パーティ製ツールの SIOS DataKeeper をセットアップし、Azure 内部ロード バランサーを構成する方法を示します。 これらのツールを使用して、Azure でファイル共有監視を使用する Windows フェールオーバー クラスターを作成できます。

![図 2:共有ディスクを使用しない Azure の Windows Server フェールオーバー クラスタリング構成][sap-ha-guide-figure-1001]

_**図 2:** 共有ディスクを使用しない Azure での Windows Server フェールオーバー クラスタリング構成_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> SIOS DataKeeper を使用する Azure の共有ディスク
高可用性の SAP ASCS/SCS インスタンスには共有記憶域をクラスター化する必要があります。 2016 年 9 月の時点で、Azure は共有記憶域クラスターの作成に使用できる共有記憶域を提供していません。 代わりに、サード パーティ製ソフトウェアの SIOS DataKeeper Cluster Edition を使用して、クラスター共有記憶域をシミュレートするミラー化された記憶域を作成できます。 SIOS ソリューションは、リアルタイムの同期データ レプリケーションを実現します。 クラスターの共有ディスク リソースを作成する方法は次のとおりです。

1. Windows クラスター構成内の各仮想マシン (VM) に追加ディスクを接続します。
2. 両方の仮想マシン ノードで、SIOS DataKeeper Cluster Edition を実行します。
3. ソース仮想マシンの追加ディスク接続ボリュームの内容をターゲット仮想マシンの追加ディスク接続ボリュームにミラー化するように SIOS DataKeeper Cluster Edition を構成します。 SIOS DataKeeper は、ソースとターゲットのローカル ボリュームを抽象化し、1 つの共有ディスクとして Windows フェールオーバー クラスタリングに提示します。

詳細については、[SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/) を参照してください。

![図 3:SIOS DataKeeper を使用する Azure での Windows Server フェールオーバー クラスタリング構成][sap-ha-guide-figure-1002]

_**図 3:** SIOS DataKeeper を使用する Azure での Windows Server フェールオーバー クラスタリング構成_

> [!NOTE]
> SQL Server のような一部の DBMS 製品では、高可用性のために共有ディスクは必要ありません。 SQL Server Always On は、DBMS のデータとログ ファイルを、クラスター ノードのローカル ディスクから別のクラスター ノードのローカル ディスクにレプリケートします。 その場合、Windows クラスター構成に共有ディスクは不要です。
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Azure での名前解決
Azure クラウド プラットフォームには、フローティング IP アドレスのような仮想 IP アドレスを構成するオプションは用意されていません。 クラウド内のクラスター リソースに到達するために仮想 IP アドレスを設定する別のソリューションが必要となります。
Azure Load Balancer サービスには内部ロード バランサーがあります。 内部ロード バランサーでは、クライアントはクラスターの仮想 IP アドレスを使用してクラスターにアクセスします。
クラスター ノードを含むリソース グループに、内部ロード バランサーをデプロイする必要があります。 その後、内部ロード バランサーのプローブ ポートで必要なすべてのポート フォワーディング規則を構成します。
クライアントは仮想ホスト名を使用して接続できます。 DNS サーバーがクラスター IP アドレスを解決し、内部ロード バランサーがクラスターのアクティブ ノードへのポート フォワーディングを処理します。

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Azure IaaS (サービスとしてのインフラストラクチャ) での SAP NetWeaver の高可用性
SAP ソフトウェア コンポーネントなどの SAP アプリケーションの高可用性を実現するには、次のコンポーネントを保護する必要があります。

* SAP アプリケーション サーバー インスタンス
* SAP ASCS/SCS インスタンス
* DBMS サーバー

高可用性シナリオでの SAP コンポーネントの保護の詳細については、[SAP NetWeaver のための Azure Virtual Machines の計画および実装][planning-guide-11]に関するページを参照してください。

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> 高可用性 SAP アプリケーション サーバー
SAP アプリケーション サーバーおよびダイアログ インスタンスについては、通常、特定の高可用性ソリューションは不要です。 高可用性は冗長性によって実現し、Azure Virtual Machines のさまざまなインスタンスで複数のダイアログ インスタンスを構成します。 Azure Virtual Machines の 2 つのインスタンスに少なくとも 2 つの SAP アプリケーション インスタンスをインストールする必要があります。

![図 4:高可用性 SAP アプリケーション サーバー][sap-ha-guide-figure-2000]

_**図 4:** 高可用性 SAP アプリケーション サーバー_

SAP アプリケーション サーバー インスタンスをホストするすべての仮想マシンを、同じ Azure 可用性セットに配置する必要があります。 Azure 可用性セットでは次のことが保証されます。

* すべての仮想マシンは、同じアップグレード ドメインの一部になります。 たとえば、アップグレード ドメインでは、計画的メンテナンス ダウンタイムの間に仮想マシンが同時に更新されないことが保証されます。
* すべての仮想マシンは、同じ障害ドメインの一部になります。 たとえば、障害ドメインでは、単一障害点によってすべての仮想マシンの可用性が影響を受けないように仮想マシンがデプロイされることが保証されます。

[仮想マシンの可用性を管理][virtual-machines-manage-availability]する方法の詳細を参照してください。

アンマネージド ディスクのみ:Azure ストレージ アカウントが単一障害点になる可能性があるため、少なくとも 2 つの Azure ストレージ アカウントを用意し、それぞれにおいて少なくとも 2 つの仮想マシンに分散させることが重要です。 理想的な設定としては、SAP ダイアログ インスタンスを実行する各仮想マシンのディスクを、異なるストレージ アカウントにデプロイします。

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> 高可用性の SAP ASCS/SCS インスタンス
図 5 は、高可用性の SAP ASCS/SCS インスタンスの例です。

![図 5:高可用性の SAP ASCS/SCS インスタンス][sap-ha-guide-figure-2001]

_**図 5:** 高可用性 SAP ASCS/SCS インスタンス_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Azure の Windows Server フェールオーバー クラスタリングを使用する SAP ASCS/SCS インスタンスの高可用性
ベア メタル デプロイやプライベート クラウド デプロイと比較すると、Azure Virtual Machines では Windows Server フェールオーバー クラスタリングを構成するための追加手順が必要となります。 Windows フェールオーバー クラスターを構築するには、SAP ASCS/SCS インスタンスをクラスター化するために、共有クラスター ディスク、複数の IP アドレス、複数の仮想ホスト名、Azure 内部ロード バランサーが必要になります。 これについては、記事の後半で詳しく説明します。

![図 6:SIOS DataKeeper を使用する Azure での SAP ASCS/SCS 構成の Windows Server フェールオーバー クラスタリング][sap-ha-guide-figure-1002]

_**図 6:** SIOS DataKeeper を使用する Azure での SAP ASCS/SCS 構成の Windows Server フェールオーバー クラスタリング_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>高可用性の DBMS インスタンス
DBMS は、SAP システムでの単一接続点でもあります。 高可用性ソリューションを使用して保護する必要があります。 図 7 では、Azure の SQL Server Always On 高可用性ソリューションと、Windows Server フェールオーバー クラスタリングおよび Azure 内部ロード バランサーを示します。 SQL Server Always On は、独自の DBMS レプリケーションを使用して、DBMS のデータとログ ファイルをレプリケートします。 この場合、クラスター共有ディスクは必要なく、セットアップ全体が簡単になります。

![図 7:高可用性 SAP DBMS と SQL Server AlwaysOn の例][sap-ha-guide-figure-2003]

_**図 7:** 高可用性 SAP DBMS と SQL Server AlwaysOn の例_

Azure Resource Manager デプロイ モデルを使用した Azure での SQL Server のクラスタリングの詳細については、次の記事を参照してください。

* [Azure VM での AlwaysOn 可用性グループの手動構成 - Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Azure の AlwaysOn 可用性グループに使用する Azure 内部ロード バランサーの構成][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> エンド ツー エンドの高可用性デプロイ シナリオ

### <a name="deployment-scenario-using-architectural-template-1"></a>Architectural Template 1 を使用するデプロイ シナリオ

図 8 は、**1 つ**の SAP システムに対する Azure での SAP NetWeaver 高可用性アーキテクチャの例です。 このシナリオは、次のようにセットアップされます。

- SAP ASCS/SCS インスタンス用に 1 つの専用クラスターが使用されます。
- DBMS インスタンス用に 1 つの専用クラスターが使用されます。
- SAP アプリケーション サーバー インスタンスが独自の専用 VM にデプロイされます。

![図 8:ASCS/SCS と DBMS に専用クラスターを使用する SAP 高可用性 Architectural Template 1][sap-ha-guide-figure-2004]

_**図 8:** ASCS/SCS と DBMS に専用クラスターを使用する SAP 高可用性 Architectural Template 1_

### <a name="deployment-scenario-using-architectural-template-2"></a>Architectural Template 2 を使用するデプロイ シナリオ

図 9 は、**1 つ**の SAP システムに対する Azure での SAP NetWeaver 高可用性アーキテクチャの例です。 このシナリオは、次のようにセットアップされます。

- SAP ASCS/SCS インスタンスと DBMS の**両方**に 1 つの専用クラスターが使用されます。
- SAP アプリケーション サーバー インスタンスが独自の専用 VM にデプロイされます。

![図 9:ASCS/SCS と DBMS に個別の専用クラスターを使用する SAP 高可用性 Architectural Template 2][sap-ha-guide-figure-2005]

_**図 9:** ASCS/SCS と DBMS に個別の専用クラスターを使用する SAP 高可用性 Architectural Template 2_

### <a name="deployment-scenario-using-architectural-template-3"></a>Architectural Template 3 を使用するデプロイ シナリオ

図 10 は、Azure での SAP NetWeaver 高可用性アーキテクチャの例を示します。このアーキテクチャは、&lt;SID1&gt; と &lt;SID2&gt; を持つ **2 つ**の SAP システムに対応しています。 このシナリオは、次のようにセットアップされます。

- 1 つの専用クラスターが、SAP ASCS/SCS SID1 インスタンス "*と*" SAP ASCS/SCS SID2 インスタンスの**両方**に使用されます (1 つのクラスター)。
- 1 つの専用クラスターが DBMS SID1 に使用され、もう 1 つの専用クラスターが DBMS SID2 に使用されます (2 つのクラスター)。
- SAP システム SID1 の SAP アプリケーション サーバー インスタンスには、独自の専用 VM があります。
- SAP システム SID2 の SAP アプリケーション サーバー インスタンスには、独自の専用 VM があります。

![図 10:SAP ASCS/SCS インスタンスごとに専用クラスターを使用する高可用性 Architectural Template 3][sap-ha-guide-figure-6003]

_**図 10:** SAP ASCS/SCS インスタンスごとに専用クラスターを使用する高可用性 Architectural Template 3_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> インフラストラクチャの準備

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Architectural Template 1 のインフラストラクチャを準備する
SAP 用の Azure Resource Manager テンプレートを使用すると、必要なリソースを簡単にデプロイできます。

Azure Resource Manager の 3 層テンプレートは、2 つのクラスターを持つ Architectural Template 1 のような高可用性シナリオもサポートします。 各クラスターは、SAP ASCS/SCS および DBMS の SAP 単一障害点です。

この記事で説明しているサンプル シナリオの Azure Resource Manager テンプレートは、以下で入手できます。

* [Azure Marketplace イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Managed Disks を使用した Azure Marketplace イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [カスタム イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Managed Disks を使用したカスタム イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Architectural Template 1 のインフラストラクチャを準備するには

- Azure Portal の **[パラメーター]** ブレードにある **[SYSTEMAVAILABILITY]** ボックスで、 **[HA]** を選択します。

  ![図 11:SAP 高可用性 Azure Resource Manager パラメーターを設定する][sap-ha-guide-figure-3000]

_**図 11:** SAP 高可用性 Azure Resource Manager パラメーターを設定する_


  テンプレートによって次のものが作成されます。

  * **仮想マシン**:
    * SAP アプリケーション サーバーの仮想マシン: <*SAPSystemSID*>-di-<*Number*>
    * ASCS/SCS クラスターの仮想マシン: <*SAPSystemSID*>-ascs-<*Number*>
    * DBMS クラスター: <*SAPSystemSID*>-db-<*Number*>

  * **すべての仮想マシンのネットワーク カードと、関連付けられている IP アドレス**:
    * <*SAPSystemSID*>-nic-di-<*Number*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*>-nic-db-<*Number*>

  * **Azure ストレージ アカウント (非管理対象ディスクのみ)**

  * 次のものの**可用性グループ**:
    * SAP アプリケーション サーバーの仮想マシン: <*SAPSystemSID*>-avset-di
    * SAP ASCS/SCS クラスターの仮想マシン: <*SAPSystemSID*>-avset-ascs
    * DBMS クラスターの仮想マシン: <*SAPSystemSID*>-avset-db

  * **Azure 内部ロード バランサー**:
    * ASCS/SCS インスタンスのすべてのポートと IP アドレス: <*SAPSystemSID*>-lb-ascs
    * SQL Server DBMS のすべてのポートと IP アドレス: <*SAPSystemSID*>-lb-db

  * **ネットワーク セキュリティ グループ**: <*SAPSystemSID*>-nsg-ascs-0  
    * <*SAPSystemSID*>-ascs-0 仮想マシンに対して開かれた外部リモート デスクトップ プロトコル (RDP) ポート

> [!NOTE]
> ネットワーク カードと Azure 内部ロード バランサーのすべての IP アドレスは、既定では**動的**です。 これらを、**静的**な IP アドレスに変更します。 この方法については、この記事の後の方で説明します。
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> 運用環境で使用するための企業ネットワーク接続 (クロスプレミス) を使用する仮想マシンのデプロイ
運用 SAP システムの場合、Azure サイト間 VPN または Azure ExpressRoute を使用して、[企業ネットワーク接続 (クロスプレミス)][planning-guide-2.2] を使用する Azure Virtual Machines をデプロイします。

> [!NOTE]
> Azure Virtual Network インスタンスを使用できます。 仮想ネットワークとサブネットは既に作成および準備されています。
>
>

1. Azure Portal の **[パラメーター]** ブレードにある **[NEWOREXISTINGSUBNET]** ボックスで、 **[existing (既存)]** を選択します。
2. **[SUBNETID]** ボックスに、Azure 仮想マシンのデプロイ先となる準備済みの Azure ネットワーク SubnetID の完全な文字列を追加します。
3. すべての Azure ネットワーク サブネットの一覧を取得するには、次の PowerShell コマンドを実行します。

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **[ID]** フィールドに **[SUBNETID]** と表示されます。
4. すべての **SUBNETID** 値の一覧を取得するには、次の PowerShell コマンドを実行します。

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   **SUBNETID** は次のようになります。

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> テストおよびデモ用のクラウドのみの SAP インスタンスのデプロイ
クラウドのみのデプロイメント モデルで高可用性 SAP システムをデプロイすることができます。 この種類のデプロイは、主として、デモおよびテストのユース ケースに適しています。 運用環境での使用には適していません。

- Azure Portal の **[パラメーター]** ブレードにある **[NEWOREXISTINGSUBNET]** ボックスで、 **[new (新規)]** を選択します。 **[SUBNETID]** フィールドは空白のままにしておきます。

  SAP Azure Resource Manager テンプレートは、Azure Virtual Network とサブネットを自動的に作成します。

> [!NOTE]
> また、同じ Azure Virtual Network インスタンス内に Active Directory と DNS 用の専用仮想マシンを少なくとも 1 つデプロイする必要があります。 テンプレートではこれらの仮想マシンは作成されません。
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Architectural Template 2 のインフラストラクチャを準備する

この Azure Resource Manager テンプレートを SAP に使うと、SAP Architectural Template 2 に必要なインフラストラクチャ リソースのデプロイを簡略化するのに役立ちます。

このデプロイ シナリオの Azure Resource Manager テンプレートは以下で入手できます。

* [Azure Marketplace イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Managed Disks を使用した Azure Marketplace イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [カスタム イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Managed Disks を使用したカスタム イメージ](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Architectural Template 3 のインフラストラクチャを準備する

**マルチ SID** 向けにインフラストラクチャを準備し、SAP を構成することができます。 たとえば、追加の SAP ASCS/SCS インスタンスを "*既存の*" クラスター構成に追加できます。 詳細については、[Azure Resource Manager で SAP マルチ SID 構成を作成するために既存のクラスター構成に追加の SAP ASCS/SCS インスタンスを構成する][sap-ha-multi-sid-guide]方法に関するトピックを参照してください。

新しいマルチ SID クラスターを作成する場合は、GitHub のマルチ SID [クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates)を使用できます。
新しいマルチ SID クラスターを作成するには、次の 3 つのテンプレートをデプロイする必要があります。

* [ASCS/SCS テンプレート](#ASCS-SCS-template)
* [データベース テンプレート](#database-template)
* [アプリケーション サーバー テンプレート](#application-servers-template)

以降のセクションでは、テンプレートと、テンプレートで指定する必要があるパラメーターの詳細について説明します。

#### <a name="ASCS-SCS-template"></a> ASCS/SCS テンプレート

ASCS/SCS テンプレートは、複数の ASCS/SCS インスタンスをホストする Windows Server フェールオーバー クラスターの作成に使用できる 2 つの仮想マシンをデプロイします。

ASCS/SCS マルチ SID テンプレートを設定するには、[ASCS/SCS マルチ SID テンプレート][sap-templates-3-tier-multisid-xscs-marketplace-image]or [ASCS/SCS multi-SID template using Managed Disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md]で、次のパラメーターの値を入力します。

  - **Resource Prefix (リソース プレフィックス)** 。  リソース プレフィックスを設定します。これは、デプロイ中に作成されるすべてのリソースのプレフィックスとして使われます。 リソースは 1 つの SAP システムのみに属するわけではないため、リソースのプレフィックスは 1 つの SAP システムの SID ではありません。  プレフィックスは、**3 ～ 6 文字**でなければなりません。
  - **Stack Type (スタックの種類)** 。 SAP システムのスタックの種類を選びます。 スタックの種類に応じて、Azure Load Balancer には、SAP システムごとに 1 つ (ABAP または Java のみ) または 2 つ (ABAP+ Java) のプライベート IP アドレスがあります。
  -  **OS Type (OS の種類)** 。 仮想マシンのオペレーティング システムを選びます。
  -  **SAP System Count (SAP システム数)** 。 このクラスターにインストールする SAP システムの数を選択します。
  -  **System Availability (システムの可用性)** 。 **[HA]** を選択します。
  -  **Admin Username and Admin Password (管理ユーザー名と管理パスワード)** 。 マシンへのサインインに使用できる新しいユーザーを作成します。
  -  **New Or Existing Subnet (新規または既存のサブネット)** 。 新しい仮想ネットワークとサブネットを作成するか、既存のサブネットを使用するかを設定します。 オンプレミス ネットワークに接続している仮想ネットワークが既にある場合は、 **[existing (既存)]** を選択します。
  -  **Subnet Id (サブネット ID)** 。VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、ID は /subscriptions/<*サブスクリプション ID*>/resourceGroups/<*リソース グループ名*>/providers/Microsoft.Network/virtualNetworks/<*仮想ネットワーク名*>/subnets/<*サブネット名*> のようになります

テンプレートは 1 つの Azure Load Balancer インスタンスをデプロイし、これにより、複数の SAP システムがサポートされます。

- ASCS インスタンスは、インスタンス番号 00、10、20 などで構成されます。
- SCS インスタンスは、インスタンス番号 01、11、21 などで構成されます。
- ASCS Enqueue Replication Server (ERS) (Linux のみ) インスタンスは、インスタンス番号 02、12、22 などで構成されます。
- SCS ERS (Linux のみ) インスタンスは、インスタンス番号 03、13、23 などで構成されます。

ロード バランサーには、1 つ (Linux の場合は 2 つ) の VIP が含まれています。1 つは ASCS/SCS 用の VIP、1 つは ERS 用の VIP (Linux のみ) です。

次の一覧には、すべての負荷分散規則が含まれています (x は、1、2、3 などの SAP システムの番号です)。
- すべての SAP システム用の Windows 固有のポート:445、5985
- ASCS ポート (インスタンス番号 x0):32x0、36x0、39x0、81x0、5x013、5x014、5x016
- SCS ポート (インスタンス番号 x1):32x1、33x1、39x1、81x1、5x113、5x114、5x116
- Linux 上の ASCS ERS ポート (インスタンス番号 x2):33x2、5x213、5x214、5x216
- Linux 上の SCS ERS ポート (インスタンス番号 x3):33x3、5x313、5x314、5x316

ロード バランサーは、次のプローブ ポートを使うように構成されます (x は 1、2、3 などの SAP システムの番号です)。
- ASCS/SCS 内部ロード バランサー プローブ ポート:620x0
- ERS 内部ロード バランサー プローブ ポート (Linux のみ):621x2

#### <a name="database-template"></a> データベース テンプレート

データベース テンプレートは、1 つの SAP システムのリレーショナル データベース管理システム (RDBMS) のインストールに使用できる 1 つまたは 2 つの仮想マシンをデプロイします。 たとえば、5 つの SAP システムに ASCS/SCS テンプレートをデプロイする場合は、このテンプレートを 5 回デプロイする必要があります。

データベース マルチ SID テンプレートを設定するには、[データベース マルチ SID テンプレート][sap-templates-3-tier-multisid-db-marketplace-image]or [database multi-SID template using Managed Disks][sap-templates-3-tier-multisid-db-marketplace-image-md]で、次のパラメーターの値を入力します。

- **Sap System Id (SAP システム ID)** 。インストールする SAP システムの SAP システム ID を入力します。 ID は、デプロイされるリソースのプレフィックスとして使われます。
- **Os Type (OS の種類)** 。 仮想マシンのオペレーティング システムを選びます。
- **Dbtype (DB の種類)** 。 クラスターにインストールするデータベースの種類を選びます。 Microsoft SQL Server をインストールする場合は、 **[SQL]** を選択します。 仮想マシンに SAP HANA をインストールする予定の場合は、 **[HANA]** を選択します。 正しいオペレーティング システムの種類 (SQL の場合は **Windows**、HANA の場合は Linux ディストリビューション) を選択してください。 仮想マシンに接続されている Azure Load Balancer は、選んだデータベースの種類をサポートするように構成されます。
  * **SQL**。 ロード バランサーは、ポート 1433 で負荷分散を行います。 SQL Server Always On セットアップにはこのポートを使ってください。
  * **HANA**。 ロード バランサーは、ポート 35015 および 35017 で負荷分散を行います。 インスタンス番号が **50** の SAP HANA をインストールしてください。
  ロード バランサーはプローブ ポート 62550 を使います。
- **Sap System Size (SAP システムのサイズ)** 。 新しいシステムで提供する SAPS の数を設定します。 システムで必要となる SAPS の数が不明な場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
- **System Availability (システムの可用性)** 。 **[HA]** を選択します。
- **Admin Username and Admin Password (管理ユーザー名と管理パスワード)** 。 マシンへのサインインに使用できる新しいユーザーを作成します。
- **Subnet Id (サブネット ID)** 。ASCS/SCS テンプレートのデプロイ時に使ったサブネットの ID または ASCS/SCS テンプレートのデプロイの一部として作成されたサブネットの ID を入力します。

#### <a name="application-servers-template"></a> アプリケーション サーバー テンプレート

アプリケーション サーバー テンプレートは、1 つの SAP システムの SAP アプリケーション サーバー インスタンスとして使用できる 2 つ以上の仮想マシンをデプロイします。 たとえば、5 つの SAP システムに ASCS/SCS テンプレートをデプロイする場合は、このテンプレートを 5 回デプロイする必要があります。

アプリケーション サーバー マルチ SID テンプレートを設定するには、[アプリケーション サーバー マルチ SID テンプレート][sap-templates-3-tier-multisid-apps-marketplace-image]or [application servers multi-SID template using Managed Disks][sap-templates-3-tier-multisid-apps-marketplace-image-md]で、次のパラメーターの値を入力します。

  -  **Sap System Id (SAP システム ID)** 。インストールする SAP システムの SAP システム ID を入力します。 ID は、デプロイされるリソースのプレフィックスとして使われます。
  -  **Os Type (OS の種類)** 。 仮想マシンのオペレーティング システムを選びます。
  -  **Sap System Size (SAP システムのサイズ)** 。 新しいシステムで提供する SAPS の数です。 システムで必要となる SAPS の数が不明な場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
  -  **System Availability (システムの可用性)** 。 **[HA]** を選択します。
  -  **Admin Username and Admin Password (管理ユーザー名と管理パスワード)** 。 マシンへのサインインに使用できる新しいユーザーを作成します。
  -  **Subnet Id (サブネット ID)** 。ASCS/SCS テンプレートのデプロイ時に使ったサブネットの ID または ASCS/SCS テンプレートのデプロイの一部として作成されたサブネットの ID を入力します。


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure 仮想ネットワーク
このガイドの例では、Azure Virtual Network のアドレス空間は 10.0.0.0/16 です。 **Subnet** という名前のサブネットが 1 つあり、そのアドレス範囲は 10.0.0.0/24 です。 すべての仮想マシンと内部ロード バランサーは、この仮想ネットワークにデプロイされます。

> [!IMPORTANT]
> ゲスト オペレーティング システム内ではネットワーク設定をいっさい変更しないでください。 これには、IP アドレス、DNS サーバー、およびサブネットが含まれます。 ネットワークの設定はすべて Azure で構成します。 動的ホスト構成プロトコル (DHCP) サービスが設定を他に伝達します。
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP アドレス

必要な DNS IP アドレスを設定するには、次の手順を実行します。

1. Azure Portal の **[DNS サーバー]** ブレードで、仮想ネットワークの **[DNS サーバー]** オプションが **[カスタム DNS]** に設定されていることを確認します。
2. ネットワークの種類に基づいて設定を選択します。 詳細については、次のリソースを参照してください。
   * [企業ネットワーク接続 (クロスプレミス)][planning-guide-2.2]: オンプレミスの DNS サーバーの IP アドレスを追加します。  
   Azure で実行されている仮想マシンにオンプレミスの DNS サーバーを拡張できます。 その場合、DNS サービスを実行する Azure Virtual Machines の IP アドレスを追加できます。
   * Azure 内の分離された VM デプロイの場合:DNS サーバーとして機能する別の仮想マシンを同じ Virtual Network インスタンスにデプロイします。 DNS サービスを実行するように設定した Azure Virtual Machines の IP アドレスを追加します。

   ![図 12:Azure Virtual Network 用に DNS サーバーを構成する][sap-ha-guide-figure-3001]

   _**図 12:** Azure Virtual Network 用に DNS サーバーを構成する_

   > [!NOTE]
   > DNS サーバーの IP アドレスを変更した場合、変更を適用して新しい DNS サーバーに反映するには、Azure Virtual Machines を再起動する必要があります。
   >
   >

このガイドの例では、DNS サービスは次の Windows 仮想マシンにインストールされ、構成されています。

| 仮想マシンの役割 | 仮想マシンのホスト名 | ネットワーク カード名 | 静的 IP アドレス |
| --- | --- | --- | --- |
| 第 1 の DNS サーバー |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| 第 2 の DNS サーバー |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> クラスター化された SAP ASCS/SCS インスタンスとクラスター化された DBMS インスタンスのホスト名と静的 IP アドレス

オンプレミスのデプロイの場合、次の予約されたホスト名と IP アドレスが必要です。

| 仮想ホスト名の役割 | 仮想ホスト名 | 仮想静的 IP アドレス |
| --- | --- | --- |
| SAP ASCS/SCS の第 1 のクラスター仮想ホスト名 (クラスター管理用) |pr1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS インスタンスの仮想ホスト名 |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS の第 2 のクラスター仮想ホスト名 (クラスター管理用) |pr1-dbms-vir |10.0.0.32 |

クラスターを作成するときに、クラスター自体を管理する仮想ホスト名 **pr1-ascs-vir** と **pr1-dbms-vir** および関連する IP アドレスを作成します。 その方法については、「[クラスター構成内のクラスター ノードの収集][sap-ha-guide-8.12.1]」を参照してください。

他の 2 つの仮想ホスト名 **pr1-ascs-sap** と **pr1-dbms-sap** および関連する IP アドレスは、DNS サーバーで手動作成できます。 クラスター化された SAP ASCS/SCS インスタンスおよびクラスター化された DBMS インスタンスは、これらのリソースを使用します。 その方法については、「[クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成][sap-ha-guide-9.1.1]」を参照してください。

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> SAP 仮想マシンの静的 IP アドレスの設定
クラスターで使用する仮想マシンをデプロイした後は、すべての仮想マシンに静的 IP アドレスを設定する必要があります。 これは、ゲスト オペレーティング システムではなく、Azure Virtual Network の構成で行います。

1. Azure Portal で、 **[リソース グループ]**  >  **[ネットワーク カード]**  >  **[設定]**  >  **[IP アドレス]** の順に選択します。
2. **[IP アドレス]** ブレードの **[割り当て]** で **[静的]** を選択します。 **[IP アドレス]** ボックスに、使用する IP アドレスを入力します。

   > [!NOTE]
   > ネットワーク カードの IP アドレスを変更する場合は、Azure Virtual Machines を再起動して変更を適用する必要があります。  
   >
   >

   ![図 13:各仮想マシンのネットワーク カードの静的 IP アドレスを設定する][sap-ha-guide-figure-3002]

   _**図 13:** 各仮想マシンのネットワーク カードの静的 IP アドレスを設定する_

   すべてのネットワーク インターフェイス、つまり Active Directory/DNS サービスに使用する仮想マシンを含むすべての仮想マシンについて、この手順を繰り返します。

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

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Azure 内部ロード バランサーの静的 IP アドレスの設定

SAP Azure Resource Manager テンプレートでは、SAP ASCS/SCS インスタンス クラスターと DBMS クラスターで使用される Azure 内部ロード バランサーが作成されます。

> [!IMPORTANT]
> SAP ASCS/SCS の仮想ホスト名の IP アドレスは、SAP ASCS/SCS 内部ロード バランサー **pr1-lb-ascs** の IP アドレスと同じです。
> DBMS の仮想名の IP アドレスは、DBMS 内部ロード バランサー **pr1-lb-dbms** の IP アドレスと同じです。
>
>

Azure 内部ロード バランサーの静的 IP アドレスを設定するには

1. 初期デプロイでは、内部ロード バランサーの IP アドレスが**動的**に設定されます。 Azure Portal の **[IP アドレス]** ブレードの **[割り当て]** で **[静的]** を選択します。
2. 内部ロード バランサー **pr1-lb-ascs** の IP アドレスを、SAP ASCS/SCS インスタンスの仮想ホスト名の IP アドレスに設定します。
3. 内部ロード バランサー **pr1-lb-dbms** の IP アドレスを、DBMS インスタンスの仮想ホスト名の IP アドレスに設定します。

   ![図 14:SAP ASCS/SCS インスタンスの内部ロード バランサーの静的 IP アドレスを設定する][sap-ha-guide-figure-3003]

   _**図 14:** SAP ASCS/SCS インスタンスの内部ロード バランサーの静的 IP アドレスを設定する_

この例で使用する 2 つの Azure 内部ロード バランサーと静的 IP アドレスは次のとおりです。

| Azure 内部ロード バランサーの役割 | Azure 内部ロード バランサーの名前 | 静的 IP アドレス |
| --- | --- | --- |
| SAP ASCS/SCS インスタンスの内部ロード バランサー |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS の内部ロード バランサー |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則

SAP Azure Resource Manager テンプレートによって、必要なポートが作成されます。
* 既定のインスタンス番号が **00** である ABAP ASCS インスタンス
* 既定のインスタンス番号が **01** である Java SCS インスタンス

SAP ASCS/SCS インスタンスをインストールするときは、ABAP ASCS インスタンスには既定のインスタンス番号 **00** を使用し、Java SCS インスタンスには既定のインスタンス番号 **01** を使用する必要があります。

次に、SAP NetWeaver ポートに必要な内部負荷分散エンドポイントを作成します。

必要な内部負荷分散エンドポイントを作成するには、まず、SAP NetWeaver ABAP ASCS ポート用に次の負荷分散エンドポイント作成します。

| サービス/負荷分散規則名 | 既定のポート番号 | (インスタンス番号が 00 の ASCS インスタンス) (インスタンス番号が 10 の ERS) の具体的なポート |
| --- | --- | --- |
| エンキュー サーバー/ *lbrule3200* |32<*InstanceNumber*> |3200 |
| ABAP メッセージ サーバー/ *lbrule3600* |36<*InstanceNumber*> |3600 |
| 内部 ABAP メッセージ/ *lbrule3900* |39<*InstanceNumber*> |3900 |
| メッセージ サーバー HTTP/ *Lbrule8100* |81<*InstanceNumber*> |8100 |
| SAP 起動サービス ASCS HTTP/ *Lbrule50013* |5<*InstanceNumber*>13 |50013 |
| SAP 起動サービス ASCS HTTPS/ *Lbrule50014* |5<*InstanceNumber*>14 |50014 |
| エンキュー レプリケーション/ *Lbrule50016* |5<*InstanceNumber*>16 |50016 |
| SAP 起動サービス ERS HTTP *Lbrule51013* |5<*InstanceNumber*>13 |51013 |
| SAP 起動サービス ERS HTTP *Lbrule51014* |5<*InstanceNumber*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| ファイル共有 *Lbrule445* | |445 |

_**表 1:** SAP NetWeaver ABAP ASCS インスタンスのポート番号_

次に、SAP NetWeaver Java SCS ポート用に次の負荷分散エンドポイントを作成します。

| サービス/負荷分散規則名 | 既定のポート番号 | (インスタンス番号が 01 の SCS インスタンス) (インスタンス番号が 11 の ERS) の具体的なポート |
| --- | --- | --- |
| エンキュー サーバー/ *lbrule3201* |32<*InstanceNumber*> |3201 |
| ゲートウェイ サーバー/ *lbrule3301* |33<*InstanceNumber*> |3301 |
| Java メッセージ サーバー/ *lbrule3900* |39<*InstanceNumber*> |3901 |
| メッセージ サーバー HTTP/ *Lbrule8101* |81<*InstanceNumber*> |8101 |
| SAP 起動サービス SCS HTTP/ *Lbrule50113* |5<*InstanceNumber*>13 |50113 |
| SAP 起動サービス SCS HTTPS/ *Lbrule50114* |5<*InstanceNumber*>14 |50114 |
| エンキュー レプリケーション/ *Lbrule50116* |5<*InstanceNumber*>16 |50116 |
| SAP 起動サービス ERS HTTP *Lbrule51113* |5<*InstanceNumber*>13 |51113 |
| SAP起動サービス ERS HTTP *Lbrule51114* |5<*InstanceNumber*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| ファイル共有 *Lbrule445* | |445 |

_**表 2:** SAP NetWeaver Java SCS インスタンスのポート番号_

![図 15:Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則][sap-ha-guide-figure-3004]

_**図 15:** Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則_

ロード バランサー **pr1-lb-dbms** の IP アドレスを、DBMS インスタンスの仮想ホスト名の IP アドレスに設定します。

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則の変更

SAP ASCS または SCS インスタンスに別の番号を使う場合は、それらのポートの名前と値を既定値から変更する必要があります。

1. Azure Portal で、 **<[*SID*>-lb-ascs ロード バランサー]**  >  **[負荷分散規則]** の順に選びます。
2. SAP ASCS または SCS インスタンスに属するすべての負荷分散規則について、以下の値を変更します。

   * Name
   * Port
   * バックエンド ポート

   たとえば、既定の ASCS インスタンス番号を 00 から 31 に変更する場合は、表 1 に記載されているすべてのポートについて変更する必要があります。

   ポート *lbrule3200* の更新の例を次に示します。

   ![図 16:Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則を変更する][sap-ha-guide-figure-3005]

   _**図 16:** Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則を変更する_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> ドメインへの Windows 仮想マシンの追加

仮想マシンに静的 IP アドレスを割り当てた後、ドメインに仮想マシンを追加します。

![図 17:ドメインに仮想マシンを追加する][sap-ha-guide-figure-3006]

_**図 17:** ドメインに仮想マシンを追加する_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> SAP ASCS/SCS インスタンスの両方のクラスター ノードでのレジストリ エントリの追加

Azure Load Balancer は、設定されている時間 (アイドル タイムアウト) だけ接続がアイドル状態になっていると接続を閉じる内部ロード バランサーを備えています。 ダイアログ インスタンスの SAP ワーク プロセスは、最初のエンキュー/デキュー要求の送信が必要になるとすぐに、SAP エンキュー プロセスへの接続を開きます。 通常、これらの接続は、ワーク プロセスまたはエンキュー プロセスが再起動するまで確立されたままになります。 しかし、接続が一定時間アイドル状態の場合、Azure 内部ロード バランサーによって接続は閉じられます。 SAP ワーク プロセスはエンキュー プロセスへの接続が存在しなくなっていると再確立するため、これが問題になることはありません。 これらのアクティビティは SAP プロセスの開発者トレースに記録されますが、これらのトレースでは大量の追加コンテンツが作成されます。 そのため、両方のクラスター ノードで TCP/IP の `KeepAliveTime` と `KeepAliveInterval` を変更することをお勧めします。 TCP/IP パラメーターでのこれらの変更と、後で説明する SAP プロファイル パラメーターを組み合わせます。

SAP ASCS/SCS インスタンスの両方のクラスター ノードでレジストリ エントリを追加するには、まず、SAP ASCS/SCS の両方の Windows クラスター ノードに次の Windows レジストリ エントリを追加します。

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 変数名 |`KeepAliveTime` |
| 変数の型 |REG_DWORD (Decimal) |
| 値 |120000 |
| ドキュメントへのリンク |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**表 3:** 第 1 の TCP/IP パラメーターを変更する_

次に、SAP ASCS/SCS の両方の Windows クラスター ノードで次の Windows レジストリ エントリを追加します。

| Path | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 変数名 |`KeepAliveInterval` |
| 変数の型 |REG_DWORD (Decimal) |
| 値 |120000 |
| ドキュメントへのリンク |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**表 4:** 第 2 の TCP/IP パラメーターを変更する_

**変更を適用するには、両方のクラスター ノードを再起動します**。

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> SAP ASCS/SCS インスタンスの Windows Server フェールオーバー クラスタリング クラスターのセットアップ

SAP ASCS/SCS インスタンスの Windows Server フェールオーバー クラスタリング クラスターのセットアップには、以下のタスクが含まれます。

- クラスター構成内のクラスター ノードの収集
- クラスターのファイル共有監視の構成

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> クラスター構成内のクラスター ノードの収集

1. 役割と機能の追加ウィザードで、両方のクラスター ノードにフェールオーバー クラスタリングを追加します。
2. フェールオーバー クラスター マネージャーを使用して、フェールオーバー クラスターをセットアップします。 フェールオーバー クラスター マネージャーで **[クラスターの作成]** を選択し、1 つ目のクラスターであるノード A の名前だけを追加します。2 つ目のノードは、まだ追加しないでください。2 つ目のノードは、後の手順で追加します。

   ![図 18:第 1 のクラスター ノードのサーバー名または仮想マシン名を追加する][sap-ha-guide-figure-3007]

   _**図 18:** 第 1 のクラスター ノードのサーバー名または仮想マシン名を追加する_

3. クラスターのネットワーク名 (仮想ホスト名) を入力します。

   ![図 19:クラスター名を入力する][sap-ha-guide-figure-3008]

   _**図 19:** クラスター名を入力する_

4. クラスターを作成した後、クラスター検証テストを実行します。

   ![図 20:クラスター検証チェックを実行する][sap-ha-guide-figure-3009]

   _**図 20:** クラスター検証チェックを実行する_

   プロセスのこの時点では、ディスクに関する警告は無視してかまいません。 ファイル共有監視と SIOS 共有ディスクは後で追加します。 この段階では、クォーラムについて心配する必要はありません。

   ![図 21:クォーラム ディスクが見つからない][sap-ha-guide-figure-3010]

   _**図 21:** クォーラム ディスクが見つからない_

   ![図 22:コア クラスター リソースに新しい IP アドレスが必要である][sap-ha-guide-figure-3011]

   _**図 22:** コア クラスター リソースに新しい IP アドレスが必要である_

5. コア クラスター サービスの IP アドレスを変更します。 サーバーの IP アドレスが仮想マシン ノードの 1 つを指しているため、コア クラスター サービスの IP アドレスを変更するまではクラスターを開始できません。 この操作は、コア クラスター サービスの IP リソースの **[プロパティ]** ページで行います。

   たとえば、クラスターの仮想ホスト名 **pr1-ascs-vir** の IP アドレス (このガイドの例では **10.0.0.42**) を割り当てる必要があります。

   ![図 23:[プロパティ] ダイアログ ボックスで IP アドレスを変更する][sap-ha-guide-figure-3012]

   _**図 23:**  **[プロパティ]** ダイアログ ボックスで IP アドレスを変更する_

   ![図 24:このクラスター用に予約された IP アドレスを割り当てる][sap-ha-guide-figure-3013]

   _**図 24:** このクラスター用に予約された IP アドレスを割り当てる_

6. クラスター仮想ホスト名をオンラインにします。

   ![図 25:正しい IP アドレスでコア クラスター サービスが起動して実行されている][sap-ha-guide-figure-3014]

   _**図 25:** 正しい IP アドレスでコア クラスター サービスが起動して実行されている_

7. 2 つ目のクラスター ノードを追加します。

   コア クラスター サービスが起動して実行されているので、第 2 のクラスター ノードを追加できます。

   ![図 26:第 2 のクラスター ノードを追加する][sap-ha-guide-figure-3015]

   _**図 26:** 第 2 のクラスター ノードを追加する_

8. 2 つ目のクラスター ノード ホストの名前を入力します。

   ![図 27:第 2 のクラスター ノード ホストの名前を入力する][sap-ha-guide-figure-3016]

   _**図 27:** 第 2 のクラスター ノード ホストの名前を入力する_

   > [!IMPORTANT]
   > **[使用可能な記憶域をすべてクラスターに追加する]** チェック ボックスがオンになって**いない**ことを確認してください。  
   >
   >

   ![図 28:このチェック ボックスはオンにしない][sap-ha-guide-figure-3017]

   _**図 28:** このチェック ボックスはオンに**しない**_

   クォーラムとディスクに関する警告は無視して構いません。 「[SAP ASCS/SCS クラスター共有ディスクのための SIOS DataKeeper Cluster Edition のインストール][sap-ha-guide-8.12.3]」で説明されているように、クォーラムと共有ディスクは後で設定します。

   ![図 29:ディスク クォーラムに関する警告を無視する][sap-ha-guide-figure-3018]

   _**図 29:** ディスク クォーラムに関する警告を無視する_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> クラスターのファイル共有監視の構成

クラスターのファイル共有監視の構成には、以下のタスクが含まれます。

- ファイル共有の作成
- フェールオーバー クラスター マネージャーでのファイル共有監視クォーラムの設定

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> ファイル共有の作成

1. クォーラム ディスクではなく、ファイル共有監視を選択します。 SIOS DataKeeper は、このオプションをサポートしています。

   この記事の例では、ファイル共有監視は Azure で実行されている Active Directory/DNS サーバー上にあります。 ファイル共有監視の名前は **domcontr-0** です。 Azure への VPN 接続を構成したので (サイト間 VPN または Azure ExpressRoute 経由)、Active Directory/DNS サービスはオンプレミスであり、ファイル共有監視の実行には適していません。

   > [!NOTE]
   > Active Directory/DNS サービスがオンプレミスのみで実行している場合は、オンプレミスで実行している Active Directory/DNS Windows オペレーティング システムには、ファイル共有監視を構成しないでください。 Azure で実行しているクラスター ノードとオンプレミスの Active Directory/DNS の間のネットワーク待機時間が長すぎて、接続の問題が発生する可能性があります。 ファイル共有監視は、クラスター ノードの近くで動いている Azure Virtual Machines に構成する必要があります。  
   >
   >

   クォーラム ドライブには、1,024 MB 以上の空き領域が必要です。 クォーラム ドライブでは空き領域を 2,048 MB にすることをお勧めします。

2. クラスター名オブジェクトを追加します。

   ![図 30:クラスター名オブジェクトの共有に対するアクセス許可を割り当てる][sap-ha-guide-figure-3019]

   _**図 30:** クラスター名オブジェクトの共有に対するアクセス許可を割り当てる_

   アクセス許可には、クラスター名オブジェクト (この例では **pr1-ascs-vir$** ) の共有内のデータを変更する権限を必ず含めます。

3. クラスター名オブジェクトをリストに追加するには、 **[追加]** を選択します。 図 31 に示されているものに加えて、コンピューター オブジェクトをチェックするようにフィルターを変更します。

   ![図 31:コンピューターを含むようにオブジェクトの種類を変更する][sap-ha-guide-figure-3020]

   _**図 31:** コンピューターを含むようにオブジェクトの種類を変更する_

   ![図 32:[コンピューター] チェック ボックスをオンにする][sap-ha-guide-figure-3021]

   _**図 32:**  **[コンピューター]** チェック ボックスをオンにする_

4. 図 31 に示すように、クラスター名オブジェクトを入力します。 レコードは既に作成されているので、図 30 に示すようにアクセス許可を変更できます。

5. 共有の **[セキュリティ]** タブを選択し、クラスター名オブジェクトのアクセス許可をさらに詳しく設定します。

   ![図 33:ファイル共有クォーラムでクラスター名オブジェクトのセキュリティ属性を設定する][sap-ha-guide-figure-3022]

   _**図 33:** ファイル共有クォーラムでクラスター名オブジェクトのセキュリティ属性を設定する_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> フェールオーバー クラスター マネージャーでのファイル共有監視クォーラムの設定

1. クラスター クォーラム構成ウィザードを開きます。

   ![図 34:クラスター クォーラム設定の構成ウィザードを開始する][sap-ha-guide-figure-3023]

   _**図 34:** クラスター クォーラム設定の構成ウィザードを開始する_

2. **[クォーラム構成の選択]** ページで、 **[クォーラム監視を選択する]** を選択します。

   ![図 35:選択できるクォーラム構成][sap-ha-guide-figure-3024]

   _**図 35:** 選択できるクォーラム構成_

3. **[クォーラム監視の選択]** ページで、 **[ファイル共有監視を構成する]** を選択します。

   ![図 36:ファイル共有監視を選択する][sap-ha-guide-figure-3025]

   _**図 36:** ファイル共有監視を選択する_

4. ファイル共有の UNC パスを入力します (この例では \\domcontr-0\FSW)。 **[次へ]** を選択して、行うことができる変更の一覧を表示します。

   ![図 37:監視共有のファイル共有の場所を定義する][sap-ha-guide-figure-3026]

   _**図 37:** 監視共有のファイル共有の場所を定義する_

5. 必要な変更を選択し、 **[次へ]** を選択します。 図 38 に示すように、クラスター構成が正常に再構成されている必要があります。  

   ![図 38:クラスターが再構成されたことを確認する][sap-ha-guide-figure-3027]

   _**図 38:** クラスターが再構成されたことを確認する_

Windows フェールオーバー クラスターが正常にインストールされた後、フェールオーバーの検出を Azure での条件に合わせるように、いくつかのしきい値を変更する必要があります。 変更されるパラメーターは、 https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ のブログに記載されています。 ASCS/SCS 用に Windows クラスター構成を構築する 2 つの VM が同じサブネットにあることを前提とした場合、次のパラメーターを次の値に変更する必要があります。
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

これらの設定は、お客様と協力してテストし、十分な回復性という面で妥当な結果が得られました。 一方、SAP ソフトウェアでの実際のエラー状態やノード/VM 障害では、十分な速度のフェールオーバーを提供しました。 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS クラスター共有ディスクのための SIOS DataKeeper Cluster Edition のインストール

これで、Azure で動作する Windows Server フェールオーバー クラスタリング構成が完了しました。 ただし、SAP ASCS/SCS インスタンスをインストールするには、共有ディスク リソースが必要です。 Azure では必要な共有ディスク リソースを作成できません。 サード パーティ製ソリューションの SIOS DataKeeper Cluster Edition を使用して、共有ディスク リソースを作成できます。

SAP ASCS/SCS クラスター共有ディスク用の SIOS DataKeeper Cluster Edition のインストールには、次のタスクが含まれます。

- .NET Framework 3.5 の追加
- SIOS DataKeeper のインストール
- SIOS DataKeeper のセットアップ

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> .NET Framework 3.5 の追加
Windows Server 2012 R2 では、Microsoft .NET Framework 3.5 は自動的に有効化またはインストールされません。 SIOS DataKeeper では、DataKeeper をインストールするすべてのノードで .NET Framework が必要になるため、クラスター内のすべての仮想マシンのゲスト オペレーティング システムに .NET Framework 3.5 をインストールする必要があります。

.NET Framework 3.5 を追加するには、次の 2 つの方法があります。

- 図 39 に示すように、Windows の役割と機能の追加ウィザードを使用します。

  ![図 39:役割と機能の追加ウィザードを使用して .NET Framework 3.5 をインストールする][sap-ha-guide-figure-3028]

  _**図 39:** 役割と機能の追加ウィザードを使用して .NET Framework 3.5 をインストールする_

  ![図 40:役割と機能の追加ウィザードを使用して .NET Framework 3.5 をインストールするときのインストール進行状況バー][sap-ha-guide-figure-3029]

  _**図 40:** 役割と機能の追加ウィザードを使用して .NET Framework 3.5 をインストールするときのインストール進行状況バー_

- コマンド ライン ツールの dism.exe を使用します。 この種のインストールでは、Windows インストール メディアの SxS ディレクトリにアクセスする必要があります。 管理者特権でのコマンド プロンプトで、次のように入力します。

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> SIOS DataKeeper のインストール

クラスターの各ノードに SIOS DataKeeper Cluster Edition をインストールします。 SIOS DataKeeper で仮想共有記憶域を作成するには、同期されたミラーを作成し、クラスター共有記憶域をシミュレートします。

SIOS ソフトウェアをインストールする前に、ドメイン ユーザー **DataKeeperSvc** を作成します。

> [!NOTE]
> **DataKeeperSvc** ユーザーを、両方のクラスター ノードの**ローカルの Administrator** グループに追加します。
>
>

SIOS DataKeeper をインストールするには

1. SIOS ソフトウェアを両方のクラスター ノードにインストールします。

   ![SIOS インストーラー][sap-ha-guide-figure-3030]

   ![図 41:SIOS DataKeeper のインストールの最初のページ][sap-ha-guide-figure-3031]

   _**図 41:** SIOS DataKeeper のインストールの最初のページ_

2. 図 42 のダイアログ ボックスで、 **[Yes (はい)]** を選択します。

   ![図 42:DataKeeper からサービスを無効にすることが通知される][sap-ha-guide-figure-3032]

   _**図 42:** DataKeeper からサービスを無効にすることが通知される_

3. 図 43 のダイアログ ボックスでは、 **[Domain or Server account (ドメインまたはサーバーのアカウント)]** を選択することをお勧めします。

   ![図 43:SIOS DataKeeper のユーザー選択][sap-ha-guide-figure-3033]

   _**図 43:** SIOS DataKeeper のユーザー選択_

4. SIOS DataKeeper 用に作成したドメイン アカウントのユーザー名とパスワードを入力します。

   ![図 44:SIOS DataKeeper インストールのドメイン ユーザー名とパスワードを入力する][sap-ha-guide-figure-3034]

   _**図 44:** SIOS DataKeeper インストールのドメイン ユーザー名とパスワードを入力する_

5. 図 45 に示すように、SIOS DataKeeper インスタンスのライセンス キーをインストールします。

   ![図 45:SIOS DataKeeper ライセンス キーを入力する][sap-ha-guide-figure-3035]

   _**図 45:** SIOS DataKeeper ライセンス キーを入力する_

6. メッセージが表示されたら、仮想マシンを再起動します。

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> SIOS DataKeeper のセットアップ

両方のノードに SIOS DataKeeper をインストールした後、構成を開始する必要があります。 この構成の目的は、各仮想マシンに接続された追加ディスク間で同期データ レプリケーションを実現することです。

1. DataKeeper の管理および構成ツールを起動し、 **[Connect to Server (サーバーに接続)]** を選択します (図 46 の赤い丸で囲んだオプション)。

   ![図 46:SIOS DataKeeper の管理および構成ツール][sap-ha-guide-figure-3036]

   _**図 46:** SIOS DataKeeper の管理および構成ツール_

2. 管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードを入力します。

   ![図 47:管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードの情報を入力する][sap-ha-guide-figure-3037]

   _**図 47:** 管理および構成ツールが接続する 1 番目のノードの名前または TCP/IP アドレスを入力し、次の手順で 2 番目のノードの情報を入力する_

3. 2 つのノード間のレプリケーション ジョブを作成します。

   ![図 48:レプリケーション ジョブを作成する][sap-ha-guide-figure-3038]

   _**図 48:** レプリケーション ジョブを作成する_

   ウィザードでレプリケーション ジョブの作成手順が示されます。
4. ソース ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義します。

   ![図 49:レプリケーション ジョブの名前を定義する][sap-ha-guide-figure-3039]

   _**図 49:** レプリケーション ジョブの名前を定義する_

   ![図 50:現在のソース ノードとして使用するノードの基本データを定義する][sap-ha-guide-figure-3040]

   _**図 50:** 現在のソース ノードとして使用するノードの基本データを定義する_

5. ターゲット ノードの名前、TCP/IP アドレス、ディスク ボリュームを定義します。

   ![図 51:現在のターゲット ノードとして使用するノードの基本データを定義する][sap-ha-guide-figure-3041]

   _**図 51:** 現在のターゲット ノードとして使用するノードの基本データを定義する_

6. 圧縮アルゴリズムを定義します。 この例では、レプリケーション ストリームを圧縮することをお勧めします。 特に再同期の状況では、レプリケーション ストリームを圧縮すると、再同期時間が大幅に短縮されます。 圧縮では仮想マシンの CPU および RAM リソースが使用されることに注意してください。 圧縮率を大きくするほど、使用される CPU リソースの量も増えます。 この設定は後で調整することもできます。

7. 確認する必要があるもう 1 つの設定は、レプリケーションを非同期と同期のどちらで行うかです。 *SAP ASCS/SCS の構成を保護する場合は、同期レプリケーションを使用する必要があります*。  

   ![図 52:レプリケーションの詳細を定義する][sap-ha-guide-figure-3042]

   _**図 52:** レプリケーションの詳細を定義する_

8. レプリケーション ジョブによってレプリケートされたボリュームを、Windows Server フェールオーバー クラスタリングのクラスター構成に共有ディスクとして示すかどうかを定義します。 SAP ASCS/SCS の構成では、Windows クラスターがレプリケートされたボリュームをクラスター ボリュームとして使用できる共有ディスクとして認識するように、 **[はい]** を選択します。

   ![図 53:[はい] を選択し、レプリケートされたボリュームをクラスター ボリュームとして設定する][sap-ha-guide-figure-3043]

   _**図 53:**  **[はい]** を選択し、レプリケートされたボリュームをクラスター ボリュームとして設定する_

   ボリュームが作成された後、DataKeeper の管理および構成ツールではレプリケーション ジョブがアクティブであると表示されます。

   ![図 54:SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリングがアクティブになっている][sap-ha-guide-figure-3044]

   _**図 54:** SAP ASCS/SCS 共有ディスクの DataKeeper 同期ミラーリングがアクティブになっている_

   図 55 のように、フェールオーバー クラスター マネージャーでディスクが DataKeeper ディスクとして表示されるようになりました。

   ![図 55:DataKeeper がレプリケートしたディスクがフェールオーバー クラスター マネージャーに表示される][sap-ha-guide-figure-3045]

   _**図 55:** DataKeeper がレプリケートしたディスクがフェールオーバー クラスター マネージャーに表示される_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> SAP NetWeaver システムのインストール

DBMS のセットアップは使用する DBMS システムによって異なるため、ここでは説明はしません。 ただし、さまざまな DBMS ベンダーが Azure でサポートする機能を使用して、DBMS に関する高可用性の問題に対処していることを想定しています。 たとえば、SQL Server の Always On またはデータベース ミラーリング、Oracle データベースの Oracle Data Guard があります。 この記事のシナリオでは、DBMS の保護は強化しませんでした。

異なる DBMS サービスが Azure のこの種のクラスター化された SAP ASCS/SCS 構成と対話する際に、特別な考慮事項はありません。

> [!NOTE]
> SAP NetWeaver ABAP システム、Java システム、ABAP + Java システムのインストール手順はほとんど同じです。 最も重要な違いは、SAP ABAP システムでは 1 つの ASCS インスタンスを使用することです。 SAP Java システムは 1 つの SCS インスタンスを使用します。 SAP ABAP + Java システムでは、1 つの ASCS インスタンスと、同じ Microsoft フェールオーバー クラスター グループで実行する 1 つの SCS インスタンスを使用します。 各 SAP NetWeaver インストール スタックのインストールの違いについて明確に説明します。 他のすべての部分は同じであると考えることができます。  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> 高可用性 ASCS/SCS インスタンスでの SAP のインストール

> [!IMPORTANT]
> DataKeeper でミラー化されたボリュームにページ ファイルを配置しないようにします。 DataKeeper はミラー化されたボリュームをサポートしていません。 ページ ファイルは、既定の場所である Azure Virtual Machines の一時ドライブ D のままにしておいてかまいません。 まだそこにない場合は、Windows ページ ファイルを Azure 仮想マシンのドライブ D: に移動します。
>
>

高可用性 ASCS/SCS インスタンスでの SAP のインストールには、以下のタスクが含まれます。

- クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成
- 最初の SAP クラスター ノードのインストール
- ASCS/SCS インスタンスの SAP プロファイルの変更
- プローブ ポートの追加
- Windows ファイアウォール プローブ ポートを開く

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成

1. Windows DNS マネージャーで、ASCS/SCS インスタンスの仮想ホスト名の DNS エントリを作成します。

   > [!IMPORTANT]
   > ASCS/SCS インスタンスの仮想ホスト名に割り当てる IP アドレスは Azure Load Balancer ( **<*SID*>-lb-ascs**) に割り当てた IP アドレスと同じでなければなりません。  
   >
   >

   SAP ASCS/SCS の仮想ホスト名 (**pr1-ascs-sap**) の IP アドレスは、Azure Load Balancer (**pr1-lb-ascs**) の IP アドレスと同じです。

   ![図 56:SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する][sap-ha-guide-figure-3046]

   _**図 56:** SAP ASCS/SCS クラスターの仮想名と TCP/IP アドレスの DNS エントリを定義する_

2. 仮想ホスト名に割り当てる IP アドレスを定義するには、 **[DNS マネージャー]**  >  **[ドメイン]** の順に選択します。

   ![図 57:SAP ASCS/SCS クラスター構成の新しい仮想名と TCP/IP アドレス][sap-ha-guide-figure-3047]

   _**図 57:** SAP ASCS/SCS クラスター構成の新しい仮想名と TCP/IP アドレス_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> 最初の SAP クラスター ノードのインストール

1. クラスター ノード A で、最初のクラスター ノード オプションを実行します。たとえば、**pr1-ascs-0** ホストで実行します。
2. Azure 内部ロード バランサーの既定のポートを維持するには、以下を選択します。

   * **ABAP システム**:**ASCS** インスタンス番号 **00**
   * **Java システム**:**SCS** インスタンス番号 **01**
   * **ABAP+Java システム**:**ASCS** インスタンス番号 **00** と **SCS** インスタンス番号 **01**

   他のインスタンス番号 (ABAP ASCS インスタンスでは 00 以外、Java SCS インスタンスでは 01 以外) を使用するには、「[Azure 内部ロード バランサーの既定の ASCS/SCS 負荷分散規則の変更][sap-ha-guide-8.9]」の説明に従って、Azure 内部ロード バランサーの既定の負荷分散規則を変更する必要があります。

次のいくつかのタスクは、通常の SAP インストール ドキュメントには記載されていません。

> [!NOTE]
> SAP インストール ドキュメントでは、最初の ASCS/SCS クラスター ノードをインストールする方法が説明されています。
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> ASCS/SCS インスタンスの SAP プロファイルの変更

新しいプロファイル パラメーターを追加する必要があります。 このプロファイル パラメーターにより、SAP ワーク プロセスとエンキュー サーバー間の接続が長時間にわたってアイドル状態のときに、接続が閉じられるのを防ぐことができます。 「[SAP ASCS/SCS インスタンスの両方のクラスター ノードでのレジストリ エントリの追加][sap-ha-guide-8.11]」では、問題のシナリオについて説明しました。 また、一部の基本的な TCP/IP 接続パラメーターに対する 2 つの変更も行いました。 第 2 の手順では、接続が Azure 内部ロード バランサーのアイドルしきい値に達しないようにするために、`keep_alive` 信号を送信するようにエンキュー サーバーを構成する必要があります。

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

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> プローブ ポートの追加

内部ロード バランサーのプローブ機能を使用して、クラスター全体の構成が Azure Load Balancer で動作するようにします。 通常、Azure 内部ロード バランサーは、参加している仮想マシン間に受信ワークロードを均等に分散させます。 ただし、アクティブなインスタンスが 1 つだけであるため、一部のクラスター構成では動作しません。 他のインスタンスはパッシブであり、ワークロードを受け付けることができません。 プローブ機能は、Azure 内部ロード バランサーがアクティブなインスタンスにのみワークロードを割り当てる場合に役に立ちます。 プローブ機能により、内部ロード バランサーはアクティブなインスタンスを検出して、そのインスタンスだけをワークロードのターゲットにすることができます。

プローブ ポートを追加するには

1. 次の PowerShell コマンドを実行して、現在の **ProbePort** の設定を確認します。 クラスター構成内のいずれかの仮想マシンで実行します。

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. プローブ ポートを定義します。 既定のプローブ ポート番号は **0** です。 この例では、プローブ ポート **62000** を使用します。

   ![図 58:クラスター構成のプローブ ポートは既定で 0 に設定されている][sap-ha-guide-figure-3048]

   _**図 58:** 既定のクラスター構成のプローブ ポートは 0 に設定されている_

   ポート番号は、SAP Azure Resource Manager テンプレートで定義されています。 PowerShell でポート番号を割り当てることができます。

   **SAP <*SID*> IP** クラスター リソースの新しい ProbePort 値を設定するには、次の PowerShell スクリプトを実行します。 環境に合わせて PowerShell 変数を更新してください。 スクリプトを実行した後、変更を有効にするために SAP クラスター グループを再起動するよう求められます。

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

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

   **SAP <*SID*>** クラスター ロールをオンラインにした後、**ProbePort** が新しい値に設定されていることを確認します。

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![図 59:新しい値を設定した後でクラスター ポートをプローブする][sap-ha-guide-figure-3049]

   _**図 59:** 新しい値を設定した後でクラスター ポートをプローブする_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Windows ファイアウォール プローブ ポートを開く

両方のクラスター ノードで Windows ファイアウォール プローブ ポートを開く必要があります。 Windows ファイアウォール プローブ ポートを開くには、次のスクリプトを使用します。 環境に合わせて PowerShell 変数を更新してください。

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** は **62000** に設定されています。 これで、**ascsha-dbas** などの他のホストから、ファイル共有 **\\\ascsha-clsap\sapmnt** にアクセスできるようになりました。

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> データベース インスタンスのインストール

データベース インスタンスをインストールするには、SAP インストール ドキュメントに記載されている手順に従います。

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> 第 2 のクラスター ノードのインストール

第 2 のクラスター ノードをインストールするには、SAP インストール ガイドの手順に従います。

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> SAP ERS Windows サービスのインスタンスのスタートアップの種類の変更

両方のクラスター ノードで、SAP ERS Windows サービスの開始の種類を **[自動 (遅延開始)]** に変更します。

![図 60:SAP ERS インスタンスのサービスの種類を遅延自動に変更する][sap-ha-guide-figure-3050]

_**図 60:** SAP ERS インスタンスのサービスの種類を遅延自動に変更する_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP プライマリ アプリケーション サーバーのインストール

プライマリ アプリケーション サーバー (PAS) のインスタンス <*SID*>-di-0 を、PAS のホストとして指定した仮想マシンにインストールします。 Azure または DataKeeper 固有の設定に対する依存関係はありません。

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP 追加アプリケーション サーバーのインストール

SAP アプリケーション サーバー インスタンスのホストとして指定したすべての仮想マシンに、SAP 追加アプリケーション サーバー (AAS) をインストールします。 たとえば、<*SID*>-di-1 では <*SID*>-di-&lt;n&gt; にします。

> [!NOTE]
> これで、高可用性 SAP NetWeaver システムのインストールが完了します。 次に、フェールオーバーのテストに進みます。
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> SAP ASCS/SCS インスタンスのフェールオーバーと SIOS レプリケーションのテスト
フェールオーバー クラスター マネージャーと SIOS DataKeeper の管理および構成ツールを使用して、SAP ASCS/SCS インスタンスのフェールオーバーと SIOS ディスク レプリケーションを簡単にテストおよび監視できます。

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS インスタンスがクラスター ノード A で動作している状態

**SAP PR1** クラスター グループが、クラスター ノード A (たとえば **pr1-ascs-0**) で動作しています。 **SAP PR1** クラスター グループに含まれ、ASCS/SCS インスタンスが使用する共有ディスク ドライブ S を、クラスター ノード A に割り当てます。

![図 61:フェールオーバー クラスター マネージャー:SAP <SID> クラスター グループがクラスター ノード A で動作している][sap-ha-guide-figure-5000]

_**図 61:** フェールオーバー クラスター マネージャー:SAP <*SID*> クラスター グループがクラスター ノード A で動作している_

SIOS DataKeeper の管理および構成ツールで、共有ディスクのデータがクラスター ノード A (例: **pr1-ascs-0 [10.0.0.40]** ) のソース ボリューム ドライブ S から、クラスター ノード B (例: **pr1-ascs-1 [10.0.0.41]** ) のターゲット ボリューム ドライブ S に同期的にレプリケートされることを確認できます。

![図 62:SIOS DataKeeper で、クラスター ノード A からクラスター ノード B にローカル ボリュームをレプリケートする][sap-ha-guide-figure-5001]

_**図 62:** SIOS DataKeeper で、クラスター ノード A からクラスター ノード B にローカル ボリュームをレプリケートする_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> ノード A からノード B へのフェールオーバー

1. 次のいずれかの方法を選択して、クラスター ノード A からクラスター ノード B への SAP <*SID*> クラスター グループのフェールオーバーを開始します。
   - フェールオーバー クラスター マネージャーを使用します  
   - 次のフェールオーバー クラスター PowerShell を使用します

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Windows ゲスト オペレーティング システムでクラスター ノード A を再起動します (ノード A からノード B への SAP <*SID*> クラスター グループの自動フェールオーバーが開始されます)。  
3. Azure Portal からクラスター ノード A を再起動します (ノード A からノード B への SAP <*SID*> クラスター グループの自動フェールオーバーが開始されます)。  
4. Azure PowerShell を使用してクラスター ノード A を再起動します (ノード A からノード B への SAP <*SID*> クラスター グループの自動フェールオーバーが開始されます)。

   フェールオーバーの後、SAP <*SID*> クラスター グループは、クラスター ノード B (たとえば **pr1-ascs-1**) で動作しています。

   ![図 63:フェールオーバー クラスター マネージャーで SAP <SID> クラスター グループがクラスター ノード B で動作している][sap-ha-guide-figure-5002]

   _**図 63**:フェールオーバー クラスター マネージャーで SAP <*SID*> クラスター グループがクラスター ノード B で動作している_

   現在、共有ディスクはクラスター ノード B にマウントされています。SIOS DataKeeper は、クラスター ノード B (例: **pr1-ascs-1 [10.0.0.41]** ) のソース ボリューム ドライブ S から、クラスター ノード A (例: **pr1-ascs-0 [10.0.0.40]** ) のターゲット ボリューム ドライブ S に、データをレプリケートします。

   ![図 64:SIOS DataKeeper で、クラスター ノード B からクラスター ノード A にローカル ボリュームをレプリケートする][sap-ha-guide-figure-5003]

   _**図 64:** SIOS DataKeeper で、クラスター ノード B からクラスター ノード A にローカル ボリュームをレプリケートする_
