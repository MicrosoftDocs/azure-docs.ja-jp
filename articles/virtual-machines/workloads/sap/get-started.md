---
title: Azure VM で SAP を使用する | Microsoft Docs
description: Microsoft Azure 上の仮想マシン (VM) で実行される SAP ソリューションについて説明します
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40ed06bef45948068e3845e728d9c1d63ed62e71
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762805"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Azure を使用して SAP ワークロードをホストして実行するシナリオ

Microsoft Azure を選択することで、ミッション クリティカルな SAP ワークロードとシナリオを、拡張性が高く、SAP に準拠した、エンタープライズで実証済みのプラットフォームで確実に実行できます。  Azure を使用して、拡張性と柔軟性、そしてコストの削減を実現しましょう。 Microsoft と SAP のパートナーシップの拡大により、Azure で開発/テストから運用までのシナリオを通じて SAP アプリケーションを実行できるうえ、そのすべてがサポートされます。 SAP NetWeaver から SAP S/4HANA、SAP BI、Linux から Windows、SAP HANA から SQL まで対応しています。

SAP NetWeaver シナリオをさまざまな DBMS を使用して Azure でホストすることに加え、その他の SAP ワークロード シナリオ (SAP BI など) も Azure でホストできます。 

Azure for SAP HANA の独自性は、Azure を他社と一線を画すものにしているオファーにあります。 SAP HANA が関与するより多くのメモリと CPU リソースを必要とする SAP シナリオをホストできるようにするため、Azure では、お客様専用のベア メタル ハードウェアの使用を提案しています。その目的は、S/4HANA またはその他の SAP HANA ワークロードで最大 24 TB (120 TB のスケールアウト) のメモリを必要とする SAP HANA のデプロイを実行することです。 このSAP HANA on Azure (L インスタンス) の独自の Azure ソリューションによって、SAP アプリケーション レイヤーまたはネイティブ Azure Virtual Machines でホストされるワークロードのミドルウェア レイヤーを使用して、SAP HANA を専用のベア メタル ハードウェア上で実行できます。 このソリューションについては、さまざまなドキュメントの「SAP HANA on Azure (L インスタンス)」セクションで説明されています。   

Azure で SAP ワークロードをホストするシナリオでは、さまざまな SAP コンポーネントと SAP SaaS または PaaS 製品に対する Activity Directory を使用した ID 統合要件とシングル サインオンを構成できます。 Azure Active Directory (AAD) と SAP のエンティティを使用したこのような統合とシングル サインオンのシナリオについては、「AAD SAP ID 統合およびシングル サインオン」セクションに一覧と説明があります。

## <a name="latest-changes"></a>最新の変更

[Azure portal を介した Azure HANA L インスタンスの制御](hana-li-portal.md)のリリース

[SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](high-availability-guide-suse-netapp-files.md)のリリース

Azure Load Balancer と関連した **Linux OS パラメーター net.ipv4.tcp_timestamps** の設定の明確化

[Azure Availability Zones での SAP ワークロードの構成](sap-ha-availability-zones.md)のリリース

[SAP ワークロードの計画とデプロイ チェックリスト](sap-deployment-checklist.md)のリリース




## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (L インスタンス) の SAP HANA

一連のドキュメントでは、SAP HANA on Azure (Large Instances)、または略して HANA Large Instances について説明します。 一覧に示されている HANA Large Instances の領域について説明しているドキュメントです。

