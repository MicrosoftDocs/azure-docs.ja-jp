---
title: Azure SQL Database のフェールオーバー グループを構成する
description: Azure portal、Az CLI、および PowerShell を使用して Azure SQL Database の単一データベース、エラスティック プール、およびマネージド インスタンスの自動フェールオーバー グループを構成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 9206fd264854cd9e5d8e46473dd60b05a3362fdd
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71328709"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Azure SQL Database のフェールオーバー グループを構成する

このトピックでは、Azure portal または PowerShell を使用して Azure SQL Database の単一データベース、エラスティック プール、およびマネージド インスタンスの[自動フェールオーバー グループ](sql-database-auto-failover-group.md)を構成する方法について説明します。 

## <a name="single-database"></a>単一データベース
Azure portal または PowerShell を使用して、フェールオーバー グループを作成し、そのグループに単一データベースを追加します。

### <a name="prerequisites"></a>前提条件

次の前提条件を考慮してください。

- セカンダリ サーバーのサーバー ログインとファイアウォールの設定は、プライマリ サーバーのものと一致している必要があります。 

### <a name="create-failover-group"></a>フェールオーバー グループの作成

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)
フェールオーバー グループを作成し、Azure portal を使用して単一データベースを追加します。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。 
1. フェールオーバー グループに追加する単一データベースを選択します。 
1. **[サーバー名]** の下にあるサーバーの名前を選択し、サーバーの設定を開きます。

   ![単一 DB のサーバーを開く](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **[設定]** ウィンドウで **[フェールオーバー グループ]** を選択し、 **[グループの追加]** を選択して新しいフェールオーバー グループを作成します。 

    ![新しいフェールオーバー グループの追加](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. **[フェールオーバー グループ]** ページで、必要な値を入力するか選択してから、 **[作成]** を選択します。

   - **グループ内のデータベース**:フェールオーバー グループに追加するデータベースを選択します。 フェールオーバー グループにデータベースを追加すると、geo レプリケーション プロセスが自動的に開始されます。 
        
    ![フェールオーバー グループへの SQL DB の追加](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
フェールオーバー グループを作成し、PowerShell を使用して単一データベースを追加します。 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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

---

### <a name="test-failover"></a>[テスト フェールオーバー] 

Azure portal または PowerShell を使用して、フェールオーバー グループのフェールオーバーをテストします。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、フェールオーバー グループのフェールオーバーをテストします。 

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。 
1. フェールオーバー グループに追加する単一データベースを選択します。 

   ![単一 DB のサーバーを開く](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **[設定]** ウィンドウで **[フェールオーバーグループ]** を選択し、先ほど作成したフェールオーバー グループを選択します。 
  
   ![ポータルからフェールオーバー グループを選択](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 
1. 作業ウィンドウで **[フェールオーバー]** を選択し、単一データベースを含むフェールオーバー グループをフェールオーバーします。 
1. TDS セッションが切断されることが通知される警告で **[はい]** を選択します。 

   ![SQL データベースを含むフェールオーバー グループをフェールオーバーする](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 現在、どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 フェールオーバーが成功すると、2 つのサーバー ロールがスワップされているはずです。 
1. サーバーを元のロールにフェールバックするには、もう一度 **[フェールオーバー]** を選択します。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してフェールオーバー グループのフェールオーバーをテストします。  

セカンダリ レプリカのロールを確認します。 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

フェールオーバー グループを再びプライマリ サーバーに戻します。

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

## <a name="elastic-pool"></a>エラスティック プール
Azure portal または PowerShell を使用して、フェールオーバー グループを作成し、そのグループにエラスティック プールを追加します。  

### <a name="prerequisites"></a>前提条件

次の前提条件を考慮してください。

- セカンダリ サーバーのサーバー ログインとファイアウォールの設定は、プライマリ サーバーのものと一致している必要があります。 

### <a name="create-the-failover-group"></a>フェールオーバー グループを作成する 

Azure portal または PowerShell を使用して、エラスティック プールのフェールオーバー グループを作成します。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)
Azure portal を使用して、フェールオーバー グループを作成し、エラスティック プールを追加します。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。 
1. フェールオーバー グループに追加するエラスティック プールを選択します。 
1. **[概要]** ウィンドウの **[サーバー名]** でサーバーの名前を選択し、サーバーの設定を開きます。
  
    ![エラスティック プールのサーバーを開く](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. **[設定]** ウィンドウで **[フェールオーバー グループ]** を選択し、 **[グループの追加]** を選択して新しいフェールオーバー グループを作成します。 

    ![新しいフェールオーバー グループの追加](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. **[フェールオーバー グループ]** ページで、必要な値を入力するか選択してから、 **[作成]** を選択します。 新しいセカンダリ サーバーを作成するか、既存のセカンダリ サーバーを選択します。 

1. **[Databases within the group]\(グループ内のデータベース\)** を選択し、フェールオーバー グループに追加するエラスティック プールを選択します。 エラスティック プールがセカンダリ サーバーにまだ存在しない場合は、セカンダリ サーバーにエラスティック プールを作成するように求める警告が表示されます。 警告を選択し、 **[OK]** を選択して、セカンダリ サーバー上にエラスティック プールを作成します。 
        
    ![エラスティック プールをフェールオーバー グループに追加する](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. **[選択]** を選択してエラスティック プール設定をフェールオーバー グループに適用した後、 **[作成]** を選択してフェールオーバー グループを作成します。 フェールオーバー グループにエラスティック プールを追加すると、geo レプリケーション プロセスが自動的に開始されます。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

フェールオーバー グループを作成し、PowerShell を使用してエラスティック プールを追加します。 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>[テスト フェールオーバー]

Azure portal または PowerShell を使用して、エラスティック プールのフェールオーバーをテストします。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

フェールオーバー グループをセカンダリ サーバーにフェールオーバーしてから、Azure portal を使用してフェールバックします。 

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。 
1. フェールオーバー グループに追加するエラスティック プールを選択します。 
1. **[概要]** ウィンドウの **[サーバー名]** でサーバーの名前を選択し、サーバーの設定を開きます。
  
    ![エラスティック プールのサーバーを開く](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. **[設定]** ウィンドウで **[フェールオーバーグループ]** を選択してから、セクション 2 で作成したフェールオーバー グループを選択します。 
  
   ![ポータルからフェールオーバー グループを選択](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 
1. 作業ウィンドウで **[フェールオーバー]** を選択し、エラスティック プールを含むフェールオーバー グループをフェールオーバーします。 
1. TDS セッションが切断されることを通知する警告で **[はい]** を選択します。 

   ![SQL データベースを含むフェールオーバー グループをフェールオーバーする](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 フェールオーバーが成功すると、2 つのサーバー ロールがスワップされているはずです。 
1. フェールオーバー グループを元の設定に戻すには、 **[フェールオーバー]** をもう一度選択します。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してフェールオーバー グループのフェールオーバーをテストします。

セカンダリ レプリカのロールを確認します。 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
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
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

## <a name="managed-instance"></a>マネージド インスタンス

Azure portal または PowerShell を使用して、2 つのマネージド インスタンス間にフェールオーバー グループを作成します。 

各マネージド インスタンスの仮想ネットワーク用のゲートウェイを作成し、2 つのゲートウェイを接続して、フェールオーバー グループを作成する必要があります。

### <a name="prerequisites"></a>前提条件
次の前提条件を考慮してください。

- セカンダリ マネージド インスタンスは空である必要があります。
- セカンダリ仮想ネットワークのサブネット範囲は、プライマリ仮想ネットワークのサブネット範囲と重複してはなりません。 
- セカンダリ インスタンスの照合順序とタイムゾーンは、プライマリ インスタンスと一致している必要があります。 
- 2 つのゲートウェイを接続するときは、両方の接続で **[共有キー]** が同じである必要があります。 

### <a name="create-primary-virtual-network-gateway"></a>プライマリ仮想ネットワーク ゲートウェイを作成する 

Azure portal または PowerShell を使用して、プライマリ仮想ネットワーク ゲートウェイを作成します。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、プライマリ仮想ネットワーク ゲートウェイを作成します。 

1. [Azure portal](https://portal.azure.com) で、リソース グループに移動し、プライマリ マネージド インスタンスの**仮想ネットワーク** リソースを選択します。 
1. **[設定]** で **[サブネット]** を選び、新しい**ゲートウェイ サブネット**を追加することを選択します。 既定値のままにします。 

   ![プライマリ マネージド インスタンスのゲートウェイを追加する](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. サブネット ゲートウェイが作成されたら、左側のナビゲーション ウィンドウで **[リソースの作成]** を選択し、検索ボックスに「`Virtual network gateway`」と入力します。 **Microsoft** によって公開された**仮想ネットワーク ゲートウェイ** リソースを選択します。 

   ![新しい仮想ネットワーク ゲートウェイを作成する](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. プライマリ マネージド インスタンスのゲートウェイを構成するために必要なフィールドに入力します。 

   次の表には、プライマリ マネージド インスタンスのゲートウェイに必要な値が示されています。
 
    | **フィールド** | 値 |
    | --- | --- |
    | **サブスクリプション** |  プライマリ マネージド インスタンスがあるサブスクリプション。 |
    | **Name** | 仮想ネットワーク ゲートウェイの名前。 | 
    | **[リージョン]** | セカンダリ マネージド インスタンスがあるリージョン。 |
    | **ゲートウェイの種類** | **[VPN]** を選択します。 |
    | **VPN の種類** | **[ルート ベース]** を選択します |
    | **SKU**| 既定値の `VpnGw1` のままにします。 |
    | **Location**| セカンダリ マネージド インスタンスおよびセカンダリ仮想ネットワークがある場所。   |
    | **Virtual Network**| セカンダリ マネージド インスタンスの仮想ネットワークを選択します。 |
    | **パブリック IP アドレス**| **[新規作成]** を選択します。 |
    | **パブリック IP アドレス名**| IP アドレスの名前を入力します。 |
    | &nbsp; | &nbsp; |

1. その他の値は既定値のままにし、 **[確認と作成]** を選択して仮想ネットワーク ゲートウェイの設定を確認します。

   ![プライマリ ゲートウェイの設定](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. **[作成]** を選択して、新しい仮想ネットワーク ゲートウェイを作成します。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してプライマリ仮想ネットワーク ゲートウェイを作成します。 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>セカンダリ仮想ネットワーク ゲートウェイを作成する

Azure portal または PowerShell を使用して、セカンダリ仮想ネットワーク ゲートウェイを作成します。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)
前のセクションの手順を繰り返し、セカンダリ マネージド インスタンスの仮想ネットワーク サブネットとゲートウェイを作成します。 セカンダリ マネージド インスタンスのゲートウェイを構成するために必要なフィールドに入力します。 

   次の表には、セカンダリ マネージド インスタンスのゲートウェイに必要な値が示されています。

   | **フィールド** | 値 |
   | --- | --- |
   | **サブスクリプション** |  セカンダリ マネージド インスタンスがあるサブスクリプション。 |
   | **Name** | 仮想ネットワーク ゲートウェイの名前 (`secondary-mi-gateway` など)。 | 
   | **[リージョン]** | セカンダリ マネージド インスタンスがあるリージョン。 |
   | **ゲートウェイの種類** | **[VPN]** を選択します。 |
   | **VPN の種類** | **[ルート ベース]** を選択します |
   | **SKU**| 既定値の `VpnGw1` のままにします。 |
   | **Location**| セカンダリ マネージド インスタンスおよびセカンダリ仮想ネットワークがある場所。   |
   | **Virtual Network**| セクション 2 で作成された仮想ネットワーク (`vnet-sql-mi-secondary` など) を選択します。 |
   | **パブリック IP アドレス**| **[新規作成]** を選択します。 |
   | **パブリック IP アドレス名**| IP アドレスの名前 (`secondary-gateway-IP` など) を入力します。 |
   | &nbsp; | &nbsp; |

   ![セカンダリ ゲートウェイの設定](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して、セカンダリ仮想ネットワーク ゲートウェイを作成します。 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>ゲートウェイを接続する 
Azure portal または PowerShell を使用して、2 つのゲートウェイ間の接続を作成します。 

2 つの接続を作成する必要があります。プライマリ ゲートウェイからセカンダリ ゲートウェイへの接続と、セカンダリ ゲートウェイからプライマリ ゲートウェイへの接続です。 

両方の接続に使用される共有キーは、各接続で同じである必要があります。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)
Azure portal を使用して、2 つのゲートウェイ間の接続を作成します。 

1. [Azure portal](https://portal.azure.com) でリソース グループに移動し、手順 4 で作成したプライマリ ゲートウェイを選択します。 
1. **[設定]** で **[接続]** を選び、 **[追加]** を選択して新しい接続を作成します。 

   ![プライマリ ゲートウェイへの接続を追加する](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. 接続の名前を入力し、 **[共有キー]** の値を入力します。 
1. **[2 番目の仮想ネットワーク ゲートウェイ]** を選択し、セカンダリ マネージド インスタンスのゲートウェイを選択します。 

   ![プライマリからセカンダリへの接続を作成する](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. **[OK]** を選択して、新しいプライマリ ゲートウェイからセカンダリ ゲートウェイへの接続を追加します。
1. これらの手順を繰り返し、セカンダリ マネージド インスタンスのゲートウェイから、プライマリ マネージド インスタンスのゲートウェイへの接続を作成します。 

   ![セカンダリからプライマリへの接続を作成する](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して 2 つのゲートウェイ間の接続を作成します。 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>フェールオーバー グループを作成する 
Azure portal または PowerShell を使用して、マネージド インスタンスのフェールオーバー グループを作成します。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、マネージド インスタンスのフェールオーバー グループを作成します。 

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。 
1. フェールオーバー グループに追加するプライマリ マネージド インスタンスを選択します。  
1. **[設定]** で **[インスタンスのフェールオーバー グループ]** に移動し、 **[グループの追加]** を選択して **[インスタンスのフェールオーバー グループ]** ページを開きます。 

   ![フェールオーバー グループを追加する](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. **[インスタンスのフェールオーバー グループ]** ページで、フェールオーバー グループの名前を入力し、ドロップダウンからセカンダリ マネージド インスタンスを選択します。 **[作成]** を選択して、フェールオーバー グループを作成します。 

   ![フェールオーバー グループの作成](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. フェールオーバー グループのデプロイが完了すると、 **[フェールオーバー グループ]** ページに戻ります。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して、マネージド インスタンスのフェールオーバー グループを作成します。 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>[テスト フェールオーバー]

Azure portal または PowerShell を使用して、フェールオーバー グループのフェールオーバーをテストします。 

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、フェールオーバー グループのフェールオーバーをテストします。 

1. [Azure portal](https://portal.azure.com) 内のマネージド インスタンスに移動し、[設定] で **[インスタンスのフェールオーバー グループ]** を選択します。 
1. どのマネージド インスタンスがプライマリであり、どのマネージド インスタンスがセカンダリであるかを確認します。 
1. **[フェールオーバー]** を選択し、切断中の TDS セッションに関する警告で **[はい]** を選択します。 

   ![フェールオーバー グループをフェールオーバーする](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. どのマネージド インスタンスがプライマリであり、どのインスタンスがセカンダリであるかを確認します。 フェールオーバーが成功した場合は、2 つのインスタンスのロールが切り替わっているはずです。 

   ![フェールオーバー後にマネージド インスタンスのロールが切り替わっている](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. もう一度 **[フェールオーバー]** を選択し、プライマリ インスタンスをプライマリ ロールにフェールバックします。 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してフェールオーバー グループのフェールオーバーをテストします。 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>リスナー エンドポイントを特定する

フェールオーバー グループを構成したら、アプリケーションのリスナー エンドポイントへの接続文字列を更新します。 これで、アプリケーションから、プライマリ データベース、エラスティック プール、またはマネージド インスタンスではなく、フェールオーバー グループ リスナーへの接続が維持されます。 このようにすると、Azure SQL データベース エンティティがフェールオーバーするたびに接続文字列を手動で更新する必要がありません。また、トラフィックは、その時点でプライマリであるエンティティにルーティングされます。 

リスナー エンドポイントは `fog-name.database.windows.net` という形式であり、Azure portal でフェールオーバー グループを表示すると確認できます。

![フェールオーバー グループの接続文字列](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>解説

- 単一データベースまたはプールされたデータベースのフェールオーバー グループを削除しても、レプリケーションは停止されず、レプリケートされたデータベースは削除されません。 単一データベースまたはプールされたデータベースを削除した後にフェールオーバー グループに追加する場合は、geo レプリケーションを手動で停止し、セカンダリ サーバーからデータベースを削除する必要があります。 いずれかの操作を行わないと、データベースをフェールオーバー グループに追加しようとしたときに `The operation cannot be performed due to multiple errors` のようなエラーが発生する可能性があります。 


## <a name="next-steps"></a>次の手順

フェールオーバー グループの構成手順の詳細については、次のチュートリアルを参照してください。
- [フェールオーバー グループに単一データベースを追加する](sql-database-single-database-failover-group-tutorial.md)
- [フェールオーバー グループにエラスティック プールを追加する](sql-database-elastic-pool-failover-group-tutorial.md)
- [マネージド インスタンスをフェールオーバー グループに追加する](sql-database-managed-instance-failover-group-tutorial.md)
 
Azure SQL Database の高可用性オプションの概要については、[geo レプリケーション](sql-database-active-geo-replication.md)と[自動フェールオーバー グループ](sql-database-auto-failover-group.md)に関する記事を参照してください。 
