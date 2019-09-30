---
title: チュートリアル:フェールオーバー グループに Azure SQL Database の単一データベースを追加する | Microsoft Docs
description: Azure portal、PowerShell、または Azure CLI を使用して Azure SQL Database の単一データベースをフェールオーバー グループに追加します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: a80dc8ccaa72a57986ed6c64f7ab7050ab4c7de5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099166"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>チュートリアル:フェールオーバー グループに Azure SQL Database の単一データベースを追加する

Azure portal、PowerShell、または Azure CLI のいずれかを使用して、Azure SQL Database の単一データベースにフェールオーバー グループを構成し、フェールオーバーをテストします。  このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> - Azure SQL Database の単一データベースを作成する。
> - 2 つの論理 SQL サーバー間に単一データベースの[フェールオーバー グループ](sql-database-auto-failover-group.md)を作成する。
> - フェールオーバーをテストする。

## <a name="prerequisites"></a>前提条件

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)
このチュートリアルを完了するには、以下のものが必要です。 

- Azure サブスクリプション。 [無料のアカウントを作成](https://azure.microsoft.com/free/)します (まだお持ちでない場合)。


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
このチュートリアルを完了するには、次のものが必要です。

- Azure サブスクリプション。 [無料のアカウントを作成](https://azure.microsoft.com/free/)します (まだお持ちでない場合)。
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
このチュートリアルを完了するには、次のものが必要です。

- Azure サブスクリプション。 [無料のアカウントを作成](https://azure.microsoft.com/free/)します (まだお持ちでない場合)。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) の最新バージョン。 

---

## <a name="1---create-a-single-database"></a>1 - 単一データベースを作成する 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - フェールオーバー グループを作成する 
この手順では、既存の Azure SQL サーバーと別のリージョンの新しい Azure SQL サーバーの間に[フェールオーバー グループ](sql-database-auto-failover-group.md)を作成します。 その後、そのフェールオーバー グループにサンプル データベースを追加します。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)
フェールオーバー グループを作成し、Azure portal を使用して単一データベースを追加します。 


1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。 
1. セクション 2 で作成した単一データベース (`mySampleDatbase` など) を選択します。 
1. **[サーバー名]** の下にあるサーバーの名前を選択し、サーバーの設定を開きます。

   ![単一 DB のサーバーを開く](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **[設定]** ウィンドウで **[フェールオーバー グループ]** を選択し、 **[グループの追加]** を選択して新しいフェールオーバー グループを作成します。 

    ![新しいフェールオーバー グループの追加](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. **[フェールオーバー グループ]** ページで、次の値を入力するか選択してから、 **[作成]** を選択します。
    - **フェールオーバー グループ名**:一意のフェールオーバー グループ名 (`failovergrouptutorial` など) を入力します。 
    - **セカンダリ サーバー**:*必要な設定を構成*するオプションを選択してから、 **[新しいサーバーの作成]** を選択します。 または、既に存在しているサーバーをセカンダリ サーバーとして選択することもできます。 次の値を入力したら、 **[選択]** を選択します。 
        - **サーバー名**: セカンダリ サーバーの一意の名前 (`mysqlsecondary` など) を入力します。 
        - **サーバー管理者ログイン**:「`azureuser`」と入力します
        - **Password**:パスワードの要件を満たす複雑なパスワードを入力します。
        - **[場所]** :ドロップダウンから場所 (`East US` など) を選択します。 この場所をプライマリ サーバーと同じ場所にすることはできません。

    > [!NOTE]
    > サーバーのログインとファイアウォールの設定が、プライマリ サーバーの設定と一致している必要があります。 
    
      ![フェールオーバー グループのセカンダリ サーバーを作成する](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **グループ内のデータベース**:セカンダリ サーバーを選択すると、このオプションがロック解除されます。 これを **[追加するデータベースを選択]** に選択してから、セクション 1 で作成したデータベースを選択します。 フェールオーバー グループにデータベースを追加すると、geo レプリケーション プロセスが自動的に開始されます。 
        
    ![フェールオーバー グループへの SQL DB の追加](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
フェールオーバー グループを作成し、PowerShell を使用して単一データベースを追加します。 

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

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   
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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
フェールオーバー グループを作成し、AZ CLI を使用して単一データベースを追加します。 

   > [!NOTE]
   > サーバーのログインとファイアウォールの設定が、プライマリ サーバーの設定と一致している必要があります。 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3 - フェールオーバーをテストする 
この手順では、フェールオーバー グループをセカンダリ サーバーにフェールオーバーしてから、Azure portal を使用してフェールバックします。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)
Azure portal を使用してフェールオーバーをテストします。 

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。 
1. セクション 2 で作成した単一データベース (`mySampleDatbase` など) を選択します。 
1. **[サーバー名]** の下にあるサーバーの名前を選択し、サーバーの設定を開きます。

   ![単一 DB のサーバーを開く](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **[設定]** ウィンドウで **[フェールオーバーグループ]** を選択してから、セクション 2 で作成したフェールオーバー グループを選択します。 
  
   ![ポータルからフェールオーバー グループを選択](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 
1. 作業ウィンドウで **[フェールオーバー]** を選択し、サンプルの単一データベースを含むフェールオーバー グループをフェールオーバーします。 
1. TDS セッションが切断されることが通知される警告で **[はい]** を選択します。 

   ![SQL データベースを含むフェールオーバー グループをフェールオーバーする](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 現在、どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 フェールオーバーが成功すると、2 つのサーバー ロールがスワップされているはずです。 
1. サーバーを元のロールにフェールバックするには、もう一度 **[フェールオーバー]** を選択します。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
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
   Write-host "Failed failover group to sucessfully to" $drServerName 
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
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
AZ CLI を使用してフェールオーバーをテストします。 


どのサーバーがセカンダリかを確認します。

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

セカンダリ サーバーにフェールオーバーします。 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

フェールオーバー グループを再びプライマリ サーバーに戻します。

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

---

## <a name="clean-up-resources"></a>リソースのクリーンアップ 
リソース グループを削除して、リソースをクリーンアップします。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)
Azure portal を使用してリソース グループを削除します。 


1. [Azure Portal](https://portal.azure.com) で、リソース グループに移動します。
1. グループ内のすべてのリソースと、リソース グループ自体を削除するには、 **[リソースグループの削除]** を選択します。 
1. リソース グループの名前 (`myResourceGroup`) をテキストボックスに入力し、 **[削除]** を選択してリソース グループを削除します。  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell を使用してリソース グループを削除します。 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
AZ CLI を使用してリソース グループを削除します。 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

---


## <a name="full-scripts"></a>完全なスクリプト

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)
Azure portal に使用できるスクリプトはありません。
 
---

他の Azure SQL Database スクリプトについては、「[Azure SQL Database 用の Azure PowerShell サンプル](sql-database-powershell-samples.md)」と「[Azure SQL Database 用の Azure CLI サンプル](sql-database-cli-samples.md)」で見つけることができます。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、フェールオーバー グループに Azure SQL Database の単一データベースを追加し、フェールオーバーをテストしました。 以下の方法について学習しました。

> [!div class="checklist"]
> - Azure SQL Database の単一データベースを作成する。 
> - 2 つの論理 SQL サーバー間に単一データベースの[フェールオーバー グループ](sql-database-auto-failover-group.md)を作成する。
> - フェールオーバーをテストする。

エラスティック プールをフェールオーバー グループに追加する方法については、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [チュートリアル:フェールオーバー グループに Azure SQL Database エラスティック プールを追加する](sql-database-elastic-pool-failover-group-tutorial.md)
