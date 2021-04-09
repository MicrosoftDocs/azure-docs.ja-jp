---
title: クイック スタート - Azure PowerShell を使用して Azure プライベート エンドポイントを作成する
description: このクイックスタートを使用して、Azure PowerShell を使用してプライベート エンドポイントを作成する方法について学習します。
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/02/2020
ms.author: allensu
ms.openlocfilehash: afd1f8b6c80cfd7aa8d5142841458d76511a9e96
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562913"
---
# <a name="quickstart-create-an-azure-private-endpoint-using-azure-powershell"></a>クイックスタート: Azure PowerShell を使用して Azure プライベート エンドポイントを作成する

プライベート エンドポイントを使用して Azure Web アプリに安全に接続することにより、Azure Private Link の使用を開始します。

このクイックスタートでは、Azure Web アプリのプライベート エンドポイントを作成し、仮想マシンをデプロイしてプライベート接続をテストします。  

プライベート エンドポイントは、Azure SQL や Azure Storage などのさまざまな種類の Azure サービスに対して作成できます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 対象の Azure サブスクリプションにデプロイされている **PremiumV2 レベル** 以上のアプリ サービス プランを持つ Azure Web アプリ。  
    * 詳細および例については、「[クイックスタート: Azure に ASP.NET Core Web アプリを作成する](../app-service/quickstart-dotnetcore.md)」を参照してください。 
    * Web アプリとエンドポイントの作成に関する詳細なチュートリアルについては「[チュートリアル: Azure プライベート エンドポイントを使用して Web アプリに接続する](tutorial-private-endpoint-webapp-portal.md)」を参照してください。

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して、次のようにリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>仮想ネットワークと bastion ホストの作成

このセクションでは、仮想ネットワーク、サブネット、bastion ホストを作成します。 

bastion ホストは、プライベート エンドポイントをテストする目的で、仮想マシンに安全に接続するために使用されます。

次を使用して仮想ネットワークと bastion ホストを作成します。

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
```

Azure Bastion ホストがデプロイされるまでに数分かかる場合があります。

## <a name="create-test-virtual-machine"></a>テスト用の仮想マシンを作成する

このセクションでは、プライベート エンドポイントのテストに使用する仮想マシンを作成します。

次のコマンドを使用して、仮想マシンを作成します。

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-private-endpoint"></a>プライベート エンドポイントの作成

このセクションでは、次を使用してプライベート エンドポイントと接続を作成します。

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place web app into variable. Replace <webapp-resource-group-name> with the resource group of your webapp. ##
## Replace <your-webapp-name> with your webapp name ##
$webapp = Get-AzWebApp -ResourceGroupName <webapp-resource-group-name> -Name <your-webapp-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $webapp.ID
    GroupID = 'sites'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する

このセクションでは、次を使用してプライベート DNS ゾーンを作成し、構成します。

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'privatelink.azurewebsites.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    ZoneName = 'privatelink.azurewebsites.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.azurewebsites.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
```

## <a name="test-connectivity-to-private-endpoint"></a>プライベート エンドポイントへの接続のテスト

このセクションでは、前の手順で作成した仮想マシンを使用し、プライベート エンドポイントを通じて SQL サーバーに接続します。

1. [Azure ポータル](https://portal.azure.com) 
 
2. 左側のナビゲーション ペインで **[リソース グループ]** を選択します。

3. **[CreatePrivateEndpointQS-rg]** を選択します。

4. **[myVM]** を選択します。

5. **myVM** の [概要] ページで **[接続]** 、 **[Bastion]** の順に選択します。

6. 青色の **[Bastion を使用する]** ボタンを選択します。

7. 仮想マシンの作成時に入力したユーザー名とパスワードを入力します。

8. 接続後にサーバーで Windows PowerShell を開きます。

9. 「`nslookup <your-webapp-name>.azurewebsites.net`」と入力します。 **\<your-webapp-name>** を、前の手順で作成した Web アプリの名前に置き換えます。  以下に表示されるようなメッセージが返されます。

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Web アプリ名に対応する **10.0.0.5** というプライベート IP アドレスが返されます。  このアドレスは、先ほど作成した仮想ネットワークのサブネット内に存在します。

10. **myVM** への bastion 接続で、Internet Explorer を開きます。

11. Web アプリの URL **https://\<your-webapp-name>.azurewebsites.net** を入力します。

12. アプリケーションがデプロイされていない場合は、既定の Web アプリ ページが表示されます。

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="既定の Web アプリ ページ。" border="true":::

13. **myVM** への接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする 
プライベート エンドポイントと VM を使いおわったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとそのすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、次のものを作成しました。

* 仮想ネットワークと bastion ホスト。
* 仮想マシン。
* Azure Web アプリのプライベート エンドポイント。

仮想マシンを使用して、プライベート エンドポイントを介した Web アプリへの接続を安全にテストしました。

プライベート エンドポイントをサポートするサービスの詳細については、以下を参照してください。
> [!div class="nextstepaction"]
> [Private Link の可用性](private-link-overview.md#availability)
