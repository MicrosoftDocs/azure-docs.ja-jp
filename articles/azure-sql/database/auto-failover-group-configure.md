---
title: フェールオーバー グループを構成する
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure portal、Azure CLI、および PowerShell を使用して、Azure SQL Database (単一データベースとプールされたデータベースの両方) および SQL Managed Instance の自動フェールオーバー グループを構成する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: 09bb7cb2344e3e708a64842916e6e483136da3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94594285"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Azure SQL Database のフェールオーバー グループを構成する
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

このトピックでは、Azure SQL Database と Azure SQL Managed Instance の[自動フェールオーバー グループ](auto-failover-group-overview.md)を構成する方法について説明します。

## <a name="single-database"></a>単一データベース

Azure portal または PowerShell を使用して、フェールオーバー グループを作成し、そのグループに単一データベースを追加します。

### <a name="prerequisites"></a>前提条件

次の前提条件を考慮してください。

- セカンダリ サーバーのサーバー ログインとファイアウォールの設定は、プライマリ サーバーのものと一致している必要があります。

### <a name="create-failover-group"></a>フェールオーバー グループの作成

# <a name="portal"></a>[ポータル](#tab/azure-portal)

フェールオーバー グループを作成し、Azure portal を使用して単一データベースを追加します。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。
1. フェールオーバー グループに追加するデータベースを選択します。
1. **[サーバー名]** の下にあるサーバーの名前を選択し、サーバーの設定を開きます。

   ![単一 DB のサーバーを開く](./media/auto-failover-group-configure/open-sql-db-server.png)

