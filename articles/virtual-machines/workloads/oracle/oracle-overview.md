---
title: Microsoft Azure での Oracle ソリューション | Microsoft Docs
description: Oracle のアプリケーションおよびソリューションを Microsoft Azure にデプロイするためのオプション (全面的に Azure インフラストラクチャ上で実行する、クラウド間接続と Oracle Cloud Infrastructure (OCI) を使用するなど) について学びます。
documentationcenter: ''
author: dbakevlar
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: kegorman
ms.openlocfilehash: 971f7c919595f915451faf9266ee3bb18b35087e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677174"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure での Oracle アプリケーションおよびソリューションの概要

この記事では、Azure インフラストラクチャを使用して Oracle ソリューションを実行するための機能を紹介します。 Azure Marketplace で入手可能な [WebLogic Server Azure アプリケーション](oracle-weblogic.md)、[Oracle VM イメージ](oracle-vm-solutions.md)や、[Azure と Oracle Cloud Infrastructure (OCI) を相互接続する](oracle-oci-overview.md)機能の詳しい紹介も参照してください。

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure インフラストラクチャ上の Oracle データベース

Azure Marketplace で入手可能な Oracle Linux イメージの Oracle Database を使用して、Azure インフラストラクチャで Oracle データベースを実行します。

* Oracle Database 12.1、12.2、および 18.3 Enterprise Edition 

* Oracle Database 12.1、12.2、および 18.3 Standard Edition

* Oracle データベース 19.3

また、Azure で利用可能な Oracle Linux 以外のイメージに Oracle Database を設定したり、Azure で一から作成したカスタム イメージに基づいてソリューションを作成したり、オンプレミス環境からカスタム イメージをアップロードしたりすることも選択できます。

必要に応じて、複数の接続ディスクを構成し、Oracle Automated Storage Management (ASM) をインストールすることでデータベースのパフォーマンスを向上させます。

## <a name="weblogic-server-with-azure-service-integrations"></a>WebLogic Server と Azure サービス統合

クラウドへの移行を促進するために、さまざまな WebLogic Server Azure アプリケーションから選択します。  データベース、Azure App Gateway、Azure Active Directory など、いくつかの事前構成済みの Azure サービス統合を利用できます。

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux および WebLogic Server 上のアプリケーション

サポートされている Oracle Linux イメージで、Azure のエンタープライズ アプリケーションを実行します。 次の仮想マシン イメージは Azure Marketplace で入手できます。

* Oracle WebLogic Server 12.1.2

* Unbreakable Enterprise Kernel (UEK) 6.8、6.9、6.10、7.3 から 7.7、8.0、8.1.を搭載した Oracle Linux 

## <a name="high-availability-and-disaster-recovery-options"></a>高可用性とディザスター リカバリーのオプション

* Azure インフラストラクチャ上で、[Availability Zones](../../../availability-zones/az-overview.md) と組み合わせて [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956)、[FSFO を持つ Active Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)、[シャーディング](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)、または [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) を構成し、リージョン内の高可用性を実現します。 また、可用性とディザスター リカバリーを強化するために、これらの構成を複数の Azure リージョンにわたって設定することもできます。

* [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) を使用して、Azure 内の Oracle Linux VM と、物理サーバーのディザスター リカバリーを調整および管理します。 

* [Azure VMware Solution](../../../vmware-cloudsimple/oracle-real-application-clusters.md) または [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/) を使用して、Oracle Real Application Clusters (RAC) を Azure で有効にします。

## <a name="backup-oracle-workloads"></a>Oracle ワークロードのバックアップ

* [Azure Backup](../../../backup/backup-overview.md) を使用して Oracle VM をバックアップします。

* Oracle RMAN を使用して Oracle Database をバックアップし、必要に応じて、[Azure Blob Fuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) を使用して、[冗長性の高い Azure Blob Storage アカウント](../../../storage/common/storage-redundancy.md)をマウントし、回復性を高めるために RMAN バックアップをそれに書き込みます。

## <a name="integration-of-azure-with-oci"></a>Azure と OCI の統合

Oracle Cloud Infrastructure (OCI) のバックエンド データベースに接続された Azure インフラストラクチャで Oracle アプリケーションを実行します。 このソリューションでは、次の機能を使用します。 

* **クラウド間ネットワーキング** - Azure ExpressRoute と Oracle FastConnect の間で利用可能な直接相互接続を使用して、高帯域幅、プライベート、低待機時間の接続をアプリケーションとデータベース レイヤーの間に確立します。
* **統合型 ID** - Azure AD と Oracle IDCS 間のフェデレーション ID をセットアップして、ソリューション用の単一の ID ソースを作成します。 OCI と Azure にまたがってリソースを管理するために、シングル サインオンを有効にします。

### <a name="deploy-oracle-applications-on-azure"></a>Oracle アプリケーションを Azure 上にデプロイする

Terraform テンプレートを使用して Azure インフラストラクチャを設定し、Oracle アプリケーションをインストールします。 

Oracle は、Azure/Oracle Cloud 相互接続ソリューションを介して Oracle データベースに接続するときに、これらのアプリケーションを Azure で実行することを認定しています。

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail アプリケーション
* Oracle Hyperion Financial Management

また、OCI や他の Azure サービスと接続するカスタム アプリケーションを Azure にデプロイします。

### <a name="set-up-oracle-databases-in-oci"></a>OCI で Oracle データベースをセットアップする

Oracle Database Cloud Services (Autonomous Database、RAC、Exadata、DBaaS、Single Node) を、Azure で実行されている Oracle ソフトウェアと組み合わせて使用します。 [OCI データベース オプション](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)の詳細を見る。 
 

## <a name="licensing"></a>ライセンス

Azure での Oracle アプリケーションのデプロイは、「ライセンス持ち込み」モデルに基づいています。 つまり、Oracle ソフトウェアを使用するライセンスが適切に供与されていて、Oracle と現在サポート契約を結んでいることを前提としています。 Oracle では、オンプレミスから Azure へのライセンス モビリティを保証しています。 Oracle-Azure [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) を参照してください。

## <a name="next-steps"></a>次のステップ

* [WebLogic Server Azure アプリケーション](oracle-weblogic.md)と、それらがサポートする Azure サービス統合について詳しく学びます。

* Azure インフラストラクチャへの [Oracle VM イメージ](oracle-vm-solutions.md)のデプロイについて詳しく学びます。

* [Azure と OCI を相互接続する](oracle-oci-overview.md)方法について詳しく学びます。

* Ignite 2019 からの [Oracle on Azure の概要セッション](https://www.pluralsight.com/courses/microsoft-ignite-session-57) を確認してください。 
