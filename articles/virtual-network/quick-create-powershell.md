---
title: 仮想ネットワークの作成 - クイック スタート - Azure PowerShell
titlesuffix: Azure Virtual Network
description: このクイック スタートでは、Azure Portal を使用した仮想ネットワークの作成について説明します。 仮想ネットワークによって、仮想マシンなどの Azure リソースが互いにプライベートな通信を行ったりインターネットと通信したりできるようになります。
services: virtual-network
documentationcenter: virtual-network
author: kumudd
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: kumud
ms.openlocfilehash: 3a21bff9e4c696063458d5734a579fc7c37258a7
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730076"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>クイック スタート:PowerShell を使用して仮想ネットワークを作成する

仮想ネットワークによって、仮想マシン (VM) などの Azure リソースが互いにプライベートな通信を行ったりインターネットと通信したりできるようになります。 このクイック スタートでは、仮想ネットワークの作成方法について説明します。 仮想ネットワークを作成したら、2 つの VM を仮想ネットワークにデプロイします。 次にインターネットから VM に接続し、仮想ネットワークを介してプライベートで通信します。

Azure サブスクリプションをお持ちでない場合は、ここで[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

代わりに PowerShell をインストールしてローカルで使用する場合、このクイック スタートでは Azure PowerShell モジュール バージョン 1.0.0 以降を使用する必要があります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 インストールまたはアップグレードの詳細については、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

最後に、PowerShell をローカルで実行している場合は、`Connect-AzAccount` も実行する必要があります。 このコマンドで、Azure との接続が作成されます。

## <a name="create-a-resource-group-and-a-virtual-network"></a>リソース グループと仮想ネットワークを作成する

リソース グループと仮想ネットワークを構成するために必要な手順がいくつかあります。

### <a name="create-the-resource-group"></a>リソース グループの作成

仮想ネットワークを作成するには、その前に、仮想ネットワークをホストするリソース グループを作成する必要があります。 [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) を使用して Azure リソース グループを作成します。 この例では、*eastus* の場所内に *myResourceGroup* という名前のリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

### <a name="create-the-virtual-network"></a>仮想ネットワークの作成

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) を使用して仮想ネットワークを作成します。 この例では、*EastUS* の場所内に *myVirtualNetwork* という名前の既定の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>サブネットの追加

Azure では、仮想ネットワーク内のサブネットにリソースがデプロイされるため、サブネットを作成する必要があります。 [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) を使用して、*default* というサブネット構成を作成します。

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
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
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
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
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

別のユーザーとパスワードを作成する必要があります。 Azure で VM を作成するには数分かかります。

> [!IMPORTANT]
> Azure の処理が完了するまで、次の手順に進まないでください。  処理の完了は、PowerShell に出力が返されることでわかります。

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) を使用して、VM のパブリック IP アドレスを返します。 この例では、*myVm1* VM のパブリック IP アドレスを返しています。

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
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

1. *myVm1* のリモート デスクトップで、PowerShell を開きます。

1. 「 `ping myVm2` 」を入力します。

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

1. 後の手順で *myVm2* に *myVm1* への ping を許可するには、次のコマンドを入力します。

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    このコマンドを実行すると、Windows ファイアウォールを介して ICMP を受信できます。

1. *myVm1* へのリモート デスクトップ接続を閉じます。

1. 「[インターネットから VM に接続する](#connect-to-a-vm-from-the-internet)」の手順を繰り返します。 今回は *myVm2* に接続します。

1. *myVm2* VM のコマンド プロンプトで `ping myvm1` と入力します。

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

    *myVm1* から応答を受信します。これは、前の手順で *myVm1* VM での Windows ファイアウォール経由の ICMP を許可したためです。

1. *myVm2* へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

仮想ネットワークと VM を完了したら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとそのすべてのリソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、既定の仮想ネットワークと 2 つの VM を作成しました。 インターネットから 1 つの VM に接続し、その VM ともう 1 つの VM のプライベート通信を行いました。 仮想ネットワーク設定について詳しくは、[仮想ネットワークの管理](manage-virtual-network.md)に関する記事をご覧ください。

Azure では、仮想マシン間で無制限のプライベート通信を実行できます。 既定で、Azure では、インターネットから Windows VM への受信リモート デスクトップ接続のみを許可しています。 さまざまな種類の VM ネットワーク通信の構成の詳細については、「[ネットワーク トラフィックをフィルター処理する](tutorial-filter-network-traffic.md)」チュートリアルを参照してください。
