---
title: SQL FQDN を使用して Azure Firewall アプリケーション規則を構成する
description: この記事では、Azure Firewall アプリケーション規則で SQL FQDN を構成する方法について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/17/2019
ms.author: victorh
ms.openlocfilehash: 6dbc855fb1eb2ee9bcd292c896706b31f819aa5a
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253129"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>SQL FQDN を使用して Azure Firewall アプリケーション規則を構成する

> [!IMPORTANT]
> SQL FQDN を使用した Azure Firewall アプリケーション規則は、現在パブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

SQL FQDN を使用して Azure Firewall アプリケーション規則を構成することができます。 これにより、仮想ネットワークからのアクセスを、指定された SQL サーバー インスタンスのみに制限することができます。

SQL FQDN では、以下のトラフィックをフィルター処理できます。

- Vnet から Azure SQL Database または Azure SQL Data Warehouse へ。 例: *sql server1.database.windows.net* へのアクセスのみ許可します。
- オンプレミスから Vnet で実行されている Azure SQL マネージド インスタンスまたは SQL IaaS へ。
- スポーク間から Vnet で実行されている Azure SQL マネージド インスタンスまたは SQL IaaS へ。

プレビュー期間中、SQL FQDN のフィルター処理は、[プロキシ モード](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy)でのみサポートされます (ポート 1433)。 既定のリダイレクト モードで SQL を使用する場合、[ネットワーク ルール](overview.md#network-traffic-filtering-rules)の一部として SQL サービス タグを使用してアクセスをフィルター処理することができます。
SQL IaaS のトラフィックに既定以外のポートを使用する場合は、ファイアウォール アプリケーション規則でこれらのポートを構成できます。

> [!NOTE]
> 現在、SQL FQDN を使用するアプリケーション規則は、すべてのリージョンの Azure CLI、REST、およびテンプレートで使用できます。 ポータルのユーザー インターフェイスは、段階的にリージョンに追加されており、ロールアウトが完了すればすべてのリージョンで利用できるようになります。

## <a name="configure-using-azure-cli"></a>Azure CLI を使用して構成する

1. [Azure CLI を使用して Azure Firewall](deploy-cli.md) をデプロイします。
2. Azure SQL Database、SQL Data Warehouse、または SQL マネージド インスタンスへのトラフィックをフィルター処理する場合は、SQL 接続のモードが**プロキシ**に設定されていることを確認します。 SQL 接続モードを切り替える方法については、[Azure SQL の接続アーキテクチャ](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy)を参照してください。 

   > [!NOTE]
   > SQL *プロキシ* モードを使用すると、*リダイレクト*と比較して待機時間が増加する可能性があります。 リダイレクト モードを使用して構成する場合は (Azure 内から接続するクライアントの既定)、代わりに ファイアウォール [ネットワーク ルール](tutorial-firewall-deploy-portal.md#configure-a-network-rule)で SQL [サービス タグ](service-tags.md)を使ってアクセスをフィルター処理できます。

3. SQL FQDN を使用して SQL サーバーへのアクセスを許可するアプリケーション規則を構成します。

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Azure portal を使用して構成する
1. [Azure CLI を使用して Azure Firewall](deploy-cli.md) をデプロイします。
2. Azure SQL Database、SQL Data Warehouse、または SQL マネージド インスタンスへのトラフィックをフィルター処理する場合は、SQL 接続のモードが**プロキシ**に設定されていることを確認します。 SQL 接続モードを切り替える方法については、[Azure SQL の接続アーキテクチャ](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy)を参照してください。 

   > [!NOTE]
   > SQL *プロキシ* モードを使用すると、*リダイレクト*と比較して待機時間が増加する可能性があります。 リダイレクト モードを使用して構成する場合は (Azure 内から接続するクライアントの既定)、代わりに ファイアウォール [ネットワーク ルール](tutorial-firewall-deploy-portal.md#configure-a-network-rule)で SQL [サービス タグ](service-tags.md)を使ってアクセスをフィルター処理できます。
3. 適切なプロトコル、ポート、および SQL FQDN をアプリケーション規則に追加し、 **[保存]** を選択します。
   ![SQL FQDN を使用したアプリケーション規則](media/sql-fqdn-filtering/application-rule-sql.png)
4. ファイアウォールを通過するトラフィックをフィルター処理する VNet 内の仮想マシンから SQL にアクセスします。 
5. [Azure Firewall ログ](log-analytics-samples.md)を検証し、トラフィックが許可されていることを確認します。

## <a name="next-steps"></a>次の手順

SQL のプロキシ モードとリダイレクト モードの詳細については、[Azure SQL データベースの接続アーキテクチャ](../sql-database/sql-database-connectivity-architecture.md)を参照してください。