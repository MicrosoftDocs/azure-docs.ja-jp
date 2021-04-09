---
title: チュートリアル:フェールオーバー グループに SQL Managed Instance を追加する
titleSuffix: Azure SQL Managed Instance
description: このチュートリアルでは、プライマリとセカンダリの Azure SQL Managed Instance の間にフェールオーバー グループを作成する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: sashan
ms.date: 08/27/2019
ms.openlocfilehash: 1609f188af8ffb58251edc806e19f7820a6b0869
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99525723"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>チュートリアル:フェールオーバー グループに SQL Managed Instance を追加する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance のマネージド インスタンスをフェールオーバー グループに追加します。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> - プライマリ マネージド インスタンスを作成する。
> - [フェールオーバー グループ](../database/auto-failover-group-overview.md)の一部として、セカンダリ マネージド インスタンスを作成する。 
> - フェールオーバーをテストする。

  > [!NOTE]
  > - このチュートリアルを実行するときは、[SQL Managed Instance のフェールオーバー グループを設定するための前提条件](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets)を使用してリソースを構成していることを確認してください。 
  > - マネージド インスタンスの作成にはかなりの時間がかかることがあります。 そのため、このチュートリアルの完了には数時間かかることがあります。 プロビジョニング時間の詳細については、「[SQL マネージド インスタンスの管理操作](sql-managed-instance-paas-overview.md#management-operations)」を参照してください。 
  > - フェールオーバー グループに参加するマネージド インスタンスには、[Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)、グローバル VNet ピアリング、または接続された 2 つの VPN ゲートウェイが必要です。 このチュートリアルでは、VPN ゲートウェイを作成して接続する手順を示します。 既に ExpressRoute が構成されている場合は、これらの手順をスキップします。 


## <a name="prerequisites"></a>前提条件

# <a name="portal"></a>[ポータル](#tab/azure-portal)
このチュートリアルを完了するには、以下のものが必要です。 

- Azure サブスクリプション。 [無料のアカウントを作成](https://azure.microsoft.com/free/)します (まだお持ちでない場合)。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
このチュートリアルを完了するには、次のものが必要です。

- Azure サブスクリプション。 [無料のアカウントを作成](https://azure.microsoft.com/free/)します (まだお持ちでない場合)。
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>リソース グループとプライマリ マネージド インスタンスを作成する

この手順では、Azure portal または PowerShell を使用して、リソース グループと、フェールオーバー グループのプライマリ マネージド インスタンスを作成します。 

パフォーマンス上の理由により、両方のマネージド インスタンスを、[ペアになっているリージョン](../../best-practices-availability-paired-regions.md)にデプロイします。 geo のペアになっているリージョンに存在するマネージド インスタンスは、ペアになっていないリージョンと比較すると、パフォーマンスがはるかに優れています。 


# <a name="portal"></a>[ポータル](#tab/azure-portal) 

Azure portal を使用して、リソース グループとプライマリ マネージド インスタンスを作成します。 

1. Azure portal の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「`Azure SQL`」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。 
1. **[+ 追加]** を選択して、 **[Select SQL deployment option]\(SQL デプロイ オプションの選択\)** ページを開きます。 **[データベース]** タイルで **[詳細の表示]** を選択すると、さまざまなデータベースに関する追加情報を表示できます。
1. **[SQL マネージド インスタンス]** タイルで **[作成]** を選択します。 

    ![SQL マネージド インスタンスを選択する](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. **[Create Azure SQL Managed Instance]\(Azure SQL Managed Instance の作成\)** ページの **[基本]** タブで、以下を実行します。
    1. **[プロジェクトの詳細]** で、ドロップダウンから自分の **サブスクリプション** を選び、リソース グループを **新規作成** することを選択します。 「`myResourceGroup`」など、リソース グループの名前を入力します。 
    1. **[SQL Managed Instance Details]\(SQL マネージド インスタンスの詳細\)** で、マネージド インスタンスの名前と、マネージド インスタンスをデプロイするリージョンを指定します。 **[コンピューティングとストレージ]** は既定値のままにしておきます。 
    1. **[管理者アカウント]** で、`azureuser` などの管理者ログインと、複雑な管理者パスワードを指定します。 

    ![プライマリ マネージド インスタンスを作成する](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. 残りの設定は既定値のままにし、 **[確認と作成]** を選択して SQL マネージド インスタンスの設定を確認します。 
1. **[作成]** を選択して、プライマリ マネージド インスタンスを作成します。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して、リソース グループとプライマリ マネージド インスタンスを作成します。 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Azure リソース グループを作成します。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 仮想ネットワークにサブネット構成を追加します。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | リソース グループ内の仮想ネットワークを取得します。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 仮想ネットワーク内のサブネットを取得します。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | ネットワーク セキュリティ グループを作成します。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | ルート テーブルを作成します。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 仮想ネットワークのサブネット構成を更新します。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 仮想ネットワークを更新します。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | ネットワーク セキュリティ グループを取得します。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| ネットワーク セキュリティ グループにネットワーク セキュリティ ルールの構成を追加します。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | ネットワーク セキュリティ グループを更新します。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | ルート テーブルにルートを追加します。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | ルート テーブルを更新します。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | マネージド インスタンスを作成します。  |

---

## <a name="create-secondary-virtual-network"></a>セカンダリ仮想ネットワークを作成する

Azure portal を使用してマネージド インスタンスを作成する場合は、仮想ネットワークを別個に作成する必要があります。これは、プライマリとセカンダリのマネージド インスタンスのサブネットで範囲が重複しないことという要件があるためです。 PowerShell を使用してマネージド インスタンスを構成する場合は、手順 3 に進んでください。 

# <a name="portal"></a>[ポータル](#tab/azure-portal) 

プライマリ仮想ネットワークのサブネット範囲を確認するには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com) で、リソース グループに移動し、プライマリ インスタンスの仮想ネットワークを選択します。  
2. **[設定]** で **[サブネット]** を選択し、**アドレス範囲** を書き留めます。 セカンダリ マネージド インスタンス用の仮想ネットワークのサブネット アドレス範囲は、これと重複することはできません。 


   ![プライマリ サブネット](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

仮想ネットワークを作成するには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]** を選択し、*仮想ネットワーク* を検索します。 
1. Microsoft によって公開された **仮想ネットワーク** オプションを選び、次のページで **[作成]** を選択します。 
1. セカンダリ マネージド インスタンスの仮想ネットワークを構成するために必要なフィールドに入力し、 **[作成]** を選択します。 

   次の表には、セカンダリ仮想ネットワークに必要な値が示されています。

    | **フィールド** | 値 |
    | --- | --- |
    | **名前** |  セカンダリ マネージド インスタンスで使用される仮想ネットワークの名前 (`vnet-sql-mi-secondary` など)。 |
    | **アドレス空間** | 仮想ネットワークのアドレス空間 (`10.128.0.0/16` など)。 | 
    | **サブスクリプション** | プライマリ マネージド インスタンスとリソース グループが存在するサブスクリプション。 |
    | **リージョン** | セカンダリ マネージド インスタンスをデプロイする場所。 |
    | **サブネット** | サブネットの名前。 `default` は既定で自動的に指定されます。 |
    | **アドレス範囲**| サブネットのアドレス範囲。 これは、`10.128.0.0/24` など、プライマリ マネージド インスタンスの仮想ネットワークで使用されるサブネット アドレス範囲とは異なる必要があります。  |
    | &nbsp; | &nbsp; |

    ![セカンダリ仮想ネットワークの値](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

この手順は、Azure portal を使用して SQL マネージド インスタンスをデプロイする場合にのみ必要です。 PowerShell を使用している場合は、手順 3 に進んでください。 

---

## <a name="create-a-secondary-managed-instance"></a>セカンダリ マネージド インスタンスを作成する
この手順では、Azure portal でセカンダリ マネージド インスタンスを作成します。これにより、2 つのマネージド インスタンス間のネットワークも構成されます。 

2 つ目のマネージド インスタンスは、次のようなものである必要があります。
- 空である。 
- プライマリ マネージド インスタンスとは異なるサブネットと IP 範囲がある。 

# <a name="portal"></a>[ポータル](#tab/azure-portal) 

Azure portal を使用してセカンダリ マネージド インスタンスを作成します。 

1. Azure portal の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「`Azure SQL`」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。 
1. **[+ 追加]** を選択して、 **[Select SQL deployment option]\(SQL デプロイ オプションの選択\)** ページを開きます。 **[データベース]** タイルで **[詳細の表示]** を選択すると、さまざまなデータベースに関する追加情報を表示できます。
1. **[SQL マネージド インスタンス]** タイルで **[作成]** を選択します。 

    ![SQL マネージド インスタンスを選択する](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. **[Create Azure SQL Managed Instance]\(Azure SQL Managed Instance の作成\)** ページの **[基本]** タブで、セカンダリ マネージド インスタンスを構成するために必要なフィールドに入力します。 

   次の表には、セカンダリ マネージド インスタンスに必要な値が示されています。
 
    | **フィールド** | 値 |
    | --- | --- |
    | **サブスクリプション** |  プライマリ マネージド インスタンスがあるサブスクリプション。 |
    | **リソース グループ**| プライマリ マネージド インスタンスがあるリソース グループ。 |
    | **SQL マネージド インスタンス名** | 新しいセカンダリ マネージド インスタンスの名前 (`sql-mi-secondary` など)  | 
    | **リージョン**| セカンダリ マネージド インスタンスの場所。  |
    | **SQL マネージド インスタンス管理者ログイン** | 新しいセカンダリ マネージド インスタンスに使用するログイン (`azureuser` など)。 |
    | **パスワード** | 新しいセカンダリ マネージド インスタンス用の管理者ログインで使用される複雑なパスワード。  |
    | &nbsp; | &nbsp; |

1. **[ネットワーク]** タブの **[仮想ネットワーク]** で、ドロップダウンからセカンダリ マネージド インスタンス用に作成した仮想ネットワークを選択します。

   ![セカンダリ MI ネットワーク](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. **[追加設定]** タブの **[geo レプリケーション]** で、 _[フェールオーバー セカンダリとして使用する]_ に **[はい]** を選択します。 ドロップダウンからプライマリ マネージド インスタンスを選択します。 
    
   照合順序とタイム ゾーンがプライマリ マネージド インスタンスのものと一致していることを確認します。 このチュートリアルで作成されたプライマリ マネージド インスタンスでは、既定値の `SQL_Latin1_General_CP1_CI_AS` 照合順序および `(UTC) Coordinated Universal Time` タイム ゾーンが使用されていました。 

   ![セカンダリ マネージド インスタンスのネットワーク](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. **[確認と作成]** を選択して、セカンダリ マネージド インスタンスの設定を確認します。 
1. **[作成]** を選択して、セカンダリ マネージド インスタンスを作成します。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してセカンダリ マネージド インスタンスを作成します。 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Azure リソース グループを作成します。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 仮想ネットワークにサブネット構成を追加します。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | リソース グループ内の仮想ネットワークを取得します。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 仮想ネットワーク内のサブネットを取得します。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | ネットワーク セキュリティ グループを作成します。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | ルート テーブルを作成します。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 仮想ネットワークのサブネット構成を更新します。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 仮想ネットワークを更新します。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | ネットワーク セキュリティ グループを取得します。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| ネットワーク セキュリティ グループにネットワーク セキュリティ ルールの構成を追加します。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | ネットワーク セキュリティ グループを更新します。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | ルート テーブルにルートを追加します。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | ルート テーブルを更新します。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | マネージド インスタンスを作成します。  |

---

## <a name="create-a-primary-gateway"></a>プライマリ ゲートウェイを作成する 

2 つのマネージド インスタンスをフェールオーバー グループに参加させるには、2 つのマネージド インスタンスの仮想ネットワークの間に、ネットワーク通信が可能になるように ExpressRoute またはゲートウェイが構成されている必要があります。 2 つの VPN ゲートウェイを接続するのではなく [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) を構成する場合は、[手順 7](#create-a-failover-group) に進んでください。  

この記事では、2 つの VPN ゲートウェイを作成して接続する手順について説明しますが、代わりに ExpressRoute を構成した場合は、フェールオーバー グループの作成に進むことができます。 

> [!NOTE]
> ゲートウェイの SKU は、スループットのパフォーマンスに影響を与えます。 このチュートリアルでは、最も基本的な SKU (`HwGw1`) を使用して、ゲートウェイをデプロイします。 上位の SKU (`VpnGw3` など) をデプロイして、より高いスループットを実現します。 使用可能なすべてのオプションについては、[ゲートウェイ SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) に関する記事を参照してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、プライマリ マネージド インスタンスの仮想ネットワークのゲートウェイを作成します。 


1. [Azure portal](https://portal.azure.com) で、リソース グループに移動し、プライマリ マネージド インスタンスの **仮想ネットワーク** リソースを選択します。 
1. **[設定]** で **[サブネット]** を選び、新しい **ゲートウェイ サブネット** を追加することを選択します。 既定値のままにします。 

   ![プライマリ マネージド インスタンスのゲートウェイを追加する](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. サブネット ゲートウェイが作成されたら、左側のナビゲーション ウィンドウで **[リソースの作成]** を選択し、検索ボックスに「`Virtual network gateway`」と入力します。 **Microsoft** によって公開された **仮想ネットワーク ゲートウェイ** リソースを選択します。 

   ![新しい仮想ネットワーク ゲートウェイを作成する](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. プライマリ マネージド インスタンスのゲートウェイを構成するために必要なフィールドに入力します。 

   次の表には、プライマリ マネージド インスタンスのゲートウェイに必要な値が示されています。
 
    | **フィールド** | 値 |
    | --- | --- |
    | **サブスクリプション** |  プライマリ マネージド インスタンスがあるサブスクリプション。 |
    | **名前** | 仮想ネットワーク ゲートウェイの名前 (`primary-mi-gateway` など)。 | 
    | **リージョン** | プライマリ マネージド インスタンスがあるリージョン。 |
    | **ゲートウェイの種類** | **[VPN]** を選択します。 |
    | **VPN の種類** | **[ルート ベース]** を選択します。 |
    | **SKU**| 既定値の `VpnGw1` のままにします。 |
    | **Virtual Network**| セクション 2 で作成された仮想ネットワーク (`vnet-sql-mi-primary` など) を選択します。 |
    | **パブリック IP アドレス**| **[新規作成]** を選択します。 |
    | **パブリック IP アドレス名**| IP アドレスの名前 (`primary-gateway-IP` など) を入力します。 |
    | &nbsp; | &nbsp; |

1. その他の値は既定値のままにし、 **[確認と作成]** を選択して仮想ネットワーク ゲートウェイの設定を確認します。

   ![プライマリ ゲートウェイの設定](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. **[作成]** を選択して、新しい仮想ネットワーク ゲートウェイを作成します。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して、プライマリ マネージド インスタンスの仮想ネットワークのゲートウェイを作成します。 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | リソース グループ内の仮想ネットワークを取得します。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 仮想ネットワークにサブネット構成を追加します。 | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 仮想ネットワークを更新します。  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 仮想ネットワーク内のサブネットを取得します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | パブリック IP アドレスを作成します。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 仮想ネットワーク ゲートウェイの IP 構成を作成します。 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 仮想ネットワーク ゲートウェイを作成します。 |


---


## <a name="create-secondary-gateway"></a>セカンダリ ゲートウェイを作成する 
この手順では、Azure portal を使用して、セカンダリ マネージド インスタンスの仮想ネットワークのゲートウェイを作成します。 


# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、前のセクションの手順を繰り返し、セカンダリ マネージド インスタンスの仮想ネットワークのサブネットとゲートウェイを作成します。 セカンダリ マネージド インスタンスのゲートウェイを構成するために必要なフィールドに入力します。 

   次の表には、セカンダリ マネージド インスタンスのゲートウェイに必要な値が示されています。

   | **フィールド** | 値 |
   | --- | --- |
   | **サブスクリプション** |  セカンダリ マネージド インスタンスがあるサブスクリプション。 |
   | **名前** | 仮想ネットワーク ゲートウェイの名前 (`secondary-mi-gateway` など)。 | 
   | **リージョン** | セカンダリ マネージド インスタンスがあるリージョン。 |
   | **ゲートウェイの種類** | **[VPN]** を選択します。 |
   | **VPN の種類** | **[ルート ベース]** を選択します。 |
   | **SKU**| 既定値の `VpnGw1` のままにします。 |
   | **Virtual Network**| セカンダリ マネージド インスタンスに、`vnet-sql-mi-secondary` などの仮想ネットワークを選択します。 |
   | **パブリック IP アドレス**| **[新規作成]** を選択します。 |
   | **パブリック IP アドレス名**| IP アドレスの名前 (`secondary-gateway-IP` など) を入力します。 |
   | &nbsp; | &nbsp; |

   ![セカンダリ ゲートウェイの設定](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して、セカンダリ マネージド インスタンスの仮想ネットワークのゲートウェイを作成します。 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating secondary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | リソース グループ内の仮想ネットワークを取得します。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 仮想ネットワークにサブネット構成を追加します。 | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 仮想ネットワークを更新します。  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 仮想ネットワーク内のサブネットを取得します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | パブリック IP アドレスを作成します。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 仮想ネットワーク ゲートウェイの IP 構成を作成します。 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 仮想ネットワーク ゲートウェイを作成します。 |

---


## <a name="connect-the-gateways"></a>ゲートウェイを接続する
この手順では、2 つの仮想ネットワークの 2 つのゲートウェイ間に双方向接続を作成します。 


# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して、2 つのゲートウェイを接続します。 


1. [Azure portal](https://portal.azure.com) から **[リソースの作成]** を選択します。
1. 検索ボックスに「`connection`」と入力し、Enter キーを押して検索します。それにより、Microsoft によって発行された **接続** リソースに移動します。
1. **[作成]** を選択して接続を作成します。 
1. **[基本]** ページで次の値を選択し、 **[OK]** を選択します。 
    1. **[接続の種類]** に対して `VNet-to-VNet` を選択します。 
    1. ドロップダウン リストからサブスクリプションを選択します。 
    1. ドロップダウンで、SQL マネージド インスタンスのリソース グループを選択します。 
    1. ドロップダウンからプライマリ マネージド インスタンスの場所を選択します。 
1. **[設定]** ページで次の値を選択または入力し、 **[OK]** を選択します。
    1. **[最初の仮想ネットワーク ゲートウェイ]** のプライマリ ネットワーク ゲートウェイを選択します (`primaryGateway` など)。  
    1. **[2 番目の仮想ネットワーク ゲートウェイ]** のセカンダリ ネットワーク ゲートウェイを選択します (`secondaryGateway` など)。 
    1. **[双方向接続の確立]** の横にあるチェック ボックスをオンにします。 
    1. 既定のプライマリ接続名をそのまま使用するか、名前を任意の値に変更します。 
    1. 接続の **共有キー (PSK)** を指定します (`mi1m2psk` など)。 
    1. **[OK]** を選択して設定を保存します。 

    ![ゲートウェイ接続を作成する](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. **[確認と作成]** ページでお使いの双方向接続の設定を確認し、 **[OK]** を選択して使用する自分の接続を作成します。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して、2 つのゲートウェイを接続します。 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 2 つの仮想ネットワーク ゲートウェイ間の接続を作成します。   |

---


## <a name="create-a-failover-group"></a>フェールオーバー グループを作成する
この手順では、フェールオーバー グループを作成し、それに両方のマネージド インスタンスを追加します。 


# <a name="portal"></a>[ポータル](#tab/azure-portal)
Azure portal を使用して、フェールオーバー グループを作成します。 


1. [Azure portal](https://portal.azure.com) の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択し、検索ボックスに「`Azure SQL`」と入力します。 (省略可能) **[Azure SQL]** の横にある星を選択してお気に入りに追加し、左側のナビゲーションに項目として追加します。 
1. 最初のセクションで作成したプライマリ マネージド インスタンス (`sql-mi-primary` など) を選択します。 
1. **[設定]** で **[インスタンスのフェールオーバー グループ]** に移動し、 **[グループの追加]** を選択して **[インスタンスのフェールオーバー グループ]** ページを開きます。 

   ![フェールオーバー グループを追加する](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. **[インスタンスのフェールオーバー グループ]** ページで、フェールオーバー グループの名前 (`failovergrouptutorial` など) を入力します。 次に、ドロップダウンからセカンダリ マネージド インスタンス (`sql-mi-secondary` など) を選択します。 **[作成]** を選択して、フェールオーバー グループを作成します。 

   ![フェールオーバー グループの作成](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. フェールオーバー グループのデプロイが完了すると、 **[フェールオーバー グループ]** ページに戻ります。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell を使用して、フェールオーバー グループを作成します。 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Azure SQL Managed Instance の新しいフェールオーバー グループを作成します。  |


---


## <a name="test-failover"></a>[テスト フェールオーバー]
この手順では、フェールオーバー グループをセカンダリ サーバーにフェールオーバーしてから、Azure portal を使用してフェールバックします。 


# <a name="portal"></a>[ポータル](#tab/azure-portal)
Azure portal を使用してフェールオーバーをテストします。 


1. [Azure portal](https://portal.azure.com)内の _セカンダリ_ マネージド インスタンスに移動し、[設定] で **[インスタンスのフェールオーバー グループ]** を選択します。 
1. どのマネージド インスタンスがプライマリであり、どのマネージド インスタンスがセカンダリであるかを確認します。 
1. **[フェールオーバー]** を選択し、切断中の TDS セッションに関する警告で **[はい]** を選択します。 

   ![フェールオーバー グループをフェールオーバーする](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. どのマネージド インスタンスがプライマリであり、どのマネージド インスタンスがセカンダリであるかを確認します。 フェールオーバーが成功した場合は、2 つのインスタンスのロールが切り替わっているはずです。 

   ![フェールオーバー後にマネージド インスタンスのロールが切り替わっている](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. 新しい "_セカンダリ_" マネージド インスタンスに移動し、もう一度 **[フェールオーバー]** を選択して、プライマリ インスタンスをプライマリの役割にフェールバックします。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell を使用してフェールオーバーをテストします。 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


フェールオーバー グループを再びプライマリ サーバーに戻します。

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | SQL Managed Instance のフェールオーバー グループを取得または一覧表示します。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | SQL マネージド インスタンスのフェールオーバー グループのフェールオーバーを実行します。 | 

---



## <a name="clean-up-resources"></a>リソースをクリーンアップする
リソースをクリーンアップするには、まず、マネージド インスタンス、次に仮想クラスターを削除します。その後、残りのリソース、最後にリソース グループを削除します。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com) で、リソース グループに移動します。 
1. マネージド インスタンスを選び、 **[削除]** を選択します。 テキスト ボックスに「`yes`」と入力して、リソースを削除することを確認し、 **[削除]** を選択します。 このプロセスは、バックグラウンドで完了するまでに時間がかかる場合があります。完了するまでは、"*仮想クラスター*" やその他の依存リソースを削除することはできません。 **[アクティビティ]** タブで削除を監視して、マネージド インスタンスが削除されたことを確認します。 
1. マネージド インスタンスが削除されたら、"*仮想クラスター*" を削除します。そのためには、リソース グループでそれを選び、 **[削除]** を選択します。 テキスト ボックスに「`yes`」と入力して、リソースを削除することを確認し、 **[削除]** を選択します。 
1. 残りのリソースを削除します。 テキスト ボックスに「`yes`」と入力して、リソースを削除することを確認し、 **[削除]** を選択します。 
1. リソース グループを削除するには、 **[リソース グループの削除]** を選択し、リソース グループの名前 (`myResourceGroup`) を入力して、 **[削除]** を選びます。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

リソース グループは 2 回削除する必要があります。 最初にリソース グループを削除すると、マネージド インスタンスと仮想クラスターが削除されますが、その後、エラー メッセージ `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` が表示されて失敗します。 Remove-AzResourceGroup コマンドをもう一度実行すると、残りのすべてのリソースと、リソース グループが削除されます。

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

チュートリアルのこの部分では、次の PowerShell コマンドレットを使用します。

| コマンド | Notes |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループを削除します。 |

---

## <a name="full-script"></a>完全なスクリプト

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Azure リソース グループを作成します。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 仮想ネットワークを作成します。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 仮想ネットワークにサブネット構成を追加します。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | リソース グループ内の仮想ネットワークを取得します。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 仮想ネットワーク内のサブネットを取得します。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | ネットワーク セキュリティ グループを作成します。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | ルート テーブルを作成します。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 仮想ネットワークのサブネット構成を更新します。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 仮想ネットワークを更新します。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | ネットワーク セキュリティ グループを取得します。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| ネットワーク セキュリティ グループにネットワーク セキュリティ ルールの構成を追加します。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | ネットワーク セキュリティ グループを更新します。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | ルート テーブルにルートを追加します。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | ルート テーブルを更新します。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | マネージド インスタンスを作成します。  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Azure SQL Managed Instance に関する情報を返します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | パブリック IP アドレスを作成します。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 仮想ネットワーク ゲートウェイの IP 構成を作成します。 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 仮想ネットワーク ゲートウェイを作成します。 |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 2 つの仮想ネットワーク ゲートウェイ間の接続を作成します。   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| SQL Managed Instance の新しいフェールオーバー グループを作成します。  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | SQL Managed Instance のフェールオーバー グループを取得または一覧表示します。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | SQL Managed Instance のフェールオーバー グループのフェールオーバーを実行します。 | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループを削除します。 | 

# <a name="portal"></a>[ポータル](#tab/azure-portal) 

Azure portal に使用できるスクリプトはありません。

---

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、2 つのマネージド インスタンスの間にフェールオーバー グループを構成しました。 以下の方法を学習しました。

> [!div class="checklist"]
> - プライマリ マネージド インスタンスを作成する。
> - [フェールオーバー グループ](../database/auto-failover-group-overview.md)の一部として、セカンダリ マネージド インスタンスを作成する。 
> - フェールオーバーをテストする。

SQL マネージド インスタンスに接続する方法と、SQL マネージド インスタンスにデータベースを復元する方法に関する次のクイックスタートに進みます。 

> [!div class="nextstepaction"]
> [SQL マネージド インスタンスに接続する](connect-vm-instance-configure.md)
> [データベースを SQL マネージド インスタンスに復元する](restore-sample-database-quickstart.md)


