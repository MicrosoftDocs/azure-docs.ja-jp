---
title: Azure Virtual Machines 用の一般的な PowerShell コマンド | Microsoft Docs
description: Azure で Windows VM の作成および管理を開始する際に使用される一般的な PowerShell コマンド。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 9d0e27c2427d53554b454e0c319ce9cf180f1633
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820792"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Azure Virtual Machines の作成および管理に使用される一般的な PowerShell コマンド

この記事では、Azure サブスクリプション内の仮想マシンの作成および管理に使用できる Azure PowerShell コマンドの一部について説明します。  具体的なコマンド ライン スイッチやオプションについて詳しくは、**Get-Help** "*コマンド*" を使って確認できます。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

この記事内のコマンドを複数実行する場合は、変数が役に立つことがあります。

- $location: 仮想マシンの場所。 [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) を使用して、適切な[地理的リージョン](https://azure.microsoft.com/regions/)を検索できます。
- $myResourceGroup: 仮想マシンを含むリソース グループの名前。
- $myVM: 仮想マシンの名前。

## <a name="create-a-vm---simplified"></a>VM を作成する - 簡易版

| タスク | command |
| ---- | ------- |
| 簡単な VM を作成する | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> New-AzVM には "*簡易版*" パラメーターのセットがあり、必要なのは 1 つの名前だけです。 -Name の値は、新しい VM の作成に必要なすべてのリソースの名前として使われます。 詳細を指定できますが、必要なものはこれだけです。|
| カスタム イメージから VM を作成する | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>独自の[マネージド イメージ](capture-image-resource.md)を既に作成してある必要があります。 イメージを使って、複数の同じ VM を作成できます。 |



## <a name="create-a-vm-configuration"></a>VM 構成の作成

| タスク | command |
| ---- | ------- |
| VM 構成の作成 |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>VM 構成は、仮想マシンの設定の定義または更新に使用します。 この構成は、VM の名前とその [サイズ](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) で初期化します。 |
| 構成設定の追加 |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>[資格情報](https://technet.microsoft.com/library/hh849815.aspx) を含むオペレーティング システムの設定が、New-AzVMConfig を使用してあらかじめ作成した構成オブジェクトに追加されます。 |
| ネットワーク インターフェイスの追加 |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>仮想ネットワークで通信するには、VM に [ネットワーク インターフェイス](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) がある必要があります。 [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) を使用して既存のネットワーク インターフェイス オブジェクトを取得することもできます。 |
| プラットフォーム イメージの指定 |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "発行元名" -Offer "発行元のプラン" -Skus "製品 SKU" -Version "最新"<BR></BR><BR></BR>[イメージ情報](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) が、New-AzVMConfig を使用してあらかじめ作成した構成オブジェクトに追加されます。 このコマンドから返されるオブジェクトは、プラットフォーム イメージを使用するために OS ディスクを設定する場合にのみ使用されます。 |
| VM の作成 |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>すべてのリソースを[リソース グループ](../../azure-resource-manager/manage-resource-groups-powershell.md)に作成する必要があります。 このコマンドを実行する前に、New-AzVMConfig、Set-AzVMOperatingSystem、Set-AzVMSourceImage、Add-AzVMNetworkInterface、および Set-AzVMOSDisk を実行します。 |
| VM の更新 |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Get-AzVM を使用して現在の VM の構成を取得し、VM オブジェクトの構成設定を変更してから、このコマンドを実行します。 |

## <a name="get-information-about-vms"></a>VM に関する情報の取得

| タスク | command |
| ---- | ------- |
| サブスクリプション内の VM の一覧表示 |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| リソース グループ内の VM の一覧表示 |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>サブスクリプションのリソース グループのリストを取得するには、[Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup) を使用します。 |
| VM に関する情報の取得 |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>VM の管理
| タスク | command |
| --- | --- |
| VM の起動 |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM の停止 |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 実行中の VM の再起動 |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM の削除 |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>次の手順
* 仮想マシンを作成するための基本的な手順について「[Resource Manager と PowerShell を使用して Windows VM を作成する](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

