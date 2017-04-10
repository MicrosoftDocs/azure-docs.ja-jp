---
title: "静的パブリック IP アドレスを持つ VM を作成する - Azure PowerShell | Microsoft Docs"
description: "PowerShell を使用して、静的パブリック IP アドレスを持つ VM を作成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ad975ab9-d69f-45c1-9e45-0d3f0f51e87e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: fb071365d8d716085d1b1bd1cb52fffc830fd75c
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-vm-with-a-static-public-ip-address-using-powershell"></a>PowerShell を使用して静的パブリック IP アドレスを持つ VM を作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [テンプレート](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (クラシック)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。 この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、クラシック デプロイメント モデルではなくこのモデルをお勧めします。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="step-1---start-your-script"></a>手順 1 - スクリプトの開始
使用するすべての PowerShell スクリプトは、 [ここ](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1)からダウンロードできます。 以下の手順に従って、ご使用の環境で機能するようにスクリプトを変更します。

デプロイに使用する値に基づいて、以下の変数の値を変更します。 以下の値は、この記事で使用するシナリオに対応しています。

```powershell
# Set variables resource group
$rgName                = "IaaSStory"
$location              = "West US"

# Set variables for VNet
$vnetName              = "WTestVNet"
$vnetPrefix            = "192.168.0.0/16"
$subnetName            = "FrontEnd"
$subnetPrefix          = "192.168.1.0/24"

# Set variables for storage
$stdStorageAccountName = "iaasstorystorage"

# Set variables for VM
$vmSize                = "Standard_A1"
$diskSize              = 127
$publisher             = "MicrosoftWindowsServer"
$offer                 = "WindowsServer"
$sku                   = "2012-R2-Datacenter"
$version               = "latest"
$vmName                = "WEB1"
$osDiskName            = "osdisk"
$nicName               = "NICWEB1"
$privateIPAddress      = "192.168.1.101"
$pipName               = "PIPWEB1"
$dnsName               = "iaasstoryws1"
```

## <a name="step-2---create-the-necessary-resources-for-your-vm"></a>手順 2 - VM に必要なリソースの作成
VM を作成するには、VM で使用するリソース グループ、VNet、パブリック IP、NIC が必要です。

1. 新しいリソース グループを作成します。

    ```powershell
    New-AzureRmResourceGroup -Name $rgName -Location $location
    ```

2. VNet とサブネットを作成します。

    ```powershell
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
        -AddressPrefix $vnetPrefix -Location $location

    Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
        -VirtualNetwork $vnet -AddressPrefix $subnetPrefix

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
    ```

3. パブリック IP リソースを作成します。 

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
        -AllocationMethod Static -DomainNameLabel $dnsName -Location $location
    ```

4. パブリック IP を使用して、上記で作成したサブネットに VM のネットワーク インターフェイス (NIC) を作成します。 VNet を Azure から取得する最初のコマンドレットに注意してください。これが必要なのは、既存の VNet を変更するために `Set-AzureRmVirtualNetwork` が実行されたためです。

    ```powershell
    $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
        -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
        -PublicIpAddress $pip
    ```

5. VM の OS ドライブをホストするストレージ アカウントを作成します。

    ```powershell
    $stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
    -ResourceGroupName $rgName -Type Standard_LRS -Location $location
    ```

## <a name="step-3---create-the-vm"></a>手順 3 - VM の作成
必要なリソースがすべて揃ったので、新しい VM を作成できます。

1. VM の構成オブジェクトを作成します。

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    ```

2. VM ローカル管理者アカウントの資格情報を取得します。

    ```powershell
    $cred = Get-Credential -Message "Type the name and password for the local administrator account."
    ```

3. VM 構成オブジェクトを作成します。

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    ```

4. VM のオペレーティング システム イメージを設定します。

    ```powershell
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
        -Offer $offer -Skus $sku -Version $version
    ```

5. OS ディスクを構成します。

    ```powershell
    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
    ```

6. NIC を VM に追加します。

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary
    ```

7. VM を作成します。

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location
    ```

8. スクリプト ファイルを保存します。

## <a name="step-4---run-the-script"></a>手順 4 - スクリプトの実行
必要な変更を加え、上記のスクリプトを理解したら、スクリプトを実行します。 

1. PowerShell コンソールまたは PowerShell ISE から上記のスクリプトを実行します。
2. 数分後に次の出力が表示されます。
   
        ResourceGroupName : IaaSStory
        Location          : westus
        ProvisioningState : Succeeded
        Tags              : 
        ResourceId        : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory
   
        AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
        DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
        Subnets           : {FrontEnd}
        ProvisioningState : Succeeded
        AddressSpaceText  : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptionsText   : {}
        SubnetsText       : [
                              {
                                "Name": "FrontEnd",
                                "AddressPrefix": "192.168.1.0/24"
                              }
                            ]
        ResourceGroupName : IaaSStory
        Location          : westus
        ResourceGuid      : [Id]
        Tag               : {}
        TagsTable         : 
        Name              : WTestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        Id                : /subscriptions/[Subscription ID]/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
   
        AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
        DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
        Subnets           : {FrontEnd}
        ProvisioningState : Succeeded
        AddressSpaceText  : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptionsText   : {
                              "DnsServers": []
                            }
        SubnetsText       : [
                              {
                                "Name": "FrontEnd",
                                "Etag": [Id],
                                "Id": "/subscriptions/[Subscription ID]/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "ProvisioningState": "Succeeded"
                              }
                            ]
        ResourceGroupName : IaaSStory
        Location          : westus
        ResourceGuid      : [Id]
        Tag               : {}
        TagsTable         : 
        Name              : WTestVNet
        Etag              : [Id]
        Id                : /subscriptions/[Subscription Id]/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
   
        TrackingOperationId : [Id]
        RequestId           : [Id]
        Status              : Succeeded
        StatusCode          : OK
        Output              : 
        StartTime           : [Subscription Id]
        EndTime             : [Subscription Id]
        Error               : 
        ErrorText           : 


