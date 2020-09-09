---
title: Windows VM からデータ ディスクを切断する - Azure
description: Resource Manager デプロイ モデルを使用して、Azure の仮想マシンからデータ ディスクをデタッチします。
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/08/2020
ms.author: cynthn
ms.openlocfilehash: 02192850dd9103b6c1783baf42c184ee16fe299f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500686"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Windows 仮想マシンからディスクを切断する方法

仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。 そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。

> [!WARNING]
> ディスクを切断した場合、自動的には削除されません。 Premium Storage のサブスクリプションにお申込みいただいている場合は、ディスクのストレージ料金が引き続き発生します。 詳細については、[Premium Storage 利用時の料金と課金](disks-types.md#billing)に関する記事を参照してください。

再びディスク上の既存のデータを使用する場合は、同じ仮想マシンや別の仮想マシンに再接続できます。

 

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell を使用してデータ ディスクを切断する方法

PowerShell を使用するとデータ ディスクを "*ホット*" 削除することができますが、ディスクを VM からデタッチする前に、そのディスクをアクティブに使用しているものがないことを確認してください。

この例では、**myResourceGroup** リソース グループの **myVM** VM から **myDisk** という名前のディスクを削除します。 最初に、[Remove-AzVMDataDisk](/powershell/module/az.compute/remove-azvmdatadisk) コマンドレットを使用してディスクを削除します。 次に、[Update-AzVM](/powershell/module/az.compute/update-azvm) コマンドレットを使用して仮想マシンの状態を更新して、データ ディスクを削除するプロセスを完了します。

```azurepowershell-interactive
$VirtualMachine = Get-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVM"
Remove-AzVMDataDisk `
   -VM $VirtualMachine `
   -Name "myDisk"
Update-AzVM `
   -ResourceGroupName "myResourceGroup" `
   -VM $VirtualMachine
```

ディスクはストレージに残りますが、仮想マシンには接続されていません。

## <a name="detach-a-data-disk-using-the-portal"></a>ポータルを使用してデータ ディスクを切断する方法

PowerShell を使用するとデータ ディスクを*ホット*削除することができますが、VM からデタッチする前に、そのディスクをアクティブに使用しているものがないことを確認してください。

1. 左側のメニューで **[Virtual Machines]** を選択します。
1. 切断するデータディスクがある仮想マシンを選択します。
1. **[設定]** で **[ディスク]** を選択します。
1. **[ディスク]** ウィンドウの上部にある **[編集]** を選択します。
1. **[ディスク]** ウィンドウで、デタッチしたいデータ ディスクの右端にある **[デタッチ]** ボタンを選択します。
1. ページの上部にある **[保存]** を選択して、変更を保存します。

ディスクはストレージに残りますが、仮想マシンには接続されていません。

## <a name="next-steps"></a>次のステップ

データ ディスクを再利用する場合は、 [別の VM にそのデータ ディスクをアタッチ](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