- [SAP HANA on Azure (Large Instances) の概要](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [SAP HANA on Azure (Large Instances) のアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [SAP HANA on Azure (Large Instances) のインフラストラクチャと接続](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [SAP HANA on Azure (Large Instances) への SAP HANA のインストール](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [SAP HANA on Azure (Large Instances) の高可用性とディザスター リカバリー](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [SAP HANA on Azure (Large Instances) のトラブルシューティングと監視](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

次のステップ:

- [SAP HANA on Azure (Large Instances) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SAP HANA
ドキュメントのこのセクションでは、SAP HANA のさまざまな側面について説明します。 前提条件として、Azure IaaS の基本的なサービスを提供する Azure のプリンシパル サービスをよく理解している必要があります (主に Azure のコンピューティング、ストレージ、およびネットワーキングに関する知識が必要)。 これらのトピックの多くは、SAP NetWeaver に関連する [Azure 計画ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)で取り扱っています。 

HANA on Azure に特化したドキュメントは、次に示す記事とそれに従属する記事の一覧で構成されます。

- [クイック スタート:Azure VM への単一インスタンスの SAP HANA の手動インストール](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Azure に SAP S/4HANA または BW/4HANA をデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Azure における SAP HANA インフラストラクチャの構成と運用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 仮想マシンの SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [1 つの Azure リージョン内での SAP HANA の可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Azure リージョンの枠を越えた SAP HANA の可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 仮想マシン上の SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [ファイル レベルの SAP HANA Azure バックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [ストレージ スナップショットに基づいた SAP HANA のバックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure Virtual Machines 上にデプロイされた SAP NetWeaver
このセクションでは、SAP NetWeaver および Business One on Azure の計画とデプロイに関するドキュメントを紹介します。 この章で示すドキュメントでは、Azure 上での HANA 以外のデータベースと SAP ワークロードに関する基本と使用について重点的に説明しています。 一方、HA に関するドキュメントと記事では、同様に Azure での HANA の高可用性に関する基礎が説明されています。 以下に記事を一覧表示します。

- [Azure Virtual Machines 上の SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Azure に SAP ERP 6.0 向け SAP IDES EHP7 SP3 をデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Microsoft Azure SUSE Linux VM での SAP NetWeaver の実行](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver のための Azure Virtual Machines の計画と実装](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver のための Azure Virtual Machines のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Site Recovery を使用して多層 SAP NetWeaver アプリケーションのデプロイを保護する](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Azure 用の SAP LaMa コネクタ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Azure 上の SAP ワークロードの下での HANA 以外のデータベースに関するドキュメントを以下に一覧表示します。

- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver のための SQL Server Azure Virtual Machines DBMS のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [SAP ワークロードのための Oracle Azure Virtual Machines DBMS のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [SAP ワークロードのための IBM DB2 Azure Virtual Machines DBMS のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [SAP ワークロードのための SAP ASE Azure Virtual Machines DBMS のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure VM 上の SAP MaxDB、liveCache、Content Server のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Azure 上での SAP HANA データベースについては、Azure Virtual Machines 上の SAP HANA に関するセクションを確認してください。

Azure 上での SAP ワークロードの高可用性について、入門者向けドキュメントは次のとおりです。

- [SAP NetWeaver のための Azure Virtual Machines 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

エントリ ドキュメントでは、アーキテクチャとシナリオに関する他のさまざまなドキュメントが参照されています。 シナリオに関する後続のドキュメントでは、高可用性のさまざまな方法のデプロイと構成を説明する詳細な技術ドキュメントへのリンクが提供されています。 SAP NetWeaver ワークロードに対する高可用性の確立と構成に関する各種ドキュメントでは、Linux オペレーティング システムも Windows オペレーティング システムも網羅されています。


Azure Active Directory (AAD) と SAP Services の統合、さらにシングル サインオンに関するドキュメントを以下に一覧表示します。

- [チュートリアル:Azure Active Directory と SAP Cloud for Customer の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [チュートリアル:Azure Active Directory と SAP Cloud Platform Identity Authentication の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [チュートリアル:Azure Active Directory と SAP Cloud Platform の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [チュートリアル:Azure Active Directory と SAP NetWeaver の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [チュートリアル:Azure Active Directory と SAP Business ByDesign の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [チュートリアル:Azure Active Directory と SAP HANA の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 環境 – Azure AD を使用した Fiori Launchpad SAML シングル サインオン](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Azure Services と SAP コンポーネントの統合に関するドキュメントの一覧は次のとおりです。

- [Power BI Desktop での SAP HANA の使用](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery と SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Power BI Desktop での SAP BW コネクタの使用](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory による SAP HANA と Business Warehouse データの統合](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




