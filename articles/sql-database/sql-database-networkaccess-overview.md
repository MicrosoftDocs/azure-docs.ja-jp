---
title: Azure SQL Database および Data Warehouse のネットワーク アクセスの制御 | Microsoft Docs
description: アクセスを管理し、単一データベースまたはプールされたデータベースを構成するための、Azure SQL Database および Data Warehouse のネットワーク アクセスの制御の概要。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 2d7cc217ff8ae45491c0f9d6b54ea8afea19cd2e
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981234"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL Database および Data Warehouse のネットワーク アクセスの制御

> [!NOTE]
> この記事は Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。

> [!IMPORTANT]
> この記事は、**Azure SQL Database Managed Instance** には "*適用されません*"。 ネットワーク構成の詳細については、[マネージド インスタンスへの接続](sql-database-managed-instance-connect-app.md)に関するページを参照してください。

[Azure portal から](sql-database-single-database-get-started.md)新しい Azure SQL Server を作成すると、結果は *yourservername.database.windows.net* という形式のパブリック エンドポイントになります。 設計上、パブリック エンドポイントへのアクセスはすべて拒否されます。 その後、次のネットワーク アクセスの制御を使用して、パブリック エンドポイントを介した SQL Database へのアクセスを選択的に許可できます
- Azure サービスを許可する: - オンに設定すると、Azure 境界内の他のリソース (たとえば、Azure Virtual Machine) が SQL Database にアクセスできます

- IP ファイアウォール規則: - この機能を使用すると、特定の IP アドレスからの (たとえば、オンプレミスのマシンからの) 接続を明示的に許可できます。

- 仮想ネットワーク ファイアウォール規則: - この機能を使用すると、Azure 境界内の特定の仮想ネットワークからのトラフィックを許可できます。

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure サービスを許可する 
[Azure portal で](sql-database-single-database-get-started.md)新しい Azure SQL Server を作成するときは、この設定をオフのままにします。

 ![新しいサーバーの作成のスクリーンショット][1]

この設定は、次のように、Azure SQL Server を作成した後にファイアウォール ウィンドウで変更することもできます。
  
 ![サーバーのファイアウォール管理のスクリーンショット][2]

**[オン]** に設定すると、Azure SQL Server により、Azure 境界内のすべてのリソース (自分のサブスクリプションの一部であることも、そうでないこともあります) からの通信が許可されます。

多くの場合、 **[オン]** 設定は、ほとんどのお客様が望むよりも許容範囲が広くなっています。この設定を **[オフ]** に設定し、より制限の厳しい IP ファイアウォール規則または仮想ネットワーク ファイアウォール規則に置き換えてもかまいません。 そうすると、次の機能に影響します。

### <a name="import-export-service"></a>Import Export Service

Azure SQL Database Import Export Service は、Azure の VM 上で実行されます。 これらの VM は VNet に存在しないため、データベースに接続するときに Azure IP を取得します。 **[Azure サービスにサーバーへのアクセスを許可する]** を削除すると、これらの VM はデータベースにアクセスできなくなります。
DACFx API を使用して BACPAC インポートまたはエクスポートをコードで直接実行することにより、問題を回避できます。

### <a name="sql-database-query-editor"></a>SQL Database クエリ エディター

Azure SQL Database クエリ エディターは、Azure の VM にデプロイされます。 これらの VM は VNet に存在しません。 そのため、VM はデータベースに接続するときに Azure IP を取得します。 **[Azure サービスにサーバーへのアクセスを許可する]** を削除すると、これらの VM はデータベースにアクセスできなくなります。

### <a name="table-auditing"></a>テーブル監査

現在、お使いの SQL Database で監査を有効にする方法は 2 つあります。 Azure SQL Server でサービス エンドポイントを有効にすると、テーブル監査は失敗します。 この問題を軽減するには、BLOB 監査に移行します。

### <a name="impact-on-data-sync"></a>データ同期への影響

Azure SQL Database には、Azure IP を使用してデータベースに接続するデータ同期機能があります。 サービス エンドポイントを使用する場合は、お使いの SQL Database サーバーへの **[Azure サービスにサーバーへのアクセスを許可する]** アクセスをオフにし、データ同期機能を無効にします。

## <a name="ip-firewall-rules"></a>IP ファイアウォール規則
IP ベースのファイアウォールは、Azure SQL Server の機能であり、明示的にクライアント マシンの [IP アドレスを追加](sql-database-server-level-firewall-rule.md)するまで、データベース サーバーへのすべてのアクセスを遮断します。


