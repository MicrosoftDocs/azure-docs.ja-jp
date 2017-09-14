---
title: "複数の NIC を使用する Azure での Windows VM の作成と管理 | Microsoft Docs"
description: "Azure PowerShell または Resource Manager テンプレートを使用して、複数の NIC を持つ Windows VM を作成および管理する方法について説明します。"
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
ms.date: 07/05/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 92f5181dbf36ef0f7e2568d557faa7c5f2144ad9
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>複数の NIC を持つ Windows 仮想マシンの作成と管理
Azure の仮想マシン (VM) は、複数の仮想ネットワーク インターフェイス カード (NIC) を持つことができます。 一般的なシナリオは、フロント エンドおよびバック エンド接続用に別々のサブネットを使用するか、監視またはバックアップ ソリューション専用のネットワークを用意することです。 この記事では、複数の NIC を持つ VM を作成する方法について説明します。 既存の VM に NIC を追加するまたはそこから NIC を削除する方法についても説明します。 [VM のサイズ](sizes.md)によってサポートされる NIC の数が異なります。VM のサイズを決める際はご注意ください。

独自の PowerShell スクリプト内に複数の NIC を作成する方法など、詳しくは、「[複数 NIC の VM のデプロイ](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
[最新バージョンの Azure PowerShell がインストールおよび構成](/powershell/azure/overview)されていることを確認します。

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myVnet*、*myVM*といったパラメーター名にします。


## <a name="create-a-vm-with-multiple-nics"></a>複数 NIC を持つ VM の作成
まず、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *EastUs* に作成します。

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>仮想ネットワークとサブネットの作成
一般的なシナリオでは、仮想ネットワークが 2 つ以上のサブネットを持ちます。 一方のサブネットがフロントエンド トラフィック用で、他方がバックエンド トラフィック用です。 両方のサブネットに接続するには、VM 上で複数の NIC を使用します。

1. [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用して 2 つの仮想ネットワーク サブネットを定義します。 次の例では、*mySubnetFrontEnd* と *mySubnetBackEnd* のサブネットを定義します。

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークとサブネットを作成します。 次の例では、*myVnet* という名前の仮想ネットワークを作成します。

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>複数の NIC の作成
[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) を使用して、2 つの NIC を作成します。 1 つをフロントエンド サブネットに、もう 1 つをバックエンド サブネットにアタッチします。 次の例では、*myNic1* と *myNic2* という名前の NIC を作成します。

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

通常は、VM 間でトラフィックを管理、分散するために、[ネットワーク セキュリティ グループ](../../virtual-network/virtual-networks-nsg.md)や[ロード バランサー](../../load-balancer/load-balancer-overview.md)も作成します。 ネットワーク セキュリティ グループの作成方法や NIC の割り当て方法については、[より詳しい複数 NIC の VM](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md) に関する記事を参照してください。

### <a name="create-the-virtual-machine"></a>仮想マシンの作成
では、VM 構成を構築してみましょう。 各 VM サイズについて、1 つの VM に追加できる NIC の合計数には制限があります。 詳細については、「[Windows VM のサイズ](sizes.md)」をご覧ください。

1. 次のように VM の資格情報を `$cred` 変数に設定します。

    ```powershell
    $cred = Get-Credential
    ```

2. [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) を使用して VM を定義します。 次の例では、*myVM* という名前の VM を定義し、2 つ以上の NIC をサポートする VM サイズを使用しています (*Standard_DS3_v2*)。

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) と [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) を使用して、残りの VM 構成を作成します。 次の例では、Windows Server 2016 の VM を作成します。

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. 以前に作成した 2 つの NIC を [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) にアタッチします。

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. 最後に、[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して VM を作成します。

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

## <a name="add-a-nic-to-an-existing-vm"></a>既存の VM への NIC の追加
既存の VM に仮想 NIC を追加するには、この VM の割り当てを解除し、仮想 NIC を追加してから、VM を起動します。

1. [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) を使用して VM の割り当てを解除します。 次の例では、*myResourceGroup* 内の *myVM* という VM の割り当てを解除します。

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm) を使用して、VM の既存の構成を取得します。 次の例では、*myResourceGroup* の *myVM* という名前の VM の情報を取得します。

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. 次の例では、*mySubnetBackEnd* にアタッチされる *myNic3* という名前の [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) を使用して、仮想 NIC を作成します。 この仮想 NIC は、[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) を使用して、*myResourceGroup* の *myVM* という名前の VM にアタッチされます。

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>プライマリ仮想 NIC
    複数 NIC VM のいずれかの NIC がプライマリである必要があります。 VM の既存の仮想 NIC の 1 つが既にプライマリとして設定されている場合、この手順をスキップできます。 次の例では、VM に 2 つの仮想 NIC が存在し、最初の NIC (`[0]`) をプライマリとして追加するものとします。
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm) を使用して VM を起動します。

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

## <a name="remove-a-nic-from-an-existing-vm"></a>既存の VM からの NIC の削除
既存の VM から仮想 NIC を削除するには、この VM の割り当てを解除し、仮想 NIC を削除してから、VM を起動します。

1. [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) を使用して VM の割り当てを解除します。 次の例では、*myResourceGroup* 内の *myVM* という VM の割り当てを解除します。

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm) を使用して、VM の既存の構成を取得します。 次の例では、*myResourceGroup* の *myVM* という名前の VM の情報を取得します。

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) を使用して、NIC 削除についての情報を取得します。 次の例では、*myNic3* に関する情報を取得します。

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. [Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface) を使用して NIC を削除してから [Update-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm) を使用して VM を更新します。 次の例では、前記の手順で `$nicId` が取得した *myNic3* を削除します。

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm) を使用して VM を起動します。

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>テンプレートを使用した複数の NIC の作成
Azure Resource Manager テンプレートでは、複数の NIC の作成など、デプロイ時にリソースの複数のインスタンスを作成することができます。 Resource Manager テンプレートで宣言型の JSON ファイルを使用して環境を定義します。 詳細については、「[Azure リソース マネージャーの概要](../../azure-resource-manager/resource-group-overview.md)」を参照してください。 *copy* を使用して、作成するインスタンスの数を指定できます。

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

詳細については、「[creating multiple instances by using *copy*](../../resource-group-create-multiple.md)」(copy を使用した複数のインスタンスの作成) を参照してください。 

`copyIndex()` を使用してリソース名に番号を付けることもできます。 *myNic1*、*MyNic2* などを作成できます。 次のコードは、インデックス値を追加する例を示します。

```json
"name": "[concat('myNic', copyIndex())]", 
```

完全な例については、「[Resource Manager テンプレートを使用して複数の NIC を作成する](../../virtual-network/virtual-network-deploy-multinic-arm-template.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
複数の NIC を持つ VM を作成する場合、「[Windows VM のサイズ](sizes.md)」を確認してください。 VM の各サイズでサポートされている NIC の最大数に注意してください。 



