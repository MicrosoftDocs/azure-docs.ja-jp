---
title: メンテナンス期間の構成 (プレビュー)
description: Azure SQL データベース、エラスティック プール、マネージド インスタンス データベースで計画メンテナンスを実行する必要がある時間の設定方法を学習します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/04/2021
ms.openlocfilehash: 210f0c52a2b27492bfa2181473043df3537157d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183201"
---
# <a name="configure-maintenance-window-preview"></a>メンテナンス期間の構成 (プレビュー)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


リソースの作成中、またはリソースの作成後の任意のタイミングで、Azure SQL データベース、エラスティック プール、または Azure SQL Managed Instance データベースの[メンテナンス期間 (プレビュー)](maintenance-window.md) を構成します。 

"*システムの既定値*" のメンテナンス期間は、ピーク営業時間中の中断を回避するため、毎日午後 5 時から午前 8 時 (リソースが配置されている Azure リージョンの現地時刻) です。 "*システムの既定値*" のメンテナンス期間が適切な時間ではない場合、他の使用可能なメンテナンス期間のいずれかを選択します。

別のメンテナンス期間に変更する機能は、すべてのサービス レベルまたはすべてのリージョンで使用できるわけではありません。 可用性の詳細については、[メンテナンス期間の可用性](maintenance-window.md#availability)に関するページを参照してください。

> [!Important]
> メンテナンス期間の構成は、Azure SQL リソースのサービス レベルの変更と同様に、時間のかかる非同期操作です。 操作の終了時に発生する短いフェールオーバーを除いて、操作中にリソースを使用できます。これは、長時間のトランザクションが中断された場合でも、通常は最大 8 秒です。 フェールオーバーの影響を最小限に抑えるには、ピーク時以外に操作を実行する必要があります。

## <a name="configure-maintenance-window-during-database-creation"></a>データベースの作成時にメンテナンス期間を構成する 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

データベース、エラスティック プール、またはマネージド インスタンスを作成するときにメンテナンス期間を構成するには、 **[追加設定]** ページで目的の **[メンテナンス期間]** を設定します。 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>1 つのデータベースまたはエラスティック プールを作成するときにメンテナンス期間を設定する

新しいデータベースまたはプールを作成する詳細な手順については、「[Azure SQL Database の単一データベースを作成する](single-database-create-quickstart.md)」を参照してください。

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="[データベースの作成] の [追加設定] タブ":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>マネージド インスタンスの作成時にメンテナンス期間を設定する

新しいマネージド インスタンスを作成する詳細な手順については、「[Azure SQL マネージド インスタンスの作成](../managed-instance/instance-create-quickstart.md)」を参照してください。

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="マネージド インスタンスの作成時の [追加設定] タブ":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次の例は、Azure PowerShell を使用してメンテナンス期間を構成する方法を示しています。 [Azure PowerShell をインストールする](/powershell/azure/install-az-ps)か、Azure Cloud Shell を使用することができます。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。


## <a name="discover-available-maintenance-windows"></a>使用可能なメンテナンス期間を検出する

メンテナンス期間を設定する場合、リージョンごとに独自のメンテナンス期間オプションがあります。これは、データベースまたはプールを配置するリージョンのタイムゾーンに対応しています。 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>SQL Database とエラスティック プールのメンテナンス期間を検出する 

次の例では、[Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) コマンドレットを使用して、*eastus2* リージョンに使用できるメンテナンス期間を返します。 データベースおよびエラスティック プールの場合は、`MaintenanceScope` を `SQLDB` に設定します。

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>SQL Managed Instance のメンテナンス期間を検出する 

次の例では、[Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) コマンドレットを使用して、*eastus2* リージョンに使用できるメンテナンス期間を返します。 マネージド インスタンスの場合は、`MaintenanceScope` を `SQLManagedInstance` に設定します。

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>単一データベースの作成時にメンテナンス期間を設定する

次の例では、[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) コマンドレットを使用して新しいデータベースを作成し、メンテナンス期間を設定します。 `-MaintenanceConfigurationId` には、データベースのリージョンの有効な値を設定する必要があります。 リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>エラスティック プールの作成時にメンテナンス期間を設定する

次の例では、[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) コマンドレットを使用して新しいエラスティック プールを作成し、メンテナンス期間を設定します。 メンテナンス期間がエラスティック プールに設定されるため、プール内のすべてのデータベースには、プールのメンテナンス期間スケジュールが設定されます。 `-MaintenanceConfigurationId` には、プールのリージョンの有効な値を設定する必要があります。 リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>マネージド インスタンスの作成時にメンテナンス期間を設定する

次の例では、[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) コマンドレットを使用して新しいマネージド インスタンスを作成し、メンテナンス期間を設定します。 メンテナンス期間がインスタンスに設定されるため、インスタンス内のすべてのデータベースには、インスタンスのメンテナンス期間スケジュールが設定されます。 `-MaintenanceConfigurationId` の場合、*MaintenanceConfigName* は、インスタンスのリージョンの有効な値である必要があります。 リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

次の例は、Azure CLI を使用してメンテナンス期間を構成する方法を示しています。 [Azure CLI をインストールする](/cli/azure/install-azure-cli)か、または Azure Cloud Shell を使用することができます。 

Azure CLI によるメンテナンス期間の構成は、SQL Managed Instance でのみ使用できます。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/cli](https://shell.azure.com/cli) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="discover-available-maintenance-windows"></a>使用可能なメンテナンス期間を検出する

メンテナンス期間を設定する場合、リージョンごとに独自のメンテナンス期間オプションがあります。これは、データベースまたはプールを配置するリージョンのタイムゾーンに対応しています。

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>SQL Database とエラスティック プールのメンテナンス期間を検出する

次の例では、[az maintenance public-configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) コマンドを使用して、*eastus2* リージョンに使用できるメンテナンス期間を返します。 データベースおよびエラスティック プールの場合は、`maintenanceScope` を `SQLDB` に設定します。

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>SQL Managed Instance のメンテナンス期間を検出する

次の例では、[az maintenance public-configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) コマンドを使用して、*eastus2* リージョンに使用できるメンテナンス期間を返します。 マネージド インスタンスの場合は、`maintenanceScope` を `SQLManagedInstance` に設定します。

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>単一データベースの作成時にメンテナンス期間を設定する

次の例では、[az sql db create](/cli/azure/sql/db#az_sql_db_create) コマンドを使用して新しいデータベースを作成し、メンテナンス期間を設定します。 `--maint-config-id` (または `-m`) には、データベースのリージョンの有効な値を設定する必要があります。 リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>エラスティック プールの作成時にメンテナンス期間を設定する

次の例では、新しいエラスティック プールを作成し、[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) コマンドレットを使用してメンテナンス期間を設定します。 メンテナンス期間がエラスティック プールに設定されるため、プール内のすべてのデータベースには、プールのメンテナンス期間スケジュールが設定されます。 `--maint-config-id` (または `-m`) には、プールのリージョンの有効な値を設定する必要があります。 リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>マネージド インスタンスの作成時にメンテナンス期間を設定する

次の例では、新しいマネージド インスタンスを作成し、[az sql mi create](/cli/azure/sql/mi#az_sql_mi_create) を使用してメンテナンス期間を設定します。 メンテナンス期間がインスタンスに設定されるため、インスタンス内のすべてのデータベースには、インスタンスのメンテナンス期間スケジュールが設定されます。 *MaintenanceConfigName* は、インスタンスのリージョンの有効な値である必要があります。 リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>既存のデータベースのメンテナンス期間を構成する


メンテナンス期間の選択をデータベースに適用すると、Azure によって必要な変更が適用されるため、場合によっては、短いフェールオーバー (数秒) が発生することがあります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

以降の手順では、Azure portal を使用して、既存のデータベース、エラスティック プール、またはマネージド インスタンスにメンテナンス期間を設定します。


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>既存のデータベースまたはエラスティック プールにメンテナンス期間を設定する

1. メンテナンス期間を設定する SQL データベースまたはエラスティック プールに移動します。
1. **[設定]** メニューで、 **[メンテナンス]** を選択し、目的のメンテナンス期間を選択します。

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="SQL データベースの [メンテナンス] ページ":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>既存のマネージド インスタンスのメンテナンス期間を設定する

1. メンテナンス期間を設定するマネージド インスタンスに移動します。
1. **[設定]** メニューで、 **[メンテナンス]** を選択し、目的のメンテナンス期間を選択します。

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="SQL マネージド インスタンスのメンテナンス ページ":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>既存のデータベースのメンテナンス期間を設定する

次の例では、[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) コマンドレットを使用して、既存のデータベースにメンテナンス期間を設定します。 `-MaintenanceConfigurationId` には、データベースのリージョンの有効な値を設定する必要があります。 リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>既存のエラスティック プールにメンテナンス期間を設定する

次の例では、[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) コマンドレットを使用して、既存のエラスティック プールにメンテナンス期間を設定します。 `$maintenanceConfig` 値がプールのリージョンの有効な値であることを確認することが重要です。  リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>既存のマネージド インスタンスのメンテナンス期間を設定する

次の例では、[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) コマンドレットを使用して、既存のマネージド インスタンスにメンテナンス期間を設定します。 `$maintenanceConfig` 値がインスタンスのリージョンの有効な値であることを確認することが重要です。  リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

次の例は、Azure CLI を使用してメンテナンス期間を構成する方法を示しています。 [Azure CLI をインストールする](/cli/azure/install-azure-cli)か、または Azure Cloud Shell を使用することができます。

## <a name="set-the-maintenance-window-for-an-existing-database"></a>既存のデータベースのメンテナンス期間を設定する

次の例では、[az sql db update](/cli/azure/sql/db#az_sql_db_update) コマンドを使用して、既存のデータベースにメンテナンス期間を設定します。 `--maint-config-id` (または `-m`) には、データベースのリージョンの有効な値を設定する必要があります。 リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>既存のエラスティック プールにメンテナンス期間を設定する

次の例では、[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) コマンドを使用して、既存のエラスティック プールにメンテナンス期間を設定します。 `maintenanceConfig` 値がプールのリージョンの有効な値であることを確認することが重要です。  リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>既存のマネージド インスタンスのメンテナンス期間を設定する

次の例では、[az sql mi update](/cli/azure/sql/mi#az_sql_mi_update) を使用して、メンテナンス期間を設定します。 メンテナンス期間がインスタンスに設定されるため、インスタンス内のすべてのデータベースには、インスタンスのメンテナンス期間スケジュールが設定されます。 `-MaintenanceConfigurationId` の場合、*MaintenanceConfigName* は、インスタンスのリージョンの有効な値である必要があります。 リージョンの有効な値を取得するには、「[使用可能なメンテナンス期間を検出する](#discover-available-maintenance-windows)」を参照してください。

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>リソースをクリーンアップする

不要な料金が発生しないように、終了後、不要になったリソースを必ず削除してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. 不要になった SQL データベースまたはエラスティック プールに移動します。
1. **[概要]** メニューで、リソースを削除するオプションを選択します。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>次のステップ

- メンテナンス期間の詳細については、[メンテナンス期間 (プレビュー)](maintenance-window.md) に関するページを参照してください。
- 詳細については、[メンテナンス期間に関する FAQ](maintenance-window-faq.yml) を参照してください。
- パフォーマンスの最適化については、「[Azure SQL Database と Azure SQL Managed Instance での監視とパフォーマンス チューニング](monitor-tune-overview.md)」を参照してください。
