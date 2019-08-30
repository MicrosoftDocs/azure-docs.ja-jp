---
title: Windows VM からデータ ディスクを切断する - Azure | Microsoft Docs
description: Resource Manager デプロイ モデルを使用して、Azure の仮想マシンからデータ ディスクをデタッチします。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: efbb6ccef9096ed89f6ccd16f8d3b37c9a97b278
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103207"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Windows 仮想マシンからディスクを切断する方法

仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。 そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。

> [!WARNING]
> ディスクを切断した場合、自動的には削除されません。 Premium Storage のサブスクリプションにお申込みいただいている場合は、ディスクのストレージ料金が引き続き発生します。 詳細については、[Premium Storage 利用時の料金と課金](disks-types.md#billing)に関する記事を参照してください。

再びディスク上の既存のデータを使用する場合は、同じ仮想マシンや別の仮想マシンに再接続できます。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell を使用してデータ ディスクを切断する方法

PowerShell を使用するとデータ ディスクを "*ホット*" 削除することができますが、ディスクを VM からデタッチする前に、そのディスクをアクティブに使用しているものがないことを確認してください。

この例では、**myResourceGroup** リソース グループの **myVM** VM から **myDisk** という名前のディスクを削除します。 最初に、[Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) コマンドレットを使用してディスクを削除します。 次に、[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) コマンドレットを使用して仮想マシンの状態を更新して、データ ディスクを削除するプロセスを完了します。

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

ディスクはストレージに残りますが、仮想マシンには接続されていません。

## <a name="detach-a-data-disk-using-the-portal"></a>ポータルを使用してデータ ディスクを切断する方法

1. 左側のメニューで **[Virtual Machines]** を選択します。
2. 切断するデータ ディスクが接続されている仮想マシンを選択し、 **[停止]** をクリックして VM の割り当てを解除します。
3. 仮想マシンのウィンドウで、 **[ディスク]** を選択します。
4. **[ディスク]** ウィンドウの上部にある **[編集]** を選択します。
5. **[ディスク]** ウィンドウで、切断するデータ ディスクの右端にある ![切断ボタンのイメージ](./media/detach-disk/detach.png) 切断ボタンをクリックします。
5. ディスクが削除されたら、ウィンドウの上部にある **[保存]** をクリックします。
6. 仮想マシンのウィンドウで **[概要]** をクリックし、ウィンドウの上部にある **[開始]** ボタンをクリックして VM を再起動します。

ディスクはストレージに残りますが、仮想マシンには接続されていません。

## <a name="next-steps"></a>次の手順

データ ディスクを再利用する場合は、 [別の VM にそのデータ ディスクをアタッチ](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)