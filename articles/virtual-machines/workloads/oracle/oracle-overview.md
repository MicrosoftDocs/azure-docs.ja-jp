---
title: Microsoft Azure での Oracle ソリューション | Microsoft Docs
description: Oracle のアプリケーションおよびソリューションを Microsoft Azure にデプロイするためのオプション (全面的に Azure インフラストラクチャ上で実行する、クラウド間接続と Oracle Cloud Infrastructure (OCI) を使用するなど) について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: e9e37c54668ec0343cbfd45e51e90216955b46c4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100019"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure での Oracle アプリケーションおよびソリューションの概要

この記事では、Azure インフラストラクチャを使用して Oracle ソリューションを実行するための機能を紹介します。 Azure Marketplace で入手可能な [Oracle VM イメージ](oracle-vm-solutions.md)や、[Azure と Oracle Cloud Infrastructure (OCI) を相互接続する](oracle-oci-overview.md)プレビュー機能の詳しい紹介も参照してください。

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure インフラストラクチャ上の Oracle データベース

Azure Marketplace で入手可能な Linux イメージを使用して、Azure インフラストラクチャ上で Oracle データベースを実行します。

* Oracle Database 12.1、12.2、および 18.3 Enterprise Edition 

* Oracle Database 12.1、12.2、および 18.3 Standard Edition 

Azure で一から作成したカスタム イメージに基づくソリューションにするか、オンプレミス環境からカスタム イメージをアップロードするかを選択することもできます。

必要に応じて、複数の接続ディスクを構成し、Oracle Automated Storage Management (ASM) をインストールすることでデータベースのパフォーマンスを向上させます。

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux および WebLogic Server 上のアプリケーション

サポートされている Oracle オペレーティング システム上で、Azure 内のエンタープライズ アプリケーションを実行します。 次のイメージは Azure Marketplace で入手できます。

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8、6.9、6.10、7.3、7.4、7.5、7.6

## <a name="high-availability-and-disaster-recovery-options"></a>高可用性とディザスター リカバリーのオプション

* Azure インフラストラクチャ上で [Availability Zones](../../../availability-zones/az-overview.md) と組み合わせて Oracle Data Guard、Active Data Guard、または GoldenGate を構成し、高可用性を実現します。

* [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) を使用して、Azure 内の Oracle Linux VM と、オンプレミスまたは物理サーバーのディザスター リカバリーを調整および管理します。 

* [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/) を使用して、Oracle Real Application Clusters (RAC) を Azure で有効にします。

## <a name="integration-of-azure-with-oci-preview"></a>Azure と OCI の統合 (プレビュー)

Oracle Cloud Infrastructure (OCI) のバックエンド データベースに接続された Azure インフラストラクチャで Oracle アプリケーションを実行します。 このソリューションでは、次の機能を使用します。 

* **クラウド間ネットワーキング** - Azure ExpressRoute と Oracle FastConnect の間で利用可能な直接相互接続を使用して、高帯域幅、プライベート、低待機時間の接続をアプリケーションとデータベース レイヤーの間に確立します。
* **統合型 ID** - Azure AD と Oracle IDCS 間のフェデレーション ID をセットアップして、ソリューション用の単一の ID ソースを作成します。 OCI と Azure にまたがってリソースを管理するために、シングル サインオンを有効にします。

### <a name="deploy-oracle-applications-on-azure"></a>Oracle アプリケーションを Azure 上に展開する

Terraform テンプレートを使用して Azure インフラストラクチャをセットアップし、クラウド間構成での実行が検証およびサポートされている Oracle アプリケーションをインストールします。

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail アプリケーション
* Oracle Hyperion Financial Management

また、OCI や他の Azure サービスと接続するカスタム アプリケーションを Azure にデプロイします。

### <a name="set-up-oracle-databases-in-oci"></a>OCI で Oracle データベースをセットアップする

Oracle Database Cloud Services (Autonomous Database、RAC、Exadata、DBaaS、Single Node) を、Azure で実行されている Oracle アプリケーションと組み合わせて使用します。 [OCI データベース オプション](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)の詳細を見る。 
 

## <a name="licensing"></a>ライセンス

Azure での Oracle アプリケーションのデプロイは、"ライセンス持ち込み" モデルに基づいています。 つまり、Oracle ソフトウェアを使用するライセンスが適切に供与されていて、Oracle と現在サポート契約を結んでいることを前提としています。 Oracle では、オンプレミスから Azure へのライセンス モビリティを保証しています。 Oracle-Azure [FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) を参照してください。

## <a name="next-steps"></a>次の手順

* Azure インフラストラクチャへの [Oracle VM イメージ](oracle-vm-solutions.md)のデプロイについて詳しく学びます。

* [Azure と OCI を相互接続する](oracle-oci-overview.md)方法について詳しく学びます。