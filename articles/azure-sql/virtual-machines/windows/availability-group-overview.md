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
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 8bbd56499c9b62248662fc5e8df0d5b3e1b672d4
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504169"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Azure VM 上の SQL Server の Always On 可用性グループ
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure 仮想マシン (VM) 上の SQL Server の Always On 可用性グループについて説明します。 

## <a name="overview"></a>概要

Azure 仮想マシン上の Always On 可用性グループは、[オンプレミスの Always On 可用性グループ](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)に似ています。 ただし、仮想マシンは Azure でホストされているため、VM の冗長性や Azure ネットワーク上でのトラフィックのルーティングなど、追加の考慮事項もいくつかあります。 

次の図は、Azure VM 上の SQL Server の可用性グループを示しています。

![可用性グループ](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>VM の冗長性 

冗長性と高可用性を向上させるには、SQL Server VM を同じ[可用性セット](../../../virtual-machines/availability-set-overview.md)に配置するか、異なる[可用性ゾーン](../../../availability-zones/az-overview.md)に配置する必要があります。

一連の VM を同じ可用性セットに配置すると、機器の障害が原因で発生するデータセンター内での停止から保護され (可用性セット内の VM はリソースを共有しません)、更新からも保護されます (可用性セット内の VM は同時に更新されることはありません)。 可用性ゾーンは、データセンター全体の障害から保護します。各ゾーンは、リージョン内の一連のデータセンターを表します。  リソースがさまざまな可用性ゾーンに配置されるようにすることで、データセンターレベルの停止によってすべての VM がオフラインになることはなくなります。

Azure VM を作成するときは、可用性セットと可用性ゾーンのどちらを構成するかを選択する必要があります。  Azure VM が両方に参加することはできません。


## <a name="connectivity"></a>接続状況 

従来のオンプレミスのデプロイでは、クライアントは仮想ネットワーク名 (VNN) を使用して可用性グループ リスナーに接続し、そのリスナーは可用性グループ内の適切な SQL Server レプリカにトラフィックをルーティングします。 ただし、Azure ネットワーク上でトラフィックをルーティングするには追加の要件があります。 

Azure VM 上の SQL Server を使用する場合、可用性グループ リスナーにトラフィックをルーティングするよう[ロード バランサー](availability-group-vnn-azure-load-balancer-configure.md)を構成します。また、SQL Server 2019 CU8 以降を使用している場合は、従来の VNN 可用性グループ リスナーに代わる[分散ネットワーク名 (DNN) リスナー](availability-group-distributed-network-name-dnn-listener-configure.md)を構成することもできます。 

クラスター接続オプションの詳細については、[HADR 接続を Azure VM 上の SQL Server にルーティングする方法](hadr-cluster-best-practices.md#connectivity)に関する記事をご覧ください。 

### <a name="vnn-listener"></a>VNN リスナー 

クライアントから Azure ネットワーク上の従来の仮想ネットワーク名 (VNN) 可用性グループ リスナーにトラフィックをルーティングするには、[Azure ロード バランサー](../../../load-balancer/load-balancer-overview.md)を使用します。 

このロード バランサーは、VNN リスナーの IP アドレスを保持しています。 複数の可用性グループがある場合は、グループごとに VNN リスナーが必要です。 1 つのロード バランサーで複数のリスナーをサポートできます。

作業を開始するには、[ロード バランサーの構成](availability-group-vnn-azure-load-balancer-configure.md)に関する記事を参照してください。 

### <a name="dnn-listener"></a>DNN リスナー

SQL Server 2019 CU8 では、分散ネットワーク名 (DNN) リスナーのサポートが導入されています。 DNN リスナーは、従来の可用性グループ リスナーに代わるものであり、Azure Load Balancer が Azure ネットワーク上でトラフィックをルーティングする必要性を排除します。 

DNN リスナーは、デプロイの簡略化、メンテナンスとコストの削減、障害発生時のフェールオーバー時間の短縮を実現するため、Azure で推奨される HADR 接続ソリューションとなります。 

DNN リスナーを既存の VNN リスナーの代わりに使用するか、または既存の VNN リスナーと組み合わせて使用します。後者の場合、可用性グループには 2 つの異なる接続ポイントとして、VNN リスナー名 (既定値以外の場合はポートも) を使用したものと、DNN リスナー名とポートを使用したものが存在します。 これは、ロード バランサーのフェールオーバーの待ち時間を回避したいものの、分散型可用性グループ、Service Broker、filestream など、VNN リスナーに依存する SQL Server 機能を引き続き利用したいユーザーに役立つ場合があります。 詳細については、[DNN リスナーと SQL Server 機能の相互運用性](availability-group-dnn-interoperability.md)に関する記事を参照してください。

作業を開始するには、[DNN リスナーの構成](availability-group-distributed-network-name-dnn-listener-configure.md)に関する記事を参照してください。


## <a name="deployment"></a>デプロイ 

Azure VM 上の SQL Server に可用性グループをデプロイするためのオプションは複数あり、その一部は他のものよりも自動化されています。 

次の表は、使用可能なオプションの比較を示しています。

| | Azure portal | Azure CLI / PowerShell | クイック スタート テンプレート | マニュアル |
|---------|---------|---------|---------|---------|
|**SQL Server のバージョン** |2016 以降 |2016 以降|2016 以降|2012 以降|
|**SQL Server のエディション** |Enterprise |Enterprise |Enterprise |Enterprise、Standard|
|**Windows Server のバージョン**| 2016 以降 | 2016 以降 | 2016 以降 | All|
|**クラスターを自動作成**|はい|はい | はい |いいえ|
|**可用性グループを自動作成** |はい |いいえ|いいえ|いいえ|
|**リスナーとロード バランサーを別々に作成** |いいえ|いいえ|いいえ|はい|
|**この方法を使用して DNN リスナーを作成可能**|いいえ|いいえ|いいえ|はい|
|**WSFC クォーラムの構成**|クラウド監視|クラウド監視|クラウド監視|All|
|**複数のリージョンを使用した DR** |いいえ|いいえ|いいえ|はい|
|**マルチサブネットのサポート** |はい|はい|はい|はい|
|**既存の AD のサポート**|はい|はい|はい|はい|
|**同一リージョン内の複数のゾーンを使用した DR**|はい|はい|はい|はい|
|**AD なしの分散型 AG**|いいえ|いいえ|いいえ|はい|
|**クラスターなしの分散型 AG** |いいえ|いいえ|いいえ|はい|

詳細については、[Azure portal](availability-group-azure-portal-configure.md)、[Azure CLI/PowerShell](./availability-group-az-commandline-configure.md)、[クイックスタート テンプレート](availability-group-quickstart-template-configure.md)、および[手動](availability-group-manually-configure-prerequisites-tutorial.md)に関するページを参照してください。

## <a name="considerations"></a>考慮事項 

Azure IaaS VM ゲスト フェールオーバー クラスターでは、サーバー (クラスター ノード) ごとに 1 つの NIC、および 1 つのサブネットを推奨しています。 Azure ネットワークは物理的な冗長性を備えているので、Azure IaaS VM ゲスト クラスターで NIC とサブネットを追加する必要はありません。 クラスター検証レポートでは、1 つのネットワークでしかノードに到達できないという警告が出ますが、Azure IaaS VM ゲスト フェールオーバー クラスターではこの警告を無視しても安全です。 

## <a name="next-steps"></a>次の手順

[HADR のベスト プラクティス](hadr-cluster-best-practices.md)を確認し、[Azure portal](availability-group-azure-portal-configure.md)、[Azure CLI または PowerShell](./availability-group-az-commandline-configure.md)、[クイックスタート テンプレート](availability-group-quickstart-template-configure.md)を使用するか、または[手動](availability-group-manually-configure-prerequisites-tutorial.md)で可用性グループのデプロイを開始します。

また、[クラスターレス可用性グループ](availability-group-clusterless-workgroup-configure.md)または可用性グループを[複数のリージョン](availability-group-manually-configure-multiple-regions.md)にデプロイすることもできます。
