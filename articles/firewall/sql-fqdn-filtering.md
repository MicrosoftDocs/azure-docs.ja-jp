---
title: SQL FQDN を使用して Azure Firewall アプリケーション規則を構成する
description: この記事では、Azure Firewall アプリケーション規則で SQL FQDN を構成する方法について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: e921ea707e4a0df968d162726fe53064491cb032
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610508"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>SQL FQDN を使用して Azure Firewall アプリケーション規則を構成する

SQL FQDN を使用して Azure Firewall アプリケーション規則を構成することができます。 これにより、仮想ネットワークからのアクセスを、指定された SQL サーバー インスタンスのみに制限することができます。

SQL FQDN では、以下のトラフィックをフィルター処理できます。

- Vnet から Azure SQL Database または Azure SQL Data Warehouse へ。 次に例を示します。*sql server1.database.windows.net* へのアクセスのみ許可します。
- オンプレミスから Vnet で実行されている Azure SQL Managed Instance または SQL IaaS へ。
- スポーク間から Vnet で実行されている Azure SQL Managed Instance または SQL IaaS へ。

SQL の FQDN のフィルター処理は、[プロキシ モード](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy)のみでサポートされます (ポート 1433)。 既定のリダイレクト モードで SQL を使用する場合、[ネットワーク ルール](features.md#network-traffic-filtering-rules)の一部として SQL サービス タグを使用してアクセスをフィルター処理することができます。
SQL IaaS のトラフィックに既定以外のポートを使用する場合は、ファイアウォール アプリケーション規則でこれらのポートを構成できます。

## <a name="configure-using-azure-cli"></a>Azure CLI を使用して構成する

1. [Azure CLI を使用して Azure Firewall](deploy-cli.md) をデプロイします。
2. Azure SQL Database、SQL Data Warehouse、または SQL Managed Instance へのトラフィックをフィルター処理する場合は、SQL 接続のモードが**プロキシ**に設定されていることを確認します。 SQL 接続モードを切り替える方法については、「[Azure SQL の接続の設定](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)」を参照してください。

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
2. Azure SQL Database、SQL Data Warehouse、または SQL Managed Instance へのトラフィックをフィルター処理する場合は、SQL 接続のモードが**プロキシ**に設定されていることを確認します。 SQL 接続モードを切り替える方法については、「[Azure SQL の接続の設定](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli)」を参照してください。  

   > [!NOTE]
   > SQL *プロキシ* モードを使用すると、*リダイレクト*と比較して待機時間が増加する可能性があります。 リダイレクト モードを使用して構成する場合は (Azure 内から接続するクライアントの既定)、代わりに ファイアウォール [ネットワーク ルール](tutorial-firewall-deploy-portal.md#configure-a-network-rule)で SQL [サービス タグ](service-tags.md)を使ってアクセスをフィルター処理できます。
3. 適切なプロトコル、ポート、および SQL FQDN をアプリケーション規則に追加し、 **[保存]** を選択します。
   ![SQL FQDN を使用したアプリケーション規則](media/sql-fqdn-filtering/application-rule-sql.png)
4. ファイアウォールを通過するトラフィックをフィルター処理する VNet 内の仮想マシンから SQL にアクセスします。 
5. [Azure Firewall ログ](log-analytics-samples.md)を検証し、トラフィックが許可されていることを確認します。

## <a name="next-steps"></a>次のステップ

SQL のプロキシ モードとリダイレクト モードの詳細については、「[Azure SQL Database 接続アーキテクチャ](../azure-sql/database/connectivity-architecture.md)」を参照してください。
