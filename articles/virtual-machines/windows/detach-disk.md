---
title: Windows VM からデータ ディスクを切断する - Azure
description: Resource Manager デプロイ モデルを使用して、Azure の仮想マシンからデータ ディスクをデタッチします。
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 24c95d486ce77028a2c49917d8f98de23a3a8315
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552135"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Windows 仮想マシンからディスクを切断する方法

仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。 そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。

> [!WARNING]
> ディスクを切断した場合、自動的には削除されません。 Premium Storage のサブスクリプションにお申込みいただいている場合は、ディスクのストレージ料金が引き続き発生します。 詳細については、[Premium Storage 利用時の料金と課金](../disks-types.md#billing)に関する記事を参照してください。

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

PowerShell を使用するとデータ ディスクを *ホット* 削除することができますが、VM からデタッチする前に、そのディスクをアクティブに使用しているものがないことを確認してください。

1. 左側のメニューで **[Virtual Machines]** を選択します。
1. 切断するデータディスクがある仮想マシンを選択します。
1. **[設定]** で **[ディスク]** を選択します。
1. **[ディスク]** ウィンドウで、デタッチしたいデータ ディスクの右端にある **[X]** ボタンを選択してデタッチします。
1. ページの上部にある **[保存]** を選択して、変更を保存します。

ディスクはストレージに残りますが、仮想マシンには接続されていません。 このディスクは削除されません。

## <a name="next-steps"></a>次のステップ

データ ディスクを再利用する場合は、[別の VM にそのデータ ディスクをアタッチ](attach-managed-disk-portal.md)します。

ディスクを削除して、ストレージ コストが発生しなくなるようにするには、「[接続されていない Azure マネージド ディスクとアンマネージド ディスクを見つけて削除する - Azure portal](../disks-find-unattached-portal.md)」を参照してください。