1. **[設定]** ウィンドウで **[フェールオーバー グループ]** を選択し、 **[グループの追加]** を選択して新しいフェールオーバー グループを作成します。

   ![新しいフェールオーバー グループの追加](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. **[フェールオーバー グループ]** ページで、必要な値を入力するか選択してから、 **[作成]** を選択します。

   - **グループ内のデータベース**:フェールオーバー グループに追加するデータベースを選択します。 フェールオーバー グループにデータベースを追加すると、geo レプリケーション プロセスが自動的に開始されます。

   ![フェールオーバー グループに SQL Database を追加する](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

フェールオーバー グループを作成し、PowerShell を使用してデータベースを追加します。

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
   Write-host "Creating a secondary server in the failover region..."
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

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、フェールオーバー グループのフェールオーバーをテストします。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。
1. フェールオーバー グループに追加するデータベースを選択します。

   ![単一 DB のサーバーを開く](./media/auto-failover-group-configure/open-sql-db-server.png)

1. **[設定]** ウィンドウで **[フェールオーバーグループ]** を選択し、先ほど作成したフェールオーバー グループを選択します。
  
   ![ポータルからフェールオーバー グループを選択](./media/auto-failover-group-configure/select-failover-group.png)

1. どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。
1. 作業ウィンドウで **[フェールオーバー]** を選択し、データベースが含まれるフェールオーバー グループをフェールオーバーします。
1. TDS セッションが切断されることが通知される警告で **[はい]** を選択します。

   ![データベースが含まれるフェールオーバー グループをフェールオーバーする](./media/auto-failover-group-configure/failover-sql-db.png)

1. 現在、どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 フェールオーバーが成功すると、2 つのサーバー ロールがスワップされているはずです。
1. サーバーを元のロールにフェールバックするには、もう一度 **[フェールオーバー]** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

> [!IMPORTANT]
> セカンダリ データベースを削除する必要がある場合は、削除する前にそれをフェールオーバー グループから削除します。 セカンダリ データベースをフェールオーバー グループから削除する前に削除すると、予期しない動作が発生する可能性があります。

## <a name="elastic-pool"></a>エラスティック プール

Azure portal または PowerShell を使用して、フェールオーバー グループを作成し、そのグループにエラスティック プールを追加します。  

### <a name="prerequisites"></a>前提条件

次の前提条件を考慮してください。

- セカンダリ サーバーのサーバー ログインとファイアウォールの設定は、プライマリ サーバーのものと一致している必要があります。

### <a name="create-the-failover-group"></a>フェールオーバー グループを作成する

Azure portal または PowerShell を使用して、エラスティック プールのフェールオーバー グループを作成します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、フェールオーバー グループを作成し、エラスティック プールを追加します。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。
1. フェールオーバー グループに追加するエラスティック プールを選択します。
1. **[概要]** ウィンドウの **[サーバー名]** でサーバーの名前を選択し、サーバーの設定を開きます。
  
   ![エラスティック プールのサーバーを開く](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. **[設定]** ウィンドウで **[フェールオーバー グループ]** を選択し、 **[グループの追加]** を選択して新しいフェールオーバー グループを作成します。

   ![新しいフェールオーバー グループの追加](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. **[フェールオーバー グループ]** ページで、必要な値を入力するか選択してから、 **[作成]** を選択します。 新しいセカンダリ サーバーを作成するか、既存のセカンダリ サーバーを選択します。

1. **[Databases within the group]\(グループ内のデータベース\)** を選択し、フェールオーバー グループに追加するエラスティック プールを選択します。 エラスティック プールがセカンダリ サーバーにまだ存在しない場合は、セカンダリ サーバーにエラスティック プールを作成するように求める警告が表示されます。 警告を選択し、 **[OK]** を選択して、セカンダリ サーバー上にエラスティック プールを作成します。

   ![エラスティック プールをフェールオーバー グループに追加する](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. **[選択]** を選択してエラスティック プール設定をフェールオーバー グループに適用した後、 **[作成]** を選択してフェールオーバー グループを作成します。 フェールオーバー グループにエラスティック プールを追加すると、geo レプリケーション プロセスが自動的に開始されます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="portal"></a>[ポータル](#tab/azure-portal)

フェールオーバー グループをセカンダリ サーバーにフェールオーバーしてから、Azure portal を使用してフェールバックします。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。
1. フェールオーバー グループに追加するエラスティック プールを選択します。
1. **[概要]** ウィンドウの **[サーバー名]** でサーバーの名前を選択し、サーバーの設定を開きます。

   ![エラスティック プールのサーバーを開く](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. **[設定]** ウィンドウで **[フェールオーバーグループ]** を選択してから、セクション 2 で作成したフェールオーバー グループを選択します。
  
   ![ポータルからフェールオーバー グループを選択](./media/auto-failover-group-configure/select-failover-group.png)

1. どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。
1. 作業ウィンドウで **[フェールオーバー]** を選択し、エラスティック プールを含むフェールオーバー グループをフェールオーバーします。
1. TDS セッションが切断されることが通知される警告で **[はい]** を選択します。

   ![データベースが含まれるフェールオーバー グループをフェールオーバーする](./media/auto-failover-group-configure/failover-sql-db.png)

1. どのサーバーがプライマリで、どのサーバーがセカンダリかを確認します。 フェールオーバーが成功すると、2 つのサーバー ロールがスワップされているはずです。
1. フェールオーバー グループを元の設定に戻すには、 **[フェールオーバー]** をもう一度選択します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

> [!IMPORTANT]
> セカンダリ データベースを削除する必要がある場合は、削除する前にそれをフェールオーバー グループから削除します。 セカンダリ データベースをフェールオーバー グループから削除する前に削除すると、予期しない動作が発生する可能性があります。

## <a name="sql-managed-instance"></a>SQL Managed Instance

Azure portal または PowerShell を使用して、Azure SQL Managed Instance 内の 2 つのマネージド インスタンス間にフェールオーバー グループを作成します。

[ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) を構成するか、あるいは、各 SQL Managed Instance の仮想ネットワーク用のゲートウェイを作成し、2 つのゲートウェイを接続して、フェールオーバー グループを作成する必要があります。 

パフォーマンス上の理由により、両方のマネージド インスタンスを、[ペアになっているリージョン](../../best-practices-availability-paired-regions.md)にデプロイします。 geo のペアになっているリージョンに存在するマネージド インスタンスは、ペアになっていないリージョンと比較すると、パフォーマンスがはるかに優れています。 

### <a name="prerequisites"></a>前提条件

次の前提条件を考慮してください。

- セカンダリ マネージド インスタンスは空である必要があります。
- セカンダリ仮想ネットワークのサブネット範囲は、プライマリ仮想ネットワークのサブネット範囲と重複してはなりません。
- セカンダリ マネージド インスタンスの照合順序とタイムゾーンは、プライマリ マネージド インスタンスと一致している必要があります。
- 2 つのゲートウェイを接続するときは、両方の接続で **[共有キー]** が同じである必要があります。

### <a name="create-primary-virtual-network-gateway"></a>プライマリ仮想ネットワーク ゲートウェイを作成する

[ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) を構成していない場合は、Azure portal または PowerShell を使用して、プライマリ仮想ネットワーク ゲートウェイを作成できます。

> [!NOTE]
> ゲートウェイの SKU は、スループットのパフォーマンスに影響を与えます。 この記事では、最も基本的な SKU (`HwGw1`) を使用して、ゲートウェイをデプロイします。 上位の SKU (`VpnGw3` など) をデプロイして、より高いスループットを実現します。 使用可能なすべてのオプションについては、[ゲートウェイ SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) に関する記事を参照してください。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、プライマリ仮想ネットワーク ゲートウェイを作成します。

1. [Azure portal](https://portal.azure.com) で、リソース グループに移動し、プライマリ マネージド インスタンスの **仮想ネットワーク** リソースを選択します。
1. **[設定]** で **[サブネット]** を選び、新しい **ゲートウェイ サブネット** を追加することを選択します。 既定値のままにします。

   ![プライマリ マネージド インスタンスのゲートウェイを追加する](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. サブネット ゲートウェイが作成されたら、左側のナビゲーション ウィンドウで **[リソースの作成]** を選択し、検索ボックスに「`Virtual network gateway`」と入力します。 **Microsoft** によって公開された **仮想ネットワーク ゲートウェイ** リソースを選択します。

   ![新しい仮想ネットワーク ゲートウェイを作成する](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. プライマリ マネージド インスタンスのゲートウェイを構成するために必要なフィールドに入力します。

   次の表には、プライマリ マネージド インスタンスのゲートウェイに必要な値が示されています。

    | **フィールド** | 値 |
    | --- | --- |
    | **サブスクリプション** |  プライマリ マネージド インスタンスがあるサブスクリプション。 |
    | **名前** | 仮想ネットワーク ゲートウェイの名前。 |
    | **リージョン** | プライマリ マネージド インスタンスがあるリージョン。 |
    | **ゲートウェイの種類** | **[VPN]** を選択します。 |
    | **VPN の種類** | **[ルート ベース]** を選択します |
    | **SKU**| 既定値の `VpnGw1` のままにします。 |
    | **場所**| セカンダリ マネージド インスタンスおよびセカンダリ仮想ネットワークがある場所。   |
    | **Virtual Network**| セカンダリ マネージド インスタンスの仮想ネットワークを選択します。 |
    | **パブリック IP アドレス**| **[新規作成]** を選択します。 |
    | **パブリック IP アドレス名**| IP アドレスの名前を入力します。 |
    | &nbsp; | &nbsp; |

1. その他の値は既定値のままにし、 **[確認と作成]** を選択して仮想ネットワーク ゲートウェイの設定を確認します。

   ![プライマリ ゲートウェイの設定](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. **[作成]** を選択して、新しい仮想ネットワーク ゲートウェイを作成します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="portal"></a>[ポータル](#tab/azure-portal)

前のセクションの手順を繰り返し、セカンダリ マネージド インスタンスの仮想ネットワーク サブネットとゲートウェイを作成します。 セカンダリ マネージド インスタンスのゲートウェイを構成するために必要なフィールドに入力します。

次の表には、セカンダリ マネージド インスタンスのゲートウェイに必要な値が示されています。

   | **フィールド** | 値 |
   | --- | --- |
   | **サブスクリプション** |  セカンダリ マネージド インスタンスがあるサブスクリプション。 |
   | **名前** | 仮想ネットワーク ゲートウェイの名前 (`secondary-mi-gateway` など)。 |
   | **リージョン** | セカンダリ マネージド インスタンスがあるリージョン。 |
   | **ゲートウェイの種類** | **[VPN]** を選択します。 |
   | **VPN の種類** | **[ルート ベース]** を選択します |
   | **SKU**| 既定値の `VpnGw1` のままにします。 |
   | **場所**| セカンダリ マネージド インスタンスおよびセカンダリ仮想ネットワークがある場所。   |
   | **Virtual Network**| セクション 2 で作成された仮想ネットワーク (`vnet-sql-mi-secondary` など) を選択します。 |
   | **パブリック IP アドレス**| **[新規作成]** を選択します。 |
   | **パブリック IP アドレス名**| IP アドレスの名前 (`secondary-gateway-IP` など) を入力します。 |
   | &nbsp; | &nbsp; |

   ![セカンダリ ゲートウェイの設定](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、2 つのゲートウェイ間の接続を作成します。

1. [Azure portal](https://portal.azure.com) から **[リソースの作成]** を選択します。
1. 検索ボックスに「`connection`」と入力し、Enter キーを押して検索します。それにより、Microsoft によって発行された **接続** リソースに移動します。
1. **[作成]** を選択して接続を作成します。
1. **[基本]** タブで次の値を選択し、 **[OK]** を選択します。
    1. **[接続の種類]** に対して `VNet-to-VNet` を選択します。
    1. ドロップダウン リストからサブスクリプションを選択します。
    1. ドロップダウンで、マネージド インスタンスのリソース グループを選択します。
    1. ドロップダウンからプライマリ マネージド インスタンスの場所を選択します。
1. **[設定]** タブで次の値を選択または入力し、 **[OK]** を選択します。
    1. **[最初の仮想ネットワーク ゲートウェイ]** のプライマリ ネットワーク ゲートウェイを選択します (`Primary-Gateway` など)。  
    1. **[2 番目の仮想ネットワーク ゲートウェイ]** のセカンダリ ネットワーク ゲートウェイを選択します (`Secondary-Gateway` など)。
    1. **[双方向接続の確立]** の横にあるチェック ボックスをオンにします。
    1. 既定のプライマリ接続名をそのまま使用するか、名前を任意の値に変更します。
    1. 接続の **共有キー (PSK)** を指定します (`mi1m2psk` など)。

   ![ゲートウェイ接続を作成する](./media/auto-failover-group-configure/create-gateway-connection.png)

1. **[概要]** タブで双方向接続の設定を確認し、 **[OK]** を選択して接続を作成します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、SQL Managed Instance のフェールオーバー グループを作成します。

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「Azure SQL」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。
1. フェールオーバー グループに追加するプライマリ マネージド インスタンスを選択します。  
1. **[設定]** で **[インスタンスのフェールオーバー グループ]** に移動し、 **[グループの追加]** を選択して **[インスタンスのフェールオーバー グループ]** ページを開きます。

   ![フェールオーバー グループを追加する](./media/auto-failover-group-configure/add-failover-group.png)

1. **[インスタンスのフェールオーバー グループ]** ページで、フェールオーバー グループの名前を入力し、ドロップダウンからセカンダリ マネージド インスタンスを選択します。 **[作成]** を選択して、フェールオーバー グループを作成します。

   ![フェールオーバー グループの作成](./media/auto-failover-group-configure/create-failover-group.png)

1. フェールオーバー グループのデプロイが完了すると、 **[フェールオーバー グループ]** ページに戻ります。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、フェールオーバー グループのフェールオーバーをテストします。

1. [Azure portal](https://portal.azure.com) 内の _セカンダリ_ マネージド インスタンスに移動し、[設定] で **[インスタンスのフェールオーバー グループ]** を選択します。
1. どのマネージド インスタンスがプライマリであり、どのマネージド インスタンスがセカンダリであるかを確認します。
1. **[フェールオーバー]** を選択し、切断中の TDS セッションに関する警告で **[はい]** を選択します。

   ![フェールオーバー グループをフェールオーバーする](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. どのマネージド インスタンスがプライマリであり、どのインスタンスがセカンダリであるかを確認します。 フェールオーバーが成功した場合は、2 つのインスタンスのロールが切り替わっているはずです。

   ![フェールオーバー後にマネージド インスタンスのロールが切り替わっている](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. 新しい "_セカンダリ_" マネージド インスタンスに移動し、もう一度 **[フェールオーバー]** を選択して、プライマリ インスタンスをプライマリの役割にフェールバックします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

## <a name="use-private-link"></a>Private Link を使用する

Private Link を使用すると、論理サーバーを、仮想ネットワークとサブネット内の特定のプライベート IP アドレスに関連付けることができます。 

フェールオーバー グループで Private Link を使用するには、次の手順を実行します。

1. プライマリ サーバーとセカンダリ サーバーが、[ペアになっているリージョン](../../best-practices-availability-paired-regions.md)内にあることを確認します。 
1. 各リージョンに仮想ネットワークとサブネットを作成して、プライマリ サーバーとセカンダリ サーバーのプライベート エンドポイントをホストします。これにより、重複しない IP アドレス空間が存在するようになります。 たとえば、10.0.0.0/16 のプライマリ仮想ネットワーク アドレス範囲と、10.0.0.1/16 のセカンダリ仮想ネットワーク アドレス範囲は重複しています。 仮想ネットワーク アドレス範囲の詳細については、[Azure 仮想ネットワークの設計](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/)に関するブログを参照してください。
1. [プライマリ サーバーのプライベート エンドポイントと Azure プライベート DNS ゾーン](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint)を作成します。 
1. セカンダリ サーバーのプライベート エンドポイントも作成しますが、今回は、プライマリ サーバー用に作成されたプライベート DNS ゾーンと同じものを再利用することを選択します。 
1. Private Link が確立されたら、この記事で概要を説明した手順に従って、フェールオーバー グループを作成できます。 


## <a name="locate-listener-endpoint"></a>リスナー エンドポイントを特定する

フェールオーバー グループを構成したら、アプリケーションのリスナー エンドポイントへの接続文字列を更新します。 これで、アプリケーションから、プライマリ データベース、エラスティック プール、またはインスタンス データベースではなく、フェールオーバー グループ リスナーへの接続が維持されます。 このようにすると、データベース エンティティがフェールオーバーするたびに接続文字列を手動で更新する必要がありません。また、トラフィックは、その時点でプライマリであるエンティティにルーティングされます。

リスナー エンドポイントは `fog-name.database.windows.net` という形式であり、Azure portal でフェールオーバー グループを表示すると確認できます。

![フェールオーバー グループの接続文字列](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>解説

- 単一データベースまたはプールされたデータベースのフェールオーバー グループを削除しても、レプリケーションは停止されず、レプリケートされたデータベースは削除されません。 単一データベースまたはプールされたデータベースを削除した後にフェールオーバー グループに追加する場合は、geo レプリケーションを手動で停止し、セカンダリ サーバーからデータベースを削除する必要があります。 いずれかの操作を行わないと、データベースをフェールオーバー グループに追加しようとしたときに `The operation cannot be performed due to multiple errors` のようなエラーが発生する可能性があります。

## <a name="next-steps"></a>次のステップ

フェールオーバー グループの構成手順の詳細については、次のチュートリアルを参照してください。

- [フェールオーバー グループに単一データベースを追加する](failover-group-add-single-database-tutorial.md)
- [フェールオーバー グループにエラスティック プールを追加する](failover-group-add-elastic-pool-tutorial.md)
- [マネージド インスタンスをフェールオーバー グループに追加する](../managed-instance/failover-group-add-instance-tutorial.md)

Azure SQL Database の高可用性オプションの概要については、[geo レプリケーション](active-geo-replication-overview.md)と[自動フェールオーバー グループ](auto-failover-group-overview.md)に関する記事を参照してください。