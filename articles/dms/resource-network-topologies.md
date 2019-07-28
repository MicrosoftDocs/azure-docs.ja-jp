---
title: Azure Database Migration Service を使用して Azure SQL Database Managed Instance を移行するためのネットワーク トポロジ | Microsoft Docs
description: Azure Database Migration Service のソースとターゲットの構成について説明します。
services: database-migration
author: hjtoland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/07/2019
ms.openlocfilehash: 74613599903f7cde606295a1e2d9eaaa0924cf50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808420"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Azure Database Migration Service を使用して Azure SQL DB Managed Instance を移行するためのネットワーク トポロジ

この記事では、オンプレミスの SQL サーバーから Azure SQL Database Managed Instance への包括的な移行エクスペリエンスを提供するために Azure Database Migration Service で使用できる、さまざまなネットワーク トポロジについて説明します。

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>ハイブリッド ワークロード用に構成された Azure SQL Database Managed Instance 

Azure SQL Database マネージド インスタンスがオンプレミス ネットワークに接続されている場合は、このトポロジを使用します。 このアプローチでは、ネットワーク ルーティングが最も簡素化され、移行時に最大限のデータ スループットが得られます。

![ハイブリッド ワークロード用のネットワーク トポロジ](media/resource-network-topologies/hybrid-workloads.png)

**要件**

- このシナリオでは、Azure SQL Database マネージド インスタンスと Azure Database Migration Service インスタンスが同じ Azure VNet 内に作成されますが、サブネットはそれぞれ別のものが使用されます。  
- このシナリオで使用される VNet は、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用して、オンプレミス ネットワークに接続されます。

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>オンプレミス ネットワークから分離された Azure SQL Database マネージド インスタンス

使用する環境において、次の 1 つ以上のシナリオが当てはまる場合は、このネットワーク トポロジを使用します。

- Azure SQL Database マネージド インスタンスはオンプレミス接続から分離されているが、Azure Database Migration Service はオンプレミス ネットワークに接続されている。
- ロール ベースのアクセス制御 (RBAC) ポリシーが使用されていて、ユーザー アクセスは、Azure SQL Database マネージド インスタンスをホストしているのと同じサブスクリプションに制限する必要がある。
- Azure SQL Database マネージド インスタンス用に使用される VNet と Azure Database Migration Service 用に使用される VNet が、それぞれ異なるサブスクリプションに属している。

![オンプレミス ネットワークから分離されたマネージド インスタンス用のネットワーク トポロジ](media/resource-network-topologies/mi-isolated-workload.png)

**要件**

- このシナリオで Azure Database Migration Service に使用される VNet は、 https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用して、オンプレミス ネットワークにも接続される必要があります。
- Azure SQL Database Managed Instance と Azure Database Migration Service 用に使用される VNet 間に、[VNet ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を設定します。

## <a name="cloud-to-cloud-migrations-shared-vnet"></a>クラウド間の移行: 共有 VNet

ソース SQL Server が Azure VM でホストされていて、Azure SQL Database Managed Instance および Azure Database Migration Service と同じ VNet を共有している場合は、このトポロジを使用します。

![共有 VNet があるクラウド間移行のためのネットワーク トポロジ](media/resource-network-topologies/cloud-to-cloud.png)

**要件**

- その他の要件はありません。

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>クラウド間の移行: 分離 VNet

使用する環境において、次の 1 つ以上のシナリオが当てはまる場合は、このネットワーク トポロジを使用します。

- Azure SQL Database マネージド インスタンスが、分離 VNet でプロビジョニングされている。
- ロール ベースのアクセス制御 (RBAC) ポリシーが使用されていて、ユーザー アクセスは、Azure SQL Database マネージド インスタンスをホストしているのと同じサブスクリプションに制限する必要がある。
- Azure SQL Database Managed Instance 用に使用される VNet と Azure Database Migration Service 用に使用される VNet が、それぞれ異なるサブスクリプションに属している。

![分離 VNet があるクラウド間移行のためのネットワーク トポロジ](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**要件**

- Azure SQL Database Managed Instance と Azure Database Migration Service 用に使用される VNet 間に、[VNet ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を設定します。

## <a name="inbound-security-rules"></a>受信セキュリティ規則

| **名前**   | **ポート** | **プロトコル** | **ソース** | **送信先** | **アクション** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | 任意      | 任意          | DMS サブネット | 任意             | ALLOW      |

## <a name="outbound-security-rules"></a>送信セキュリティ規則

| **名前**                  | **ポート**                                              | **プロトコル** | **ソース** | **送信先**           | **アクション** | **ルールの理由**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| management                | 443,9354                                              | TCP          | 任意        | 任意                       | ALLOW      | Service Bus と Azure Blob Storage を経由する管理プレーン通信。 <br/>(Microsoft ピアリングが有効になっている場合、この規則は必要ないことがあります。)                                                             |
| 診断               | 12000                                                 | TCP          | 任意        | 任意                       | ALLOW      | DMS はこの規則を使用して、トラブルシューティングのために診断情報を収集します。                                                                                                                      |
| SQL ソース サーバー         | 1433 (または、SQL Server がリッスンしている TCP IP ポート) | TCP          | 任意        | オンプレミスのアドレス空間 | ALLOW      | DMS からの SQL Server ソース接続 <br/>(サイト間接続がある場合、この規則は必要ないことがあります。)                                                                                       |
| SQL Server 名前付きインスタンス | 1434                                                  | UDP          | 任意        | オンプレミスのアドレス空間 | ALLOW      | DMS からの SQL Server 名前付きインスタンス ソース接続 <br/>(サイト間接続がある場合、この規則は必要ないことがあります。)                                                                        |
| SMB 共有                 | 445                                                   | TCP          | 任意        | オンプレミスのアドレス空間 | ALLOW      | Azure VM 上の Azure SQL Database MI と SQL Server への移行用にデータベース バックアップ ファイルを格納するための DMS に対する SMB ネットワーク共有 <br/>(サイト間接続がある場合、この規則は必要ないことがあります)。 |
| DMS_subnet                | 任意                                                   | 任意          | 任意        | DMS_Subnet                | ALLOW      |                                                                                                                                                                                                  |

## <a name="see-also"></a>関連項目

- [SQL Server を Azure SQL Database Managed Instance に移行する](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Azure Database Migration Service を使用するための前提条件の概要](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Azure Portal を使用した仮想ネットワークの作成](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>次の手順

- Azure Database Migration Service の概要については、「[Azure Database Migration Service とは](dms-overview.md)」の記事を参照してください。
- Azure Database Migration Service のリージョン別の提供状況に関する最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)」のページを参照してください。
