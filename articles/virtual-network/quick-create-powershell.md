---
title: 仮想ネットワークの作成 - クイック スタート - Azure PowerShell
titlesuffix: Azure Virtual Network
description: このクイックスタートでは、Azure portal を使用して仮想ネットワークを作成します。 仮想ネットワークを使用すると、Azure リソースが互いに通信を行ったり、インターネットと通信したりできるようになります。
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b27f050d3d37daab05e8c5125d6b75a6bb4dea50
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199035"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>クイック スタート:PowerShell を使用して仮想ネットワークを作成する

仮想ネットワークによって、仮想マシン (VM) などの Azure リソースが互いにプライベートな通信を行ったりインターネットと通信したりできるようになります。 

このクイック スタートでは、仮想ネットワークの作成方法について説明します。 仮想ネットワークを作成したら、2 つの VM を仮想ネットワークにデプロイします。 次にインターネットから VM に接続し、仮想ネットワークを介してプライベートで通信します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure PowerShell または Azure Cloud Shell

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group-and-a-virtual-network"></a>リソース グループと仮想ネットワークを作成する

リソース グループと仮想ネットワークを構成するために必要な手順がいくつかあります。

### <a name="create-the-resource-group"></a>リソース グループの作成

仮想ネットワークを作成するには、その前に、仮想ネットワークをホストするリソース グループを作成する必要があります。 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) を使用して Azure リソース グループを作成します。 この例では、**Eastus** の場所内に **CreateVNetQS-rg** という名前のリソース グループを作成します。

```azurepowershell-interactive
$rg = @{
    Name = 'CreateVNetQS-rg'
    Location = 'EastUS'
}
New-AzResourceGroup @rg
```

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。 この例では、**EastUS** の場所内に **myVNet** という名前の既定の仮想ネットワークを作成します。

```azurepowershell-interactive
$vnet = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-subnet"></a>サブネットの追加

Azure では、仮想ネットワーク内のサブネットにリソースがデプロイされるため、サブネットを作成する必要があります。 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) を使用して、**default** というサブネット構成を作成します。

```azurepowershell-interactive
$subnet = @{
    Name = 'default'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>サブネットを仮想ネットワークに関連付ける

[Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) を使用して、仮想ネットワークにサブネット構成を書き込むことができます。 このコマンドで、サブネットが作成されます。

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

仮想ネットワーク内に 2 つの VM を作成します。

### <a name="create-the-first-vm"></a>最初の VM を作成する

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して最初の VM を作成します。 次のコマンドを実行すると、資格情報の入力が求められます。 VM のユーザー名とパスワードを入力します。

```azurepowershell-interactive
$vm1 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM1'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm1 -AsJob
```

`-AsJob` オプションを使用すると、バックグラウンドで VM が作成されます。 次の手順に進むことができます。

Azure でバックグラウンドで VM の作成を開始すると、次のような内容が返されます。

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>2 つ目の VM を作成する

このコマンドを使用して 2 番目の VM を作成します。

```azurepowershell-interactive
$vm2 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM2'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm2
```

別のユーザーとパスワードを作成する必要があります。 Azure で VM を作成するには数分かかります。

> [!IMPORTANT]
> Azure の処理が完了するまで、次の手順に進まないでください。  処理の完了は、PowerShell に出力が返されることでわかります。

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

VM のパブリック IP アドレスを取得するには、[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) を使用します。

この例では、**myVm1** VM のパブリック IP アドレスを返しています。

```azurepowershell-interactive
$ip = @{
    Name = 'myVM1'
    ResourceGroupName = 'CreateVNetQS-rg'
}
Get-AzPublicIpAddress @ip | select IpAddress
```

お使いのローカル コンピューターでコマンド プロンプトを開きます。 `mstsc` コマンドを実行します。 `<publicIpAddress>` を最後の手順で返されたパブリック IP アドレスに置き換えます。

> [!NOTE]
> ローカル コンピューター上の PowerShell プロンプトからこれらのコマンドを実行済みで、Az PowerShell モジュール バージョン 1.0 以降を使用している場合、そのインターフェイスで続行できます。

```cmd
mstsc /v:<publicIpAddress>
```
1. メッセージが表示されたら、 **[Connect]** を選択します。

1. VM の作成時に指定したユーザー名とパスワードを入力します。

    > [!NOTE]
    > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

1. **[OK]** を選択します。

1. 証明書の警告を受け取ることがあります。 この場合は、 **[はい]** または **[続行]** を選択します。

## <a name="communicate-between-vms"></a>VM 間の通信

1. **myVm1** のリモート デスクトップで、PowerShell を開きます。

1. 「`ping myVm2`」と入力します。

    次のような内容が返されます。

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    インターネット制御メッセージ プロトコル (ICMP) を使用しているため、ping は失敗します。 既定では、Windows ファイアウォールを介した ICMP は許可されていません。

1. 後の手順で **myVm2** に **myVm1** への ping を許可するには、次のコマンドを入力します。

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    このコマンドを実行すると、Windows ファイアウォールを介して ICMP を受信できます。

1. **myVm1** へのリモート デスクトップ接続を閉じます。

1. 「[インターネットから VM に接続する](#connect-to-a-vm-from-the-internet)」の手順を繰り返します。 今回は **myVm2** に接続します。

1. **myVm2** VM のコマンド プロンプトで `ping myvm1` と入力します。

    次のような内容が返されます。

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    **myVm1** から応答を受信します。これは、前の手順で **myVm1** VM での Windows ファイアウォール経由の ICMP を許可したためです。

1. **myVm2** へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

仮想ネットワークと VM を完了したら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとそのすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateVNetQS-rg' -Force
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは次のようにします。 

* 既定の仮想ネットワークと 2 つの VM を作成しました。 
* インターネットから 1 つの VM に接続し、2 つの VM の間でプライベート通信を行いました。

仮想ネットワークでは、VM 間のプライベート通信に制限はありません。 

各種 VM ネットワーク通信の構成の詳細については、次の記事に進んでください。
> [!div class="nextstepaction"]
> [ネットワーク トラフィックをフィルター処理する](tutorial-filter-network-traffic.md)
