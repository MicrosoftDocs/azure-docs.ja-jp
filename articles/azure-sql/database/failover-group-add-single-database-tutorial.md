---
title: チュートリアル:フェールオーバー グループにデータベースを追加する
description: Azure portal、PowerShell、または Azure CLI を使用して、Azure SQL Database のデータベースを自動フェールオーバー グループに追加します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/19/2019
ms.openlocfilehash: 68fa089713c3dd89b4699011ded7d667bca6f73f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178077"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>チュートリアル:自動フェールオーバー グループに Azure SQL Database を追加する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[フェールオーバー グループ](auto-failover-group-overview.md)は、複数の geo レプリケートされたデータベースをグループ化できるようにする、宣言型の抽象化レイヤーです。 Azure portal、PowerShell、または Azure CLI のいずれかを使用して、Azure SQL Database にフェールオーバー グループを構成し、フェールオーバーをテストする方法について説明します。  このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> - Azure SQL Database にデータベースを作成する
> - 2 つのサーバー間にデータベースのフェールオーバー グループを作成する。
> - フェールオーバーをテストする。

## <a name="prerequisites"></a>前提条件

# <a name="the-portal"></a>[ポータル](#tab/azure-portal)

このチュートリアルを完了するには、以下のものが必要です。

- Azure サブスクリプション。 [無料のアカウントを作成](https://azure.microsoft.com/free/)します (まだお持ちでない場合)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

このチュートリアルを完了するには、次のものが必要です。

- Azure サブスクリプション。 [無料のアカウントを作成](https://azure.microsoft.com/free/)します (まだお持ちでない場合)。
- [Azure PowerShell](/powershell/azure/)

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

このチュートリアルを完了するには、次のものが必要です。

- Azure サブスクリプション。 [無料のアカウントを作成](https://azure.microsoft.com/free/)します (まだお持ちでない場合)。
- 最新バージョンの [Azure CLI](/cli/azure/install-azure-cli)。

---

## <a name="1---create-a-database"></a>1 - データベースを作成する

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - フェールオーバー グループを作成する

この手順では、既存のサーバーと別のリージョンの新しいサーバーの間に[フェールオーバー グループ](auto-failover-group-overview.md)を作成します。 その後、そのフェールオーバー グループにサンプル データベースを追加します。

# <a name="the-portal"></a>[ポータル](#tab/azure-portal)

フェールオーバー グループを作成し、Azure portal を使用してデータベースを追加します。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。
1. セクション 1 で作成したデータベース (`mySampleDatabase` など) を選択します。
1. フェールオーバー グループは、サーバー レベルで構成できます。 **[サーバー名]** の下にあるサーバーの名前を選択し、サーバーの設定を開きます。

   ![データベースのサーバーを開く](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. **[設定]** ウィンドウで **[フェールオーバー グループ]** を選択し、 **[グループの追加]** を選択して新しいフェールオーバー グループを作成します。

   ![新しいフェールオーバー グループの追加](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. **[フェールオーバー グループ]** ページで、次の値を入力するか選択してから、 **[作成]** を選択します。

   - **フェールオーバー グループ名**:一意のフェールオーバー グループ名 (`failovergrouptutorial` など) を入力します。
   - **セカンダリ サーバー**:*必要な設定を構成* するオプションを選択してから、 **[新しいサーバーの作成]** を選択します。 または、既に存在しているサーバーをセカンダリ サーバーとして選択することもできます。 次の値を入力したら、 **[選択]** を選択します。
      - **[サーバー名]** : セカンダリ サーバーの一意の名前 (`mysqlsecondary` など) を入力します。
      - **サーバー管理者ログイン**:「`azureuser`」と入力します
      - **パスワード**:パスワードの要件を満たす複雑なパスワードを入力します。
      - **[場所]** :ドロップダウンから場所 (`East US` など) を選択します。 この場所をプライマリ サーバーと同じ場所にすることはできません。

     > [!NOTE]
     > サーバーのログインとファイアウォールの設定が、プライマリ サーバーの設定と一致している必要があります。

     ![フェールオーバー グループのセカンダリ サーバーを作成する](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **グループ内のデータベース**:セカンダリ サーバーを選択すると、このオプションがロック解除されます。 これを **[追加するデータベースを選択]** に選択してから、セクション 1 で作成したデータベースを選択します。 フェールオーバー グループにデータベースを追加すると、geo レプリケーション プロセスが自動的に開始されます。

   ![フェールオーバー グループに SQL Database を追加する](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

フェールオーバー グループを作成し、PowerShell を使用してデータベースを追加します。

   > [!NOTE]
   > サーバーのログインとファイアウォールの設定が、プライマリ サーバーの設定と一致している必要があります。

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 単一データベースとエラスティック プールをホストする Azure SQL Database 内のサーバーを作成します。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Azure SQL Database 内のサーバーのファイアウォール規則を作成します。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Azure SQL Database に新しい単一データベースを作成します。 |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Azure SQL Database に新しいフェールオーバー グループを作成します。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Azure SQL Database で 1 つまたは複数のデータベースを取得します。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Azure SQL Database 内のフェールオーバー グループに 1 つまたは複数のデータベースを追加します。 |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

フェールオーバー グループを作成し、Azure CLI を使用してデータベースを追加します。

   > [!NOTE]
   > サーバーのログインとファイアウォールの設定が、プライマリ サーバーの設定と一致している必要があります。

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

チュートリアルのこの部分では、次の Azure CLI コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | データベースとエラスティック プールをホストするサーバーを作成します。 |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | サーバーのファイアウォール規則を作成します。 |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) | フェールオーバー グループを更新します。 |

---

## <a name="3---test-failover"></a>3 - フェールオーバーをテストする

この手順では、フェールオーバー グループをセカンダリ サーバーにフェールオーバーしてから、Azure portal を使用してフェールバックします。

# <a name="the-portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してフェールオーバーをテストします。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。
1. セクション 2 で作成したデータベース (`mySampleDatbase` など) を選択します。
1. **[サーバー名]** の下にあるサーバーの名前を選択し、サーバーの設定を開きます。

   ![データベースのサーバーを開く](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. **[設定]** ウィンドウで **[フェールオーバーグループ]** を選択してから、セクション 2 で作成したフェールオーバー グループを選択します。
  
   ![ポータルからフェールオーバー グループを選択](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。
1. 作業ペインで **[フェールオーバー]** を選択し、サンプルのデータベースを含むフェールオーバー グループをフェールオーバーします。
1. TDS セッションが切断されることが通知される警告で **[はい]** を選択します。

   ![データベースが含まれるフェールオーバー グループをフェールオーバーする](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. 現在、どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 フェールオーバーが成功すると、2 つのサーバー ロールがスワップされているはずです。
1. サーバーを元のロールにフェールバックするには、もう一度 **[フェールオーバー]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してフェールオーバーをテストします。

セカンダリ レプリカのロールを確認します。

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

セカンダリ サーバーにフェールオーバーします。

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName
   ```

フェールオーバー グループを再びプライマリ サーバーに戻します。

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL Database のフェールオーバー グループを取得または一覧表示します。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Databese のフェールオーバー グループのフェールオーバーを実行します。 |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してフェールオーバーをテストします。

どのサーバーがセカンダリかを確認します。

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

セカンダリ サーバーにフェールオーバーします。

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

フェールオーバー グループを再びプライマリ サーバーに戻します。

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

チュートリアルのこの部分では、次の Azure CLI コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group#az-sql-failover-group-list) | サーバーにフェールオーバー グループが表示されます。 |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 現在のプライマリ サーバーからすべてのデータベースをフェールオーバーして、フェールオーバー グループのプライマリを設定します。 |

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループを削除して、リソースをクリーンアップします。

# <a name="the-portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してリソース グループを削除します。

1. [Azure Portal](https://portal.azure.com) で、リソース グループに移動します。
1. グループ内のすべてのリソースと、リソース グループ自体を削除するには、 **[リソースグループの削除]** を選択します。
1. リソース グループの名前 (`myResourceGroup`) をテキストボックスに入力し、 **[削除]** を選択してリソース グループを削除します。  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してリソース グループを削除します。

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループを削除します |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してリソース グループを削除します。

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

チュートリアルのこの部分では、次の Azure CLI コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

---

> [!IMPORTANT]
> リソース グループを保持し、セカンダリ データベースを削除する場合は、削除する前にそれをフェールオーバー グループから削除します。 セカンダリ データベースをフェールオーバー グループから削除する前に削除すると、予期しない動作が発生する可能性があります。

## <a name="full-scripts"></a>完全なスクリプト

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Azure SQL Database 内の単一データベースとエラスティック プールをホストするサーバーを作成します。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Azure SQL Database 内のサーバーのファイアウォール規則を作成します。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Azure SQL Database に新しいデータベースを作成します。 |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Azure SQL Database に新しいフェールオーバー グループを作成します。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Azure SQL Database で 1 つまたは複数のデータベースを取得します。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Azure SQL Database 内のフェールオーバー グループに 1 つまたは複数のデータベースを追加します。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL Database 内のフェールオーバー グループを取得または一覧表示します。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Databese 内のフェールオーバー グループのフェールオーバーを実行します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Azure SQL Database 内のリソース グループを削除します。|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az account set](/cli/azure/account#az-account-set) | サブスクリプションを現在のアクティブなサブスクリプションとして設定します。 |
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Azure SQL Database 内の単一データベースとエラスティック プールをホストするサーバーを作成します。 |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Azure SQL Database にサーバー レベルの IP ファイアウォール規則を作成します。 |
| [az sql db create](/cli/azure/sql/db) | Azure SQL Database にデータベースを作成します。 |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) | Azure SQL Database にフェールオーバー グループを作成します。 |
| [az sql failover-group list](/cli/azure/sql/failover-group#az-sql-failover-group-list) | Azure SQL Database 内のサーバーのフェールオーバー グループを一覧表示します。 |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | 現在のプライマリ サーバーからすべてのデータベースをフェールオーバーして、フェールオーバー グループのプライマリを設定します。 |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

# <a name="the-portal"></a>[ポータル](#tab/azure-portal)

Azure portal に使用できるスクリプトはありません。

---

他の Azure SQL Database スクリプトについては、「[Azure SQL Database 用の Azure PowerShell サンプル](powershell-script-content-guide.md)」と「[Azure SQL Database 用の Azure CLI サンプル](az-cli-script-samples-content-guide.md)」で見つけることができます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、フェールオーバー グループに Azure SQL Database のデータベースを追加し、フェールオーバーをテストしました。 以下の方法を学習しました。

> [!div class="checklist"]
>
> - Azure SQL Database にデータベースを作成する
> - 2 つのサーバー間にデータベースのフェールオーバー グループを作成する。
> - フェールオーバーをテストする。

エラスティック プールをフェールオーバー グループに追加する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル:フェールオーバー グループに Azure SQL Database エラスティック プールを追加する](failover-group-add-elastic-pool-tutorial.md)