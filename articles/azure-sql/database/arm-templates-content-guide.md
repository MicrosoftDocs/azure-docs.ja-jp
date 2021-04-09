---
title: Azure Resource Manager テンプレート - Azure SQL Database および SQL Managed Instance
description: Azure Resource Manager テンプレートを使用して、Azure SQL Database と Azure SQL Managed Instance を作成および構成します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 5202145af6f60f34bdce57aa94f4fec92a8b227f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92677257"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Azure SQL Database および SQL Managed Instance 用 Azure Resource Manager テンプレート
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Resource Manager テンプレートを使用すると、インフラストラクチャをコードとして定義し、Azure SQL Database と Azure SQL Managed Instance の Azure クラウドにソリューションをデプロイできます。

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

次の表は、Azure SQL Database 用の Azure Resource Manager テンプレートのリンク一覧です。

|Link |説明|
|---|---|
| [SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | この Azure Resource Manager テンプレートでは、Azure SQL Database に単一データベースを作成し、サーバーレベルの IP ファイアウォール規則を構成します。 |
| [サーバー](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | この Azure Resource Manager テンプレートでは、Azure SQL Database 用のサーバーを作成します。 |
| [エラスティック プール](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | このテンプレートを使用すると、エラスティック プールをデプロイして、そこにデータベースを割り当てることができます。 |
| [フェールオーバー グループ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | このテンプレートは、2 つのサーバー、単一データベース、フェールオーバー グループを Azure SQL Database に作成します。|
| [脅威の検出](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | このテンプレートを使用すると、サーバーと、脅威検出が有効になっているデータベースのセット、および各データベースのアラート用のメール アドレスをデプロイできます。 脅威検出は、SQL Advanced Threat Protection (ATP) サービスの一部であり、サーバーおよびデータベースに対する潜在的な脅威に対応するためのセキュリティ層を提供します。|
| [Azure Blob Storage への監査の出力](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | このテンプレートを使用すると、監査を有効にしたサーバーをデプロイして、監査ログを Blob Storage に書き込むことができます。 Azure SQL Database を監査すると、データベース イベントが追跡され、監査ログに書き込まれます。監査ログは、Azure ストレージ アカウント、OMS ワークスペース、または Event Hubs に表示できます。|
| [Azure イベント ハブの監査](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | このテンプレートを使用すると、監査を有効にしたサーバーをデプロイして、監査ログを既存のイベント ハブに書き込むことができます。 監査イベントをイベント ハブに送信するには、`Enabled` `State` で監査設定を設定し、`IsAzureMonitorTargetEnabled` を `true` に設定します。 また、`master` データベースの `SQLSecurityAuditEvents` ログ カテゴリで診断設定を構成します (サーバーレベルの監査用)。 監査によってデータベース イベントが追跡され、監査ログに書き込まれます。監査ログは、Azure ストレージ アカウント、OMS ワークスペース、または Event Hubs に置くことができます。|
| [Azure Web アプリと SQL データベース](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | このサンプルでは、無料の Azure Web アプリと Azure SQL Database のデータベースを "Basic" サービス レベルで作成します。|
| [Azure Web アプリおよび Redis Cache と SQL データベース](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | このテンプレートでは、同じリソース グループ内に Web アプリ、Redis Cache、データベースを作成し、Web アプリ内にデータベースと Redis Cache 用の 2 つの接続文字列を作成します。|
| [ADF V2 を使用した Blob Storage からのデータのインポート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | この Azure Resource Manager テンプレートでは、Azure Blob Storage から SQL Database にデータをコピーする Azure Data Factory V2 のインスタンスを作成します。|
| [HDInsight クラスターとデータベース](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | このテンプレートを使用すると、HDInsight クラスターと論理 SQL サーバー、データベース、2 つのテーブルを作成できます。 このテンプレートは、[HDInsight の Hadoop での Sqoop の使用](../../hdinsight/hadoop/hdinsight-use-sqoop.md)に関する記事で使用されています。 |
| [スケジュールに従って SQL ストアド プロシージャを実行する Azure Logic Apps](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | このテンプレートを使用すると、スケジュールに従って SQL ストアド プロシージャを実行する Logic Apps を作成できます。 プロシージャの引数は、テンプレートの body セクションに配置できます。|

## <a name="azure-sql-managed-instance"></a>[Azure SQL Managed Instance](#tab/managed-instance)

次の表は、Azure SQL Managed Instance 用の Azure Resource Manager テンプレートのリンク一覧です。

|Link|説明|
|---|---|
| [新しい VNet の SQL Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | この Azure Resource Manager テンプレートでは、新しい構成済みの Azure 仮想ネットワークを作成し、その仮想ネットワーク内にマネージド インスタンスを作成します。 |
| [SQL Managed Instance のネットワーク環境](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | このデプロイでは、2 つのサブネットを持つ構成済み Azure 仮想ネットワークが作成されます。1 つのサブネットはマネージド インスタンス専用で、もう 1 つには他のリソース (VM、App Service 環境など) を配置できます。 このテンプレートでは、マネージド インスタンスをデプロイすることができる、適切に構成されたネットワーク環境が作成されます。 |
| [SQL Managed Instance と P2S 接続](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | このデプロイでは、`ManagedInstance` と `GatewaySubnet` という 2 つのサブネットを持つ Azure 仮想ネットワークが作成されます。 SQL Managed Instance は、ManagedInstance サブネットにデプロイされます。 仮想ネットワーク ゲートウェイは `GatewaySubnet` サブネットに作成され、ポイント対サイト VPN 接続用に構成されます。 |
| [SQL Managed Instance と仮想マシン](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | このデプロイでは、`ManagedInstance` と `Management` という 2 つのサブネットを持つ Azure 仮想ネットワークが作成されます。 SQL Managed Instance は、`ManagedInstance` サブネットにデプロイされます。 最新バージョンの SQL Server Management Studio (SSMS) を持つ仮想マシンは、`Management` サブネットにデプロイされます。 |

---