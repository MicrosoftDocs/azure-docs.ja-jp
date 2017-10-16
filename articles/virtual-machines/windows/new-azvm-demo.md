---
title: "Azure Cloud Shell で New-AzVM コマンドレットを使用して Windows VM を作成する | Microsoft Docs"
description: "Azure Cloud Shell で New-AzVM コマンドレットを使用して Windows 仮想マシンを作成する方法を簡単に説明します。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/21/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 3be46c8c02ad136edb1936fbb39560d479b27277
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-new-azvm-preview-in-cloud-shell"></a>Cloud Shell で New-AzVM (プレビュー) を使用して Windows 仮想マシンを作成する 

New-AzVM (プレビュー) コマンドレットは、PowerShell を使用して新しい VM を簡単に作成する方法です。 このガイドでは、Azure Cloud Shell で PowerShell を使用して、プレインストールされている New-AzVM コマンドレットを用いて、Windows Server 2016 を実行する新しい Azure 仮想マシンを作成する方法について説明します。 デプロイが完了したら、RDP を使用してサーバーに接続します。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


[!INCLUDE [cloud-shell-powershell](../../../includes/cloud-shell-powershell.md)]

## <a name="create-the-vm"></a>VM の作成

**New-AzVM** コマンドレットを使用して、Azure Marketplace の Windows Server 2016 Datacenter イメージの使用を含むスマートな既定値で VM を作成することができます。 New-AzVM を単独で使用すると、リソース名に既定値が使用されます。 この例では、**-Name** パラメーターを *myVM* として設定します。 このコマンドレットは、リソース名のプレフィックスとして *myVM* を使用して、必要なすべてのリソースを作成します。 

Cloud Shell で **PowerShell** が選択されていることを確認して、次のように入力します。

```azurepowershell-interactive
New-AzVm -Name myVM
```

VM のユーザー名とパスワードの作成を求められます。これらは、このトピックで後ほど VM に接続するときに使用します。 パスワードは、12 ～ 123 文字で指定する必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という複雑さの 4 要件のうち、3 つを満たしている必要があります。

VM と関連リソースを作成するにはしばらくかかります。 完了したら、[Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) コマンドレットを使用して、作成されたすべてのリソースを確認することができます。

```azurepowershell-interactive
Find-AzureRmResource `
    -ResourceGroupNameEquals myVMResourceGroup | Format-Table Name
```

## <a name="connect-to-the-vm"></a>VM に接続します

デプロイが完了したら、仮想マシンとのリモート デスクトップ接続を作成します。

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) コマンドを使用して、仮想マシンのパブリック IP アドレスを返します。 この IP アドレスを書き留めておきます。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
    -ResourceGroupName myVMResourceGroup | Select IpAddress
```

ローカル コンピューターで、コマンド プロンプトを開き、**mstsc** コマンドを使用して、新しい VM とのリモート デスクトップ セッションを開始します。 &lt;publicIPAddress&gt; を仮想マシンの IP アドレスで置き換えます。 メッセージが表示されたら、VM の作成時に設定したユーザー名とパスワードを入力します。

```
mstsc /v:<publicIpAddress>
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myVMResourceGroup
```

## <a name="next-steps"></a>次のステップ

このトピックでは、New-AzVM を使用して単純な仮想マシンをデプロイし、RDP 経由でその仮想マシンに接続しました。 Azure 仮想マシンの詳細については、Windows VM のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Windows 仮想マシンのチュートリアル](./tutorial-manage-vm.md)
