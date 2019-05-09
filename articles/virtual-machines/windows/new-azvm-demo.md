---
title: Azure Cloud Shell で簡素化された New-AzVM コマンドレットを使用して Windows VM を作成する | Microsoft Docs
description: Azure Cloud Shell で簡素化された New-AzVM コマンドレットを使用して Windows 仮想マシンを作成する方法を簡単に説明します。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 02f2bd78ca5656534b106c6f7f18c05165b4b9ff
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444497"
---
# <a name="create-a-windows-virtual-machine-with-the-simplified-new-azvm-cmdlet-in-cloud-shell"></a>Cloud Shell で簡素化された New-AzVM コマンドレットを使用して Windows 仮想マシンを作成する 

[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) コマンドレットには、PowerShell を使用して新しい VM を作成するための簡素化されたパラメーター セットが追加されました。 このトピックでは、Azure Cloud Shell で PowerShell を使用して、プレインストールされている最新バージョンの New-AzureVM コマンドレットで新しい VM を作成する方法について説明します。 スマートな既定値を使用して必要なすべてのリソースが自動的に作成される、簡素化されたパラメーター セットを使用します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]


## <a name="create-the-vm"></a>VM の作成

[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) コマンドレットを使用して、Azure Marketplace の Windows Server 2016 Datacenter イメージの使用を含むスマートな既定値で VM を作成することができます。 New-AzVM は **-Name** パラメーターだけで使用できます。この値は、すべてのリソース名に使用されます。 この例では、**-Name** パラメーターを *myVM* として設定します。 

Cloud Shell で **PowerShell** が選択されていることを確認して、次のように入力します。

```azurepowershell-interactive
New-AzVm -Name myVM
```

VM のユーザー名とパスワードの作成を求められます。これらは、このトピックで後ほど VM に接続するときに使用します。 パスワードは、12 ～ 123 文字で指定する必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という複雑さの 4 要件のうち、3 つを満たしている必要があります。

VM と関連リソースを作成するにはしばらくかかります。 完了したら、[Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) コマンドレットを使用して、作成されたすべてのリソースを確認することができます。

```azurepowershell-interactive
Get-AzResource `
    -ResourceGroupName myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>VM に接続します

デプロイが完了したら、仮想マシンとのリモート デスクトップ接続を作成します。

[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) コマンドを使用して、仮想マシンのパブリック IP アドレスを返します。 この IP アドレスを書き留めておきます。

```azurepowershell-interactive
Get-AzPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

ローカル コンピューターで、コマンド プロンプトを開き、**mstsc** コマンドを使用して、新しい VM とのリモート デスクトップ セッションを開始します。 &lt;publicIPAddress&gt; を仮想マシンの IP アドレスで置き換えます。 メッセージが表示されたら、VM の作成時に設定したユーザー名とパスワードを入力します。

```
mstsc /v:<publicIpAddress>
```
## <a name="specify-different-resource-names"></a>異なるリソース名の指定

リソースにはよりわかりやすい名前を指定することもできます。この場合も、これらのリソースは自動で作成されます。 次の例では、新しいリソース グループなど、新しい VM の複数のリソースに名前が付けられています。

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 3389
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myVM
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このトピックでは、New-AzVM を使用して単純な仮想マシンをデプロイし、RDP 経由でその仮想マシンに接続しました。 Azure 仮想マシンの詳細については、Windows VM のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Windows 仮想マシンのチュートリアル](./tutorial-manage-vm.md)
