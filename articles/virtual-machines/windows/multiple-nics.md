---
title: 複数の NIC を使用する Azure での Windows VM の作成と管理
description: Azure PowerShell または Resource Manager テンプレートを使用して、複数の NIC を持つ Windows VM を作成および管理する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 2664d175818a6e29dcd3f704c6938987bae050cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555178"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>複数の NIC を持つ Windows 仮想マシンの作成と管理
Azure の仮想マシン (VM) は、複数の仮想ネットワーク インターフェイス カード (NIC) を持つことができます。 一般的なシナリオは、フロントエンドとバックエンドの接続に異なるサブネットを使用する場合です。 VM 上の複数の NIC を複数のサブネットに関連付けることはできますが、それらのサブネットはすべて同じ仮想ネットワーク (vNet) 内に存在する必要があります。 この記事では、複数の NIC を持つ VM を作成する方法について説明します。 既存の VM に NIC を追加するまたはそこから NIC を削除する方法についても説明します。 [VM のサイズ](../sizes.md)によってサポートされる NIC の数が異なります。VM のサイズを決める際はご注意ください。

## <a name="prerequisites"></a>前提条件

次の例では、パラメーター名を独自の値を置き換えます。 たとえば、*myResourceGroup*、*myVnet*、*myVM* といったパラメーター名にします。

 

## <a name="create-a-vm-with-multiple-nics"></a>複数 NIC を持つ VM の作成
まず、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *EastUs* に作成します。

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>仮想ネットワークとサブネットの作成
一般的なシナリオでは、仮想ネットワークが 2 つ以上のサブネットを持ちます。 一方のサブネットがフロントエンド トラフィック用で、他方がバックエンド トラフィック用です。 両方のサブネットに接続するには、VM 上で複数の NIC を使用します。

