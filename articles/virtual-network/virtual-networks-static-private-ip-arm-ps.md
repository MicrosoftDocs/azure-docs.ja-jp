---
title: "VM のプライベート IP アドレスの構成 - Azure PowerShell | Microsoft Docs"
description: "PowerShell を使用して仮想マシンのプライベート IP アドレスを構成する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b1eb8aa6bc822932b9f2abd1c448aca96069fefa
ms.openlocfilehash: 2810190897c44c944912ef3325b1f40479aa3078
ms.lasthandoff: 02/28/2017


---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-powershell"></a>PowerShell を使用して仮想マシンのプライベート IP アドレスを構成する

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

Azure には、Azure Resource Manager とクラシックという&2; 種類のデプロイメント モデルがあります。 Resource Manager デプロイメント モデルを使用してリソースを作成することをお勧めします。 2 つのモデルの違いの詳細については、[Azure のデプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md)に関する記事を参照してください。 この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [クラシック デプロイ モデルで静的プライベート IP アドレスを管理する](virtual-networks-static-private-ip-classic-ps.md)こともできます。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

以下の PowerShell のサンプル コマンドでは、上記シナリオに基づいて単純な環境が既に作成されていると想定します。 このドキュメントに表示されているコマンドを実行する場合は、まず、 [vnet の作成](virtual-networks-create-vnet-arm-ps.md)に関する記事に示されているテスト環境を構築します。

## <a name="create-a-vm-with-a-static-private-ip-address"></a>静的プライベート IP アドレスを持つ VM を作成する
静的プライベート IP *192.168.1.101* を使用して、*TestVNet* という名前の VNet の *FrontEnd* サブネットで *DNS01* という名前の VM を作成するには、以下の手順に従います。

1. ストレージ アカウント、場所、リソース グループ、および使用する資格情報の変数を設定します。 VM のユーザー名とパスワードを入力する必要があります。 ストレージ アカウントおよびリソース グループが既に存在している必要があります。

    ```powershell
    $stName  = "vnetstorage"
    $locName = "Central US"
    $rgName  = "TestRG"
    $cred    = Get-Credential -Message "Type the name and password of the local administrator account."
    ```

2. 仮想ネットワークと VM を作成するサブネットを取得します。

    ```powershell
    $vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    $subnet = $vnet.Subnets[0].Id
    ```

3. 必要に応じて、インターネットから VM にアクセスするためのパブリック IP アドレスを作成します。

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName `
    -Location $locName -AllocationMethod Dynamic
    ```

4. VM に割り当てる静的プライベート IP アドレスを使用して、NIC を作成します。 IP が、VM を追加するサブネットの範囲内であることを確認します。 これはこの記事の主要な手順です (プライベート IP が静的になるように設定する場合)。

    ```powershell
    $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName `
    -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id `
    -PrivateIpAddress 192.168.1.101
    ```

5. 上記で作成した NIC を使用して VM を作成します。

    ```powershell
    $vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01 `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri `
    -CreateOption fromImage
    New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 
    ```

    予想される出力:
    
        EndTime             : [Date and time]
        Error               : 
        Output              : 
        StartTime           : [Date and time]
        Status              : Succeeded
        TrackingOperationId : [Id]
        RequestId           : [Id]
        StatusCode          : OK 

## <a name="retrieve-static-private-ip-address-information-for-a-network-interface"></a>ネットワーク インターフェイス用の静的プライベート IP アドレス情報を取得する
上記のスクリプトで作成された VM の静的プライベート IP アドレス情報を表示するには、次の PowerShell コマンドを実行し、*PrivateIpAddress* と *PrivateIpAllocationMethod* の値を確認します。

```powershell
Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
```

予想される出力:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Static",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="remove-a-static-private-ip-address-from-a-network-interface"></a>ネットワーク インターフェイスから静的プライベート IP アドレスを削除する
上記のスクリプトで VM に追加された静的プライベート IP アドレスを削除するには、次の PowerShell コマンドを実行します。

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

予想される出力:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WindowsVM"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Dynamic",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="add-a-static-private-ip-address-to-a-network-interface"></a>ネットワーク インターフェイスに静的プライベート IP アドレスを追加する
上記のスクリプトを使用して作成した VM に静的プライベート IP アドレスを追加するには、次のコマンドを実行します。

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```
## <a name="change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>ネットワーク インターフェイスに割り当てられているプライベート IP アドレスの割り当て方法を変更する

プライベート IP アドレスは、静的または動的な割り当て方法を使用して NIC に割り当てられます。 動的 IP アドレスは、以前は停止 (割り当て解除) 状態だった VM を起動した後に変更できます。 VM が停止 (割り当て解除) 状態から再起動された後であっても、同じ IP アドレスを必要とするサービスをホストしている場合は、問題が起こる可能性があります。 静的 IP アドレスは、VM が削除されるまで保持されます。 IP アドレスの割り当て方法を変更するには、割り当て方法を動的から静的に変更する次のスクリプトを実行します。 現在のプライベート IP アドレスの割り当て方法が静的な場合は、スクリプトを実行する前に、*Static* を *Dynamic* に変更します。

```powershell
$RG = "TestRG"
$NIC_name = "testnic1"

$nic = Get-AzureRmNetworkInterface -ResourceGroupName $RG -Name $NIC_name
$nic.IpConfigurations[0].PrivateIpAllocationMethod = 'Static'
Set-AzureRmNetworkInterface -NetworkInterface $nic 
$IP = $nic.IpConfigurations[0].PrivateIpAddress

Write-Host "The allocation method is now set to"$nic.IpConfigurations[0].PrivateIpAllocationMethod"for the IP address" $IP"." -NoNewline
```

NIC の名前がわからない場合は、次のコマンドを入力してリソース グループ内の NIC の一覧を表示することができます。

```powershell
Get-AzureRmNetworkInterface -ResourceGroupName $RG | Where-Object {$_.ProvisioningState -eq 'Succeeded'} 
```

## <a name="next-steps"></a>次のステップ
* [予約済みパブリック IP](virtual-networks-reserved-public-ip.md) アドレスについて理解する。
* [インスタンスレベル パブリック IP (ILPIP)](virtual-networks-instance-level-public-ip.md) アドレスについて理解する。
* [予約済み IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)を確認する。


