---
title: "複数の NIC を持つ Windows VM の作成 | Microsoft Docs"
description: "Azure PowerShell または Resource Manager テンプレートを使用して、複数の NIC を持つ Windows VM を作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 95b2820d2f68be34cca7b8d414c581ba44a29804
ms.lasthandoff: 03/17/2017


---
# <a name="create-a-windows-vm-with-multiple-nics"></a>複数の NIC を持つ Windows VM の作成
Azure では、複数の仮想ネットワーク インターフェイス (NIC) を持つ仮想マシン (VM) を作成できます。 一般的なシナリオは、フロント エンドおよびバック エンド接続用に別々のサブネットを使用するか、監視またはバックアップ ソリューション専用のネットワークを用意することです。 この記事では、複数の NIC を持つ VM を作成するためのクイック コマンドを紹介します。 独自の PowerShell スクリプト内に複数の NIC を作成する方法など、詳しくは、[複数 NIC の VM のデプロイ](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)に関する記事を参照してください。 [VM のサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)によってサポートされる NIC の数が異なります。VM のサイズを決める際はご注意ください。

## <a name="create-core-resources"></a>コア リソースの作成
[最新の Azure PowerShell がインストールおよび構成](/powershell/azureps-cmdlets-docs)されていることを確認します。 Azure アカウントにログインします。

```powershell
Login-AzureRmAccount
```

次の例では、パラメーター名を独自の値を置き換えます。 パラメーター名の例には、`myResourceGroup`、`mystorageaccount`、および `myVM` が含まれています。

まず、リソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `WestUs` の場所に作成します。

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

VM を保持するストレージ アカウントを作成します。 次の例では、`mystorageaccount` という名前のストレージ アカウントを作成します。

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>仮想ネットワークとサブネットの作成
2 つの仮想ネットワーク サブネットを定義します。1 つはフロント エンド トラフィック用、もう 1 つはバック エンド トラフィック用です。 次の例では、`mySubnetFrontEnd` と `mySubnetBackEnd` という名前の 2 つのサブネットを定義しています。

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

仮想ネットワークとサブネットを作成します。 次の例では、`myVnet` という名前の仮想ネットワークを作成します。

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>複数の NIC の作成
2 つの NIC を作成します。1 つをフロント エンド サブネットに、もう 1 つをバック エンド サブネットにアタッチします。 次の例では、`myNic1` と `myNic2` という名前の 2 つの NIC を作成します。

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

通常は、VM 間でトラフィックを管理、分散するために、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)や[ロード バランサー](../load-balancer/load-balancer-overview.md)も作成します。 ネットワーク セキュリティ グループの作成方法や NIC の割り当て方法については、 [より詳しい複数 NIC の VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) に関する記事を参照してください。

## <a name="create-the-virtual-machine"></a>仮想マシンの作成
では、VM 構成を構築してみましょう。 各 VM サイズについて、1 つの VM に追加できる NIC の合計数には制限があります。 詳しくは、「 [Windows VM のサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 

最初に、次のように VM の資格情報を `$cred` 変数に設定します。

```powershell
$cred = Get-Credential
```

次の例では、`myVM` という名前の VM を定義し、最大 2 つの NIC をサポートする VM サイズを使用しています (`Standard_DS2_v2`)。

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

VM 構成の残りの部分を作成します。 次の例では、Windows Server 2012 R2 の VM を作成します。

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName "myVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

以前に作成した 2 つの NIC をアタッチします。

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

新しい VM 用のストレージと仮想ディスクを構成します。

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

最後に、VM を作成します。

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="add-a-nic-to-an-existing-vm"></a>既存の VM への NIC の追加

既存の仮想マシンに NIC を追加できるようになりました。 この機能を使用するには、この後の Stop-AzureRmVM コマンドレットを使用して、まず VM の割り当てを解除する必要があります。

```powershell
Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
```

次に、Get-AzureRmVM コマンドレットを使用して、VM の既存の構成を取得します。

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
```

新しい NIC は、この記事の最初で説明したように **VM と同じ VNET** に作成できます。または、既存の NIC にアタッチできます。 VNET 内の既存の NIC `MyNic3` にアタッチすると仮定します。 

```powershell
$nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId -Primary | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
```

> [!NOTE]
> マルチ NIC VM 内の NIC の 1 つをプライマリにする必要があるため、新しい NIC をプライマリとして設定します。 VM 上の以前の NIC がプライマリである場合は、-Primary スイッチを指定する必要はありません。 VM 上のプライマリ NIC を切り替える場合は、次の手順に従います。

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"

# Find out all the NICs on the VM and find which one is Primary
$vm.NetworkProfile.NetworkInterfaces

# Set the NIC 0 to be primary
$vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
$vm.NetworkProfile.NetworkInterfaces[1].Primary = $false

# Update the VM state in Azure
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
```

## <a name="remove-a-nic-from-an-existing-vm"></a>既存の VM からの NIC の削除

NIC を VM から削除することもできます。 この機能を使用するには、この後の Stop-AzureRmVM コマンドレットを使用して、まず VM の割り当てを解除する必要があります。

```powershell
Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
```

次に、Get-AzureRmVM コマンドレットを使用して、VM の既存の構成を取得します。

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
```

ここで、VM 上のすべての NIC を表示し、削除する NIC の名前をコピーします。

```powershell
$vm.NetworkProfile.NetworkInterfaces

Remove-AzureRmNetworkInterface -Name "myNic3" -ResourceGroupName "myResourceGroup"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Resource Manager テンプレートを使用して複数の NIC を作成する
Azure Resource Manager テンプレートで宣言型の JSON ファイルを使用して環境を定義します。 詳しくは、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。 Resource Manager テンプレートでは、複数の NIC の作成など、デプロイ時にリソースの複数のインスタンスを作成することができます。 *copy* を使用して、作成するインスタンスの数を指定します。

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

詳細については、[*copy* を使用した複数のインスタンスの作成](../azure-resource-manager/resource-group-create-multiple.md)に関する記事を参照してください。 

`copyIndex()` を使用してリソース名に数値を追加することもできます。これにより、`myNic1`、`MyNic2` などを作成することができます。インデックス値を追加する例を次に示します。

```json
"name": "[concat('myNic', copyIndex())]", 
```

完全な例については、「 [Resource Manager テンプレートを使用して複数の NIC を作成する](../virtual-network/virtual-network-deploy-multinic-arm-template.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
複数 NIC を持つ VM を作成するときは必ず「 [Windows VM のサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 」をご覧ください。 VM の各サイズでサポートされている NIC の最大数に注意してください。 