1. [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) を使用して 2 つの仮想ネットワーク サブネットを定義します。 次の例では、*mySubnetFrontEnd* と *mySubnetBackEnd* のサブネットを定義します。

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークとサブネットを作成します。 次の例では、*myVnet* という名前の仮想ネットワークを作成します。

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>複数の NIC の作成
[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、2 つの NIC を作成します。 1 つをフロントエンド サブネットに、もう 1 つをバックエンド サブネットにアタッチします。 次の例では、*myNic1* と *myNic2* という名前の NIC を作成します。

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

一般に、[ネットワーク セキュリティ グループ](../../virtual-network/network-security-groups-overview.md)を作成して、VM へのネットワーク トラフィックをフィルターし、[ロード バランサー](../../load-balancer/load-balancer-overview.md)を作成して、複数の VM 間でトラフィックを分散します。

### <a name="create-the-virtual-machine"></a>仮想マシンの作成
では、VM 構成を構築してみましょう。 各 VM サイズについて、1 つの VM に追加できる NIC の合計数には制限があります。 詳細については、「[Windows VM のサイズ](../sizes.md)」をご覧ください。

1. 次のように VM の資格情報を `$cred` 変数に設定します。

    ```powershell
    $cred = Get-Credential
    ```

2. [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) を使用して VM を定義します。 次の例では、*myVM* という名前の VM を定義し、2 つ以上の NIC をサポートする VM サイズを使用しています (*Standard_DS3_v2*)。

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) と [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) を使用して、残りの VM 構成を作成します。 次の例では、Windows Server 2016 の VM を作成します。

    ```powershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. 以前に作成した 2 つの NIC を [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) にアタッチします。

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. [New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成します。

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. [複数の NIC 用のオペレーティング システムの構成](#configure-guest-os-for-multiple-nics)に関する記事の手順を完了して、セカンダリ NIC の OS へのルートを追加します。

## <a name="add-a-nic-to-an-existing-vm"></a>既存の VM への NIC の追加
既存の VM に仮想 NIC を追加するには、この VM の割り当てを解除し、仮想 NIC を追加してから、VM を起動します。 [VM のサイズ](../sizes.md)によってサポートされる NIC の数が異なります。VM のサイズを決める際はご注意ください。 必要な場合は、[VM のサイズを変更できます](resize-vm.md)。

1. [Stop-AzVM](/powershell/module/az.compute/stop-azvm) を使用して VM の割り当てを解除します。 次の例では、*myResourceGroup* 内の *myVM* という VM の割り当てを解除します。

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. [Get-AzVm](/powershell/module/az.compute/get-azvm) を使用して、VM の既存の構成を取得します。 次の例では、*myResourceGroup* の *myVM* という名前の VM の情報を取得します。

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. 次の例では、*mySubnetBackEnd* にアタッチされる *myNic3* という名前の [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) を使用して、仮想 NIC を作成します。 この仮想 NIC は、[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) を使用して、*myResourceGroup* の *myVM* という名前の VM にアタッチされます。

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
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
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. [Start-AzVm](/powershell/module/az.compute/start-azvm) を使用して VM を起動します。

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. [複数の NIC 用のオペレーティング システムの構成](#configure-guest-os-for-multiple-nics)に関する記事の手順を完了して、セカンダリ NIC の OS へのルートを追加します。

## <a name="remove-a-nic-from-an-existing-vm"></a>既存の VM からの NIC の削除
既存の VM から仮想 NIC を削除するには、この VM の割り当てを解除し、仮想 NIC を削除してから、VM を起動します。

1. [Stop-AzVM](/powershell/module/az.compute/stop-azvm) を使用して VM の割り当てを解除します。 次の例では、*myResourceGroup* 内の *myVM* という VM の割り当てを解除します。

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. [Get-AzVm](/powershell/module/az.compute/get-azvm) を使用して、VM の既存の構成を取得します。 次の例では、*myResourceGroup* の *myVM* という名前の VM の情報を取得します。

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) を使用して、NIC 削除についての情報を取得します。 次の例では、*myNic3* に関する情報を取得します。

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. [Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface) を使用して NIC を削除してから [Update-AzVm](/powershell/module/az.compute/update-azvm) を使用して VM を更新します。 次の例では、前記の手順で `$nicId` が取得した *myNic3* を削除します。

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. [Start-AzVm](/powershell/module/az.compute/start-azvm) を使用して VM を起動します。

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>テンプレートを使用した複数の NIC の作成
Azure Resource Manager テンプレートでは、複数の NIC の作成など、デプロイ時にリソースの複数のインスタンスを作成することができます。 Resource Manager テンプレートで宣言型の JSON ファイルを使用して環境を定義します。 詳細については、「[Azure リソース マネージャーの概要](../../azure-resource-manager/management/overview.md)」を参照してください。 *copy* を使用して、作成するインスタンスの数を指定できます。

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

詳細については、「[creating multiple instances by using *copy*](../../azure-resource-manager/templates/copy-resources.md)」(copy を使用した複数のインスタンスの作成) を参照してください。 

`copyIndex()` を使用してリソース名に番号を付けることもできます。 *myNic1*、*MyNic2* などを作成できます。 次のコードは、インデックス値を追加する例を示します。

```json
"name": "[concat('myNic', copyIndex())]", 
```

完全な例については、「[Resource Manager テンプレートを使用して複数の NIC を作成する](../../virtual-network/template-samples.md)」を参照してください。

[複数の NIC 用のオペレーティング システムの構成](#configure-guest-os-for-multiple-nics)に関する記事の手順を完了して、セカンダリ NIC の OS へのルートを追加します。

## <a name="configure-guest-os-for-multiple-nics"></a>複数の NIC 用にゲスト OS を構成する

Azure では、既定のゲートウェイが、仮想マシンにアタッチされた最初の (プライマリ) ネットワーク インターフェイスに割り当てられます。 Azure では、既定のゲートウェイは、仮想マシンにアタッチされた追加の (セカンダリ) ネットワーク インターフェイスに割り当てられません。 したがって、既定では、セカンダリ ネットワーク インターフェイスのサブネット外のリソースと通信することはできません。 ただし、通信を有効にする手順はオペレーティング システムによってそれぞれ異なりますが、セカンダリ ネットワーク インターフェイスはサブネットの外部にあるリソースと通信することができます。

1. Windows コマンド プロンプトから `route print` コマンドを実行します。このコマンドは、2 つのネットワーク インターフェイスがアタッチされた仮想マシンについて、次の出力のような内容を返します。

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    この例で、**Microsoft Hyper-V Network Adapter #4** (インターフェイス 7) は、既定のゲートウェイが割り当てられていないセカンダリ ネットワーク インターフェイスです。

2. コマンド プロンプトから `ipconfig` コマンドを実行して、セカンダリ ネットワーク インターフェイスに割り当てられている IP アドレスを確認します。 この例では、インターフェイス 7 には 192.168.2.4 が割り当てられています。 セカンダリ ネットワーク インターフェイスに対して、既定のゲートウェイ アドレスは返されません。

3. セカンダリ ネットワーク インターフェイスのサブネットの外部にあるアドレスに宛てたすべてのトラフィックをサブネットのゲートウェイにルーティングするには、次のコマンドを実行します。

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    サブネットのゲートウェイ アドレスは、サブネットに対して定義されているアドレス範囲内の最初の IP アドレス (.1 で終わる) です。 すべてのトラフィックをサブネットの外部にルーティングするわけではない場合は、特定の宛先へのルートを個別に追加することができます。 たとえば、セカンダリ ネットワーク インターフェイスからネットワーク 192.168.3.0 にトラフィックをルーティングするだけの場合は、次のコマンドを入力します。

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. ネットワーク 192.168.3.0 上にあるリソースとの通信が成功したことを確認するには、次のコマンドを入力します。インターフェイス 7 (192.168.2.4) を使用して 192.168.3.4 に対して ping が実行されます。

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    次のコマンドを使用して、ping を実行しているデバイスの Windows ファイアウォールを介して ICMP を開くことが必要な場合があります。
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. 追加したルートがルート テーブルにあることを確認するには、`route print` コマンドを入力します。次のテキストのような出力が返されます。

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    リストされたルートのうち、**Gateway** の値が *192.168.1.1* になっているルートは、プライマリ ネットワーク インターフェイスの既定のルートです。 **Gateway** の値が *192.168.2.1* になっているルートは、追加したルートです。

## <a name="next-steps"></a>次のステップ
複数の NIC を持つ VM を作成する場合、「[Windows VM のサイズ](../sizes.md)」を確認してください。 VM の各サイズでサポートされている NIC の最大数に注意してください。