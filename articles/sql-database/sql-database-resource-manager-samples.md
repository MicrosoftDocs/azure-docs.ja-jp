---
title: SQL Database 用 Azure Resource Manager テンプレート | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して、Azure SQL Database を作成および構成します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 81f3e4beb29f21f1b752a876827e9b35856c713a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566888"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Azure SQL Database 用 Azure Resource Manager テンプレート

Azure Resource Manager テンプレートを使用すると、インフラストラクチャをコードとして定義し、ソリューションを Azure クラウドにデプロイできます。

## <a name="single-database--elastic-pool"></a>単一データベースとエラスティック プール

次の表は、Azure SQL Database 用の Azure Resource Manager テンプレートのリンク一覧です。

| |  |
|---|---|
| [1 つのデータベース](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | この Azure Resource Manager テンプレートでは、単一 Azure SQL データベースと論理サーバーを作成し、ファイアウォール規則を構成します。 |
| [論理サーバー](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | この Azure Resource Manager テンプレートでは、Azure SQL Database 用の論理サーバーを作成します。 |
| [エラスティック プール](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | このテンプレートを使用すると、新しいエラスティック プールと、それに関連付けられている新しい SQL サーバーおよびそれに割り当てられる新しい SQL データベースをデプロイできます。 |
| [フェールオーバー グループ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | このテンプレートでは、2 つの Azure SQL 論理サーバー、1 つの SQL データベース、および 1 つのフェールオーバー グループを作成します。|
| [脅威の検出](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | このテンプレートを使用すると、Azure SQL 論理サーバーと、脅威検出が有効になっている Azure SQL データベースのセット、および各データベースのアラート用のメール アドレスをデプロイできます。 脅威検出は、SQL Advanced Threat Protection (ATP) サービスの一部であり、SQL サーバーおよびデータベースに対する潜在的な脅威に対応するためのセキュリティ層を提供します。|
| [Azure Blob Storage の監査](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | このテンプレートを使用すると、監査を有効にした Azure SQL 論理サーバーをデプロイして、監査ログを BLOB ストレージに書き込むことができます。 Azure SQL Database を監査すると、データベース イベントが追跡され、監査ログに書き込まれます。監査ログは、Azure ストレージ アカウント、OMS ワークスペース、または Event Hubs に表示できます。|
| [Azure イベント ハブの監査](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | このテンプレートを使用すると、監査を有効にした Azure SQL サーバーをデプロイして、監査ログを既存のイベント ハブに書き込むことができます。 監査イベントをイベント ハブに送信するには、`Enabled` `State` で監査設定を設定し、`IsAzureMonitorTargetEnabled` を `true` に設定します。 また、`master` データベースの `SQLSecurityAuditEvents` 診断ログ カテゴリで診断設定を構成します (サーバー レベルの監査用)。 Azure SQL Database と SQL Data Warehouse を監査すると、データベース イベントが追跡され、監査ログに書き込まれます。監査ログは、Azure ストレージ アカウント、OMS ワークスペース、または Event Hubs に表示できます。|
| [Azure Web アプリと SQL データベース](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | このサンプルでは、無料の Azure Web アプリと SQL データベースを "Basic" サービス レベルで作成します。|
| [Azure Web アプリおよび Redis Cache と SQL データベース](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | このテンプレートでは、同じリソース グループ内に Web アプリ、Redis Cache、および SQL データベースを作成し、Web アプリ内に SQL Database と Redis Cache 用の 2 つの接続文字列を作成します。|
| [ADF V2 を使用した BLOB ストレージからのデータのインポート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | この Azure Resource Manager テンプレートでは、Azure Blob Storage から SQL Database にデータをコピーする Azure Data Factory V2 を作成します。|
| [HDInsight クラスターと SQL データベース](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | このテンプレートを使用すると、HDInsight クラスター、SQL Database サーバー、SQL データベース、および 2 つのテーブルを作成できます。 このテンプレートは、[HDInsight の Hadoop での Sqoop の使用](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop)に関する記事で使用されています。 |
| [スケジュールに従って SQL ストアド プロシージャを実行する Azure ロジック アプリ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | このテンプレートを使用すると、スケジュールに従って SQL ストアド プロシージャを実行するロジック アプリを作成できます。 プロシージャの引数は、テンプレートの body セクションに配置できます。|

## <a name="managed-instance"></a>マネージド インスタンス

次の表は、Azure SQL Database - マネージド インスタンス用の Azure Resource Manager テンプレートのリンク一覧です。

| |  |
|---|---|
| [新しい VNet のマネージド インスタンス](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | この Azure Resource Manager テンプレートでは、新しい構成済みの Azure VNet と、VNet 内のマネージド インスタンスを作成します。 |
| [マネージド インスタンスのネットワーク環境](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | このデプロイでは、2 つのサブネットを持つ構成済み Azure 仮想ネットワークが作成されます。1 つのサブネットはマネージド インスタンス専用で、もう 1 つには他のリソース (VM、App Service 環境など) を配置できます。 このテンプレートでは、マネージド インスタンスをデプロイすることができる、適切に構成されたネットワーク環境が作成されます。 |
| [マネージド インスタンスと P2S 接続](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | このデプロイでは、`ManagedInstance` と `GatewaySubnet` という 2 つのサブネットを持つ Azure 仮想ネットワークが作成されます。 マネージド インスタンスは、ManagedInstance サブネットにデプロイされます。 仮想ネットワーク ゲートウェイは `GatewaySubnet` サブネットに作成され、ポイント対サイト VPN 接続用に構成されます。 |
| [マネージド インスタンスと仮想マシン](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | このデプロイでは、`ManagedInstance` と `Management` という 2 つのサブネットを持つ Azure 仮想ネットワークが作成されます。 マネージド インスタンスは、`ManagedInstance` サブネットにデプロイされます。 最新バージョンの SQL Server Management Studio (SSMS) を持つ仮想マシンは、`Management` サブネットにデプロイされます。 |
