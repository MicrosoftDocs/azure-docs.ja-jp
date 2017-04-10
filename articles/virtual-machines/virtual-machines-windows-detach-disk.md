---
title: "Windows VM からデータ ディスクを切断する - Azure | Microsoft Docs"
description: "Resource Manager デプロイ モデルを使用する Azure 仮想マシンから、データ ディスクを切断する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 7274a86ada8115e86156592cf034d0350f59ebab
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Windows 仮想マシンからディスクを切断する方法
仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。 そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。 

> [!WARNING]
> ディスクを切断した場合、自動的には削除されません。 Premium Storage のサブスクリプションにお申込みいただいている場合は、ディスクのストレージ料金が引き続き発生します。 詳細については [Premium Storage 利用時の料金と課金](../storage/storage-premium-storage.md#pricing-and-billing)に関する記事を参照してください。 
> 
> 

再びディスク上の既存のデータを使用する場合は、同じ仮想マシンや別の仮想マシンに再接続できます。  

## <a name="detach-a-data-disk-using-the-portal"></a>ポータルを使用してデータ ディスクを切断する方法
1. ポータル ハブで **[仮想マシン]**を選択します。
2. 切断するデータ ディスクが接続されている仮想マシンを選択し、**[停止]** をクリックして VM の割り当てを解除します。
3. 仮想マシンのブレードで、**[ディスク]** を選択します。
4. **[ディスク]** ブレードの上部で、**[編集]** を選択します。
5. **[ディスク]** ブレードで、切断するデータ ディスクの右端にある ![切断ボタン イメージ](./media/virtual-machines-common-detach-disk/detach.png) 切断ボタンをクリックします。
5. ディスクが削除されたら、ブレードの上部にある [保存] をクリックします。
6. [仮想マシン] ブレードで **[概要]** をクリックし、ブレードの上部にある **[開始]** ボタンをクリックして VM を再起動します。



ディスクはストレージに残りますが、仮想マシンからは切断されています。

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell を使用してデータ ディスクを切断する方法
この例では、最初のコマンドで Get-AzureRmVM コマンドレットを使用して、リソース グループ **RG11** の**MyVM07** という名前の仮想マシンを取得します。 コマンドは仮想マシンを変数 **$VirtualMachine** に保存します。 

2 番目のコマンドは、DataDisk3 という名前のデータ ディスクを仮想マシンから削除します。 

最後のコマンドは仮想マシンの状態を更新し、データ ディスク削除のプロセスを完了します。

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine
```

詳細については、「[Remove-AzureRmVMDataDisk](/powershell/remove-azurermvmdatadisk)」を参照してください。

## <a name="next-steps"></a>次のステップ
データ ディスクを再利用する場合は、 [別の VM にそのデータ ディスクをアタッチ](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