## <a name="virtual-network-firewall-rules"></a>仮想ネットワーク ファイアウォール規則

Azure SQL Server ファイアウォールでは、IP 規則だけでなく、"*仮想ネットワーク規則*" を定義することもできます。  
詳細については、[Azure SQL Database の仮想ネットワーク サービス エンドポイントと規則](sql-database-vnet-service-endpoint-rule-overview.md)に関する記事をご覧ください。

 ### <a name="azure-networking-terminology"></a>Azure のネットワーク用語  
仮想ネットワーク ファイアウォール規則について調べる際には、次の Azure のネットワーク用語に注意してください

**仮想ネットワーク:** ご自分の Azure サブスクリプションに仮想ネットワークを関連付けることができます 

**サブネット:** 仮想ネットワークには**サブネット**が含まれます。 保持している任意の Azure 仮想マシン (VM) がサブネットに割り当てられます。 1 つのサブネットには、複数の VM や他のコンピューティング ノードが含まれる場合があります。 お使いの仮想ネットワークの外部にあるコンピューティング ノードは、アクセスを許可するようにセキュリティを構成しない限り、お使いの仮想ネットワークにはアクセスできません。

**仮想ネットワーク サービス エンドポイント:** [仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]は、プロパティ値に 1 つ以上の正式な Azure サービスの種類名が含まれるサブネットです。 この記事では、"SQL Database" という名前の Azure サービスを参照する **Microsoft.Sql** という種類名に注目します。

**仮想ネットワーク規則:** お使いの SQL Database サーバーの仮想ネットワーク規則は、SQL Database サーバーのアクセス制御リスト (ACL) に記載されているサブネットです。 SQL Database の ACL 内に記載するためには、サブネットに **Microsoft.Sql** という種類名が含まれている必要があります。 仮想ネットワーク規則は、サブネット上にあるどのノードからの通信も許可するように、お使いの SQL Database サーバーに指示します。


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP 規則と仮想ネットワーク ファイアウォール規則

Azure SQL Server のファイアウォールでは、SQL Database への通信が許可される IP アドレス範囲を指定できます。 この方法は、Azure プライベート ネットワークの外部にある安定した IP アドレスに適しています。 ただし、Azure プライベート ネットワーク内の仮想マシン (VM) では、"*動的*" IP アドレスが構成されます。 VM を再起動したときに動的 IP アドレスが変化し、その結果 IP ベースのファイアウォール規則が無効になることがあります。 運用環境では、ファイアウォール規則に動的 IP アドレスを指定することは、賢明ではありません。

この制限は、VM の "*静的*" IP アドレスを取得することで回避できます。 詳細については、「[Azure portal を使用して仮想マシンのプライベート IP アドレスを構成する][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]」を参照してください。ただし、静的 IP のアプローチは管理が困難になる場合があり、大規模に行うとコストがかさみます。 

仮想ネットワーク規則は、対象の VM を含む特定のサブネットからのアクセスを確立および管理するためのより簡単な代替手段です。

> [!NOTE]
> サブネット上に SQL Database を保持することは、まだできません。 Azure SQL Database サーバーが仮想ネットワーク内のサブネット上のノードになった場合、仮想ネットワークはお使いの SQL Database と通信できます。 この場合、仮想ネットワーク規則や IP ルールがなくても、VM は SQL Database と通信できます。

## <a name="next-steps"></a>次の手順

- サーバーレベルの IP ファイアウォール規則の作成に関するクイックスタートについては、[Azure SQL データベースの作成](sql-database-single-database-get-started.md)に関するページを参照してください。

- サーバーレベルの Vnet ファイアウォール規則の作成のクイックスタートについては、[Azure SQL Database 用の仮想ネットワーク サービス エンドポイントと規則](sql-database-vnet-service-endpoint-rule-overview.md)に関するページを参照してください。

- オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL データベースに接続する方法のヘルプについては、[SQL Database のクライアント クイックスタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関するページを参照してください。

- 他に開くことが必要な可能性のあるポートの詳細については、**SQL Database の外部と内部**に関するセクションを、[ADO.NET 4.5 と SQL Database における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)に関する記事で確認してください。

- Azure SQL Database 接続の概要については、「[Azure SQL の接続アーキテクチャ](sql-database-connectivity-architecture.md)」を参照してください

- Azure SQL Database のセキュリティの概要については、「[データベースの保護](sql-database-security-overview.md)」を参照してください。

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
