---
title: SQL Server Always On 可用性グループの概要
description: この記事では、Azure Virtual Machines での SQL Server Always On 可用性グループについて説明します。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: bc0d5b0cdf616904ee7ed61fdc681ba60bff8f75
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87480290"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Azure Virtual Machines での SQL Server Always On 可用性グループの概要。

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure Virtual Machines での SQL Server 可用性グループについて説明します。 

Azure Virtual Machines での Always On 可用性グループは、オンプレミスの Always On 可用性グループに似ています。 詳細については、「 [AlwaysOn 可用性グループ (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)」をご覧ください。 

次の図は、Azure Virtual Machines での完全な SQL Server 可用性グループの一部を示したものです。

![可用性グループ](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Azure Virtual Machines での可用性グループの重要な違いは、仮想マシン (VM) には[ロード バランサー](../../../load-balancer/load-balancer-overview.md)が必要なことです。 ロード バランサーは、可用性グループ リスナーの IP アドレスを保持しています。 複数の可用性グループがある場合は、グループごとにリスナーが必要です。 1 つのロード バランサーで複数のリスナーをサポートできます。

さらに、Azure IaaS VM ゲスト フェールオーバー クラスターでは、サーバー (クラスター ノード) ごとに 1 つの NIC、および 1 つのサブネットを使用することをお勧めします。 Azure ネットワークは物理的な冗長性を備えているので、Azure IaaS VM ゲスト クラスターで NIC とサブネットを追加する必要はありません。 クラスター検証レポートでは、1 つのネットワークでしかノードに到達できないという警告が出ますが、Azure IaaS VM ゲスト フェールオーバー クラスターではこの警告を無視しても安全です。 

冗長性と高可用性を向上させるには、SQL Server VM を同じ[可用性セット](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets)に配置するか、異なる[可用性ゾーン](/azure/availability-zones/az-overview)に配置する必要があります。 

|  | Windows Server のバージョン | SQL Server のバージョン | SQL Server のエディション | WSFC クォーラムの構成 | マルチリージョンの DR | マルチサブネットのサポート | 既存の AD のサポート | マルチゾーン同一リージョンの DR | Dist-AG サポート (AD ドメインなし) | Dist-AG サポート (クラスターなし) |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| **[SQL VM CLI](availability-group-az-cli-configure.md)** | 2016 | 2017 </br>2016   | Ent | クラウド監視 | いいえ | はい | はい | はい | いいえ | いいえ |
| **[クイックスタート テンプレート](availability-group-quickstart-template-configure.md)** | 2016 | 2017</br>2016  | Ent | クラウド監視 | いいえ | はい | はい | はい | いいえ | いいえ |
| **[マニュアル](availability-group-manually-configure-prerequisites-tutorial.md)** | All | All | All | All | はい | はい | はい | はい | はい | はい |

**SQL Server AlwaysOn クラスター (プレビュー)** テンプレートが Azure Marketplace から削除され、入手できなくなりました。 

Azure Virtual Machines に SQL Server 可用性グループを作成する準備ができたら、次のチュートリアルをご覧ください。

## <a name="manually-with-azure-cli"></a>Azure CLI を使用して手動で行う

可用性グループの構成とデプロイには、Azure CLI を使用することをお勧めします。これは、最も簡単で最も高速にデプロイできるからです。 Azure CLI を使用すると、Windows フェールオーバー クラスターの作成、クラスターへの SQL Server VM の結合、およびリスナーと内部ロード バランサーの作成のすべてを 30 分以内に達成することができます。 このオプションでは、可用性グループの手動作成がまだ必要ですが、その他の必要な構成手順はすべて自動化されています。 

詳細については、「[Use Azure SQL VM CLI to configure Always On availability group for SQL Server on an Azure VM (Azure SQL VM CLI を使用して Azure VM 上で SQL Server のための Always On 可用性グループを構成する)](availability-group-az-cli-configure.md)」を参照してください。 

## <a name="automatically-with-azure-quickstart-templates"></a>Azure クイック スタート テンプレートを使用して自動的に行う

Azure クイック スタート テンプレートは、SQL VM リソース プロバイダーを使用して Windows フェールオーバー クラスターをデプロイし、SQL Server VM をそれに結合し、リスナーを作成し、内部ロード バランサーを構成します。 このオプションを使用する場合でも、可用性グループと内部ロード バランサー (ILB) を手動で作成する必要があります。 ただし、ILB の構成など、その他の必要なすべての構成手順は自動化および簡略化されます。 

詳細については、「[Use Azure Quickstart Template to configure Always On availability group for SQL Server on an Azure VM (Azure クイック スタート テンプレートを使用して Azure VM 上で SQL Server のための Always On 可用性グループを構成する)](availability-group-quickstart-template-configure.md)」を参照してください。


## <a name="automatically-with-an-azure-portal-template"></a>Azure portal テンプレートを使用して自動的に行う

[Azure VM での AlwaysOn 可用性グループの自動構成 - Resource Manager](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-the-azure-portal"></a>Azure portal で手動で行う

テンプレートを使わずに、自分で仮想マシンを作成することもできます。 最初に、前提条件を満たした後、可用性グループを作成します。 次のトピックを参照してください。 

- [Azure Virtual Machines で Always On 可用性グループを作成するための前提条件を満たす](availability-group-manually-configure-prerequisites-tutorial.md)

- [Always On 可用性グループを作成して可用性と障害復旧を向上させる](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>次のステップ

[異なるリージョンの Azure Virtual Machines に SQL Server AlwaysOn 可用性グループを構成する](availability-group-manually-configure-multiple-regions.md)
