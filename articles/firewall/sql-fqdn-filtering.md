---
title: SQL FQDN を使用して Azure Firewall アプリケーション規則を構成する
description: この記事では、Azure Firewall アプリケーション規則で SQL FQDN を構成する方法について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: c65f32cc3ce56ddf3fd235de8c002528e7a3cebd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791444"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>SQL FQDN を使用して Azure Firewall アプリケーション規則を構成する

SQL FQDN を使用して Azure Firewall アプリケーション規則を構成することができます。 これにより、仮想ネットワークからのアクセスを、指定された SQL サーバー インスタンスのみに制限することができます。

SQL FQDN では、以下のトラフィックをフィルター処理できます。

- VNets から Azure SQL Database または Azure Synapse Analytics へのトラフィック。 次に例を示します。*sql server1.database.windows.net* へのアクセスのみ許可します。
- オンプレミスから Vnet で実行されている Azure SQL Managed Instance または SQL IaaS へ。
- スポーク間から Vnet で実行されている Azure SQL Managed Instance または SQL IaaS へ。

SQL の FQDN のフィルター処理は、[プロキシ モード](../azure-sql/database/connectivity-architecture.md#connection-policy)のみでサポートされます (ポート 1433)。 既定のリダイレクト モードで SQL を使用する場合、[ネットワーク ルール](features.md#network-traffic-filtering-rules)の一部として SQL サービス タグを使用してアクセスをフィルター処理することができます。
SQL IaaS のトラフィックに既定以外のポートを使用する場合は、ファイアウォール アプリケーション規則でこれらのポートを構成できます。

## <a name="configure-using-azure-cli"></a>Azure CLI を使用して構成する

1. [Azure CLI を使用して Azure Firewall](deploy-cli.md) をデプロイします。
2. Azure SQL Database、Azure Synapse Analytics、または SQL マネージド インスタンスへのトラフィックをフィルター処理する場合は、SQL 接続のモードが **プロキシ** に設定されていることを確認します。 SQL 接続モードを切り替える方法については、「[Azure SQL の接続の設定](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli)」を参照してください。

   > [!NOTE]
   > SQL *プロキシ* モードを使用すると、*リダイレクト* と比較して待機時間が増加する可能性があります。 リダイレクト モードを使用して構成する場合は (Azure 内から接続するクライアントの既定)、代わりに ファイアウォール [ネットワーク ルール](tutorial-firewall-deploy-portal.md#configure-a-network-rule)で SQL [サービス タグ](service-tags.md)を使ってアクセスをフィルター処理できます。

3. SQL FQDN を使用して SQL サーバーへのアクセスを許可するアプリケーション ルールを含む新しいルール コレクションを作成します。

   ```azurecli
    az extension add -n azure-firewall
    
    az network firewall application-rule create \ 
    -g FWRG \
    --f azfirewall \ 
    --c sqlRuleCollection \
    --priority 1000 \
    --action Allow \
    --name sqlRule \
    --protocols mssql=1433 \
    --source-addresses 10.0.0.0/24 \
    --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-azure-powershell"></a>Azure PowerShell を使用して構成する

1. [Azure PowerShell を使用して Azure Firewall](deploy-ps.md) をデプロイします。
2. Azure SQL Database、Azure Synapse Analytics、または SQL マネージド インスタンスへのトラフィックをフィルター処理する場合は、SQL 接続のモードが **プロキシ** に設定されていることを確認します。 SQL 接続モードを切り替える方法については、「[Azure SQL の接続の設定](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli)」を参照してください。

   > [!NOTE]
   > SQL *プロキシ* モードを使用すると、*リダイレクト* と比較して待機時間が増加する可能性があります。 リダイレクト モードを使用して構成する場合は (Azure 内から接続するクライアントの既定)、代わりに ファイアウォール [ネットワーク ルール](tutorial-firewall-deploy-portal.md#configure-a-network-rule)で SQL [サービス タグ](service-tags.md)を使ってアクセスをフィルター処理できます。

3. SQL FQDN を使用して SQL サーバーへのアクセスを許可するアプリケーション ルールを含む新しいルール コレクションを作成します。

   ```azurepowershell
   $AzFw = Get-AzFirewall -Name "azfirewall" -ResourceGroupName "FWRG"
    
   $sqlRule = @{
      Name          = "sqlRule"
      Protocol      = "mssql:1433" 
      TargetFqdn    = "sql-serv1.database.windows.net"
      SourceAddress = "10.0.0.0/24"
   }
    
   $rule = New-AzFirewallApplicationRule @sqlRule
    
   $sqlRuleCollection = @{
      Name       = "sqlRuleCollection" 
      Priority   = 1000 
      Rule       = $rule
      ActionType = "Allow"
   }
    
   $ruleCollection = New-AzFirewallApplicationRuleCollection @sqlRuleCollection
    
   $Azfw.ApplicationRuleCollections.Add($ruleCollection)    
   Set-AzFirewall -AzureFirewall $AzFw    
   ```

## <a name="configure-using-the-azure-portal"></a>Azure portal を使用して構成する
1. [Azure CLI を使用して Azure Firewall](deploy-cli.md) をデプロイします。
2. Azure SQL Database、Azure Synapse Analytics、または SQL マネージド インスタンスへのトラフィックをフィルター処理する場合は、SQL 接続のモードが **プロキシ** に設定されていることを確認します。 SQL 接続モードを切り替える方法については、「[Azure SQL の接続の設定](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli)」を参照してください。  

   > [!NOTE]
   > SQL *プロキシ* モードを使用すると、*リダイレクト* と比較して待機時間が増加する可能性があります。 リダイレクト モードを使用して構成する場合は (Azure 内から接続するクライアントの既定)、代わりに ファイアウォール [ネットワーク ルール](tutorial-firewall-deploy-portal.md#configure-a-network-rule)で SQL [サービス タグ](service-tags.md)を使ってアクセスをフィルター処理できます。
3. 適切なプロトコル、ポート、および SQL FQDN をアプリケーション規則に追加し、 **[保存]** を選択します。
   ![SQL FQDN を使用したアプリケーション規則](media/sql-fqdn-filtering/application-rule-sql.png)
4. ファイアウォールを通過するトラフィックをフィルター処理する VNet 内の仮想マシンから SQL にアクセスします。 
5. [Azure Firewall ログ](./firewall-workbook.md)を検証し、トラフィックが許可されていることを確認します。

## <a name="next-steps"></a>次のステップ

SQL のプロキシ モードとリダイレクト モードの詳細については、「[Azure SQL Database 接続アーキテクチャ](../azure-sql/database/connectivity-architecture.md)」を参照してください。