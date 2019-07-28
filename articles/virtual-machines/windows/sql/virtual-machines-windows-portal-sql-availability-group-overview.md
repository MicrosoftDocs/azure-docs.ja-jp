---
title: SQL Server 可用性グループ - Azure Virtual Machines - 概要 | Microsoft Docs
description: この記事では、Azure Virtual Machines での SQL Server 可用性グループについて説明します。
services: virtual-machines
documentationCenter: na
author: mikeraymsft
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.openlocfilehash: b9977965dc076ec36aa90680a1732b6640b1e41a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60325807"
---
# <a name="introducing-sql-server-always-on-availability-groups-on-azure-virtual-machines"></a>Azure Virtual Machines での SQL Server Always On 可用性グループの概要 #

この記事では、Azure Virtual Machines での SQL Server 可用性グループについて説明します。 

Azure Virtual Machines での Always On 可用性グループは、オンプレミスの Always On 可用性グループに似ています。 詳細については、「 [AlwaysOn 可用性グループ (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)」をご覧ください。 

次の図は、Azure Virtual Machines での完全な SQL Server 可用性グループの一部を示したものです。

![可用性グループ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

Azure Virtual Machines での可用性グループの重要な違いは、Azure Virtual Machines には[ロード バランサー](../../../load-balancer/load-balancer-overview.md)が必要なことです。 ロード バランサーは、可用性グループ リスナーの IP アドレスを保持しています。 複数の可用性グループがある場合は、グループごとにリスナーが必要です。 1 つのロード バランサーで複数のリスナーをサポートできます。

さらに、Azure IaaS VM ゲスト フェールオーバー クラスターでは、サーバー (クラスター ノード) ごとに 1 つの NIC、および 1 つのサブネットを使用することをお勧めします。 Azure ネットワークは物理的な冗長性を備えているので、Azure IaaS VM ゲスト クラスターで NIC とサブネットを追加する必要はありません。 クラスター検証レポートでは、1 つのネットワークでしかノードに到達できないという警告が出ますが、Azure IaaS VM ゲスト フェールオーバー クラスターではこの警告を無視しても安全です。 

|  | Windows Server のバージョン | SQL Server のバージョン | SQL Server のエディション | WSFC クォーラムの構成 | マルチリージョンの DR | マルチサブネットのサポート | 既存の AD のサポート | マルチゾーン同一リージョンの DR | Dist-AG サポート (AD ドメインなし) | Dist-AG サポート (クラスターなし) |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) | 2016 | 2017 </br>2016   | Ent | クラウド監視 | いいえ | 可能 | はい | はい | いいえ | いいえ |
| [クイック スタート テンプレート](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 2016 | 2017</br>2016  | Ent | クラウド監視 | いいえ | 可能 | はい | はい | いいえ | いいえ |
| [Portal テンプレート](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) | 2016 </br>2012 R2 | 2016</br>2014 | Ent | ファイル共有 | いいえ | いいえ | いいえ | いいえ | いいえ | いいえ |
| [手動](virtual-machines-windows-portal-sql-availability-group-prereq.md) | All | All | All | All | はい | はい | はい | はい | はい | はい |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Azure Virtual Machines に SQL Server 可用性グループを作成する準備ができたら、次のチュートリアルをご覧ください。

## <a name="manually-with-azure-cli"></a>Azure CLI を使用して手動で行う
Azure CLI を使用した可用性グループの構成とデプロイは、単純さとデプロイ速度の観点から最良の方法のため、推奨オプションです。 Azure CLI を使用すると、Windows フェールオーバー クラスターの作成、クラスターへの SQL Server VM の結合、およびリスナーと内部ロード バランサーの作成のすべてを 30 分以内に達成することができます。 このオプションでは、可用性グループの手動作成がまだ必要ですが、その他の必要な構成手順はすべて自動化されています。 

詳細については、「[Use Azure SQL VM CLI to configure Always On availability group for SQL Server on an Azure VM (Azure SQL VM CLI を使用して Azure VM 上で SQL Server のための Always On 可用性グループを構成する)](virtual-machines-windows-sql-availability-group-cli.md)」を参照してください。 

## <a name="automatically-with-azure-quickstart-templates"></a>Azure クイック スタート テンプレートを使用して自動的に行う
Azure クイック スタート テンプレートは、SQL VM リソース プロバイダーを使用して Windows フェールオーバー クラスターをデプロイし、SQL Server VM をそれに結合し、リスナーを作成し、内部ロード バランサーを構成します。 このオプションでは、可用性グループおよび内部ロード バランサー (ILB) の手動作成がまだ必要ですが、(ILB の構成を含め) 他の必要な構成手順はすべて自動化および簡素化されています。 

詳細については、「[Use Azure Quickstart Template to configure Always On availability group for SQL Server on an Azure VM (Azure クイック スタート テンプレートを使用して Azure VM 上で SQL Server のための Always On 可用性グループを構成する)](virtual-machines-windows-sql-availability-group-quickstart-template.md)」を参照してください。


## <a name="automatically-with-an-azure-portal-template"></a>Azure portal テンプレートを使用して自動的に行う

[Azure VM での AlwaysOn 可用性グループの自動構成 - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)


## <a name="manually-in-azure-portal"></a>Azure portal で自動的に行う

テンプレートを使わずに、自分で仮想マシンを作成することもできます。 最初に、前提条件を満たした後、可用性グループを作成します。 以下のトピックをご覧ください。 

- [Azure Virtual Machines で Always On 可用性グループを作成するための前提条件を満たす](virtual-machines-windows-portal-sql-availability-group-prereq.md)

- [Always On 可用性グループを作成して可用性と障害復旧を向上させる](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="next-steps"></a>次の手順

[異なるリージョンの Azure Virtual Machines に SQL Server AlwaysOn 可用性グループを構成する](virtual-machines-windows-portal-sql-availability-group-dr.md)
