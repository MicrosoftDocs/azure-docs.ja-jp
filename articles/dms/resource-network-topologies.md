---
title: SQL Managed Instance の移行のためのネットワーク トポロジ
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用した Azure SQL Managed Instance の移行のための、ソースおよびターゲット構成について学習します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 0799e8c76bc5d3969943d766aa83de40659a236a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093337"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Azure Database Migration Service を使用して Azure SQL Managed Instance を移行するためのネットワーク トポロジ

この記事では、SQL サーバーから Azure SQL Managed Instance への包括的な移行エクスペリエンスを提供するために Azure Database Migration Service で使用できる、さまざまなネットワーク トポロジについて説明します。

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>ハイブリッド ワークロード用に構成された Azure SQL Managed Instance 

Azure SQL Managed Instance がオンプレミス ネットワークに接続されている場合は、このトポロジを使用します。 このアプローチでは、ネットワーク ルーティングが最も簡素化され、移行時に最大限のデータ スループットが得られます。

![ハイブリッド ワークロード用のネットワーク トポロジ](media/resource-network-topologies/hybrid-workloads.png)

**必要条件**

- このシナリオでは、SQL Managed Instance と Azure Database Migration Service インスタンスが同じ Microsoft Azure Virtual Network 内に作成されますが、サブネットはそれぞれ別のものが使用されます。  
- このシナリオで使用される仮想ネットワークは、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用して、オンプレミス ネットワークに接続されます。

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>オンプレミス ネットワークから分離された SQL Managed Instance

使用する環境において、次の 1 つ以上のシナリオが当てはまる場合は、このネットワーク トポロジを使用します。

- SQL Managed Instance はオンプレミス接続から分離されているが、Azure Database Migration Service インスタンスはオンプレミス ネットワークに接続されている。
- Azure ロール ベースのアクセス制御 (Azure RBAC) ポリシーが使用されていて、ユーザー アクセスは、SQL Managed Instance をホストしているのと同じサブスクリプションに制限する必要がある。
- SQL Managed Instance 用と、Azure Database Migration Service 用に使用される仮想ネットワークが、それぞれ異なるサブスクリプションに属している。

![オンプレミス ネットワークから分離されたマネージド インスタンス用のネットワーク トポロジ](media/resource-network-topologies/mi-isolated-workload.png)

**必要条件**

- このシナリオで Azure Database Migration Service に使用される仮想ネットワークは、 https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用して、オンプレミス ネットワークにも接続される必要があります。
- SQL Managed Instance 用と、Azure Database Migration Service 用に使用される仮想ネットワーク間に、[VNet ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)を設定します。

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>クラウド間の移行:共有仮想ネットワーク

ソース SQL Server が Azure VM でホストされていて、SQL Managed Instance および Azure Database Migration Service と同じ仮想ネットワークを共有している場合は、このトポロジを使用します。

![共有 VNet があるクラウド間移行のためのネットワーク トポロジ](media/resource-network-topologies/cloud-to-cloud.png)

**必要条件**

- その他の要件はありません。

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>クラウド間の移行:分離された仮想ネットワーク

使用する環境において、次の 1 つ以上のシナリオが当てはまる場合は、このネットワーク トポロジを使用します。

- SQL Managed Instance が、分離された仮想ネットワークでプロビジョニングされている。
- Azure ロール ベースのアクセス制御 (Azure RBAC) ポリシーが使用されていて、ユーザー アクセスは、SQL Managed Instance をホストしているのと同じサブスクリプションに制限する必要がある。
- SQL Managed Instance 用と、Azure Database Migration Service 用に使用される仮想ネットワークが、それぞれ異なるサブスクリプションに属している。

![分離 VNet があるクラウド間移行のためのネットワーク トポロジ](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**必要条件**

- SQL Managed Instance 用と、Azure Database Migration Service 用に使用される仮想ネットワーク間に、[VNet ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)を設定します。

## <a name="inbound-security-rules"></a>受信セキュリティ規則

| **名前**   | **ポート** | **プロトコル** | **ソース** | **送信先** | **アクション** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Any      | Any          | DMS サブネット | Any             | Allow      |

## <a name="outbound-security-rules"></a>送信セキュリティ規則

| **名前**                  | **ポート**                                              | **プロトコル** | **ソース** | **送信先**           | **アクション** | **ルールの理由**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceBus                | 443、ServiceTag: ServiceBus                           | TCP          | Any        | Any                       | Allow      | Service Bus を経由する管理プレーン通信。 <br/>(Microsoft ピアリングが有効になっている場合、この規則は必要ないことがあります。)                                                             |
| ストレージ                   | 443、ServiceTag: Storage                              | TCP          | Any        | Any                       | Allow      | Azure Blob Storage を使用した管理プレーン。 <br/>(Microsoft ピアリングが有効になっている場合、この規則は必要ないことがあります。)                                                             |
| 診断               | 443、ServiceTag: AzureMonitor                         | TCP          | Any        | Any                       | Allow      | DMS はこの規則を使用して、トラブルシューティングのために診断情報を収集します。 <br/>(Microsoft ピアリングが有効になっている場合、この規則は必要ないことがあります。)                                                  |
| SQL ソース サーバー         | 1433 (または、SQL Server がリッスンしている TCP IP ポート) | TCP          | Any        | オンプレミスのアドレス空間 | Allow      | DMS からの SQL Server ソース接続 <br/>(サイト間接続がある場合、この規則は必要ないことがあります。)                                                                                       |
| SQL Server 名前付きインスタンス | 1434                                                  | UDP          | Any        | オンプレミスのアドレス空間 | Allow      | DMS からの SQL Server 名前付きインスタンス ソース接続 <br/>(サイト間接続がある場合、この規則は必要ないことがあります。)                                                                        |
| SMB 共有                 | 445 (シナリオで必要な場合)                             | TCP          | Any        | オンプレミスのアドレス空間 | Allow      | Azure VM 上の Azure SQL Database MI と SQL Server への移行用にデータベース バックアップ ファイルを格納するための DMS に対する SMB ネットワーク共有 <br/>(サイト間接続がある場合、この規則は必要ないことがあります)。 |
| DMS_subnet                | Any                                                   | Any          | Any        | DMS_Subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>関連項目

- [SQL Server から SQL Managed Instance に移行する](./tutorial-sql-server-to-managed-instance.md)
- [Azure Database Migration Service を使用するための前提条件の概要](./pre-reqs.md)
- [Azure Portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>次のステップ

- Azure Database Migration Service の概要については、「[Azure Database Migration Service とは](dms-overview.md)」の記事を参照してください。
- Azure Database Migration Service のリージョン別の提供状況に関する最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)」のページを参照してください。