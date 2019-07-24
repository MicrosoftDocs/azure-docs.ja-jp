---
title: Azure で VHD のスナップショットを作成する | Microsoft Docs
description: バックアップまたは問題のトラブルシューティングに使うために、Azure VM のコピーを作成する方法について説明します。
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 15eb778e-fc07-45ef-bdc8-9090193a6d20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: dea8547905cb558cb0be7dc23f89099773e84ff0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58074780"
---
# <a name="create-a-snapshot"></a>スナップショットの作成

スナップショットは、仮想ハード ドライブ (VHD) の完全な読み取り専用コピーです。 バックアップ、または仮想マシン (VM) の問題を解決するために、OS またはデータ ディスク VHD のスナップショットを取得できます。

スナップショットを使用して新しい VM を作成しようとしている場合は、進行中のすべてのプロセスを消去するため、スナップショットを作成する前に VM を正常にシャット ダウンすることをお勧めします。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左のメニューから、**[リソースの作成]** を選択し、**スナップショット**を探して選択します。
3. **スナップショット** ウィンドウで、**[作成]** を選択します。 **[スナップショットの作成]** ウィンドウが表示されます。
4. スナップショットの **[名前]** を入力します。
5. 既存の[リソース グループ](../../azure-resource-manager/resource-group-overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。 
6. Azure データセンターの**場所**を選択します。  
7. **[ソース ディスク]** で、スナップショットを作成するマネージド ディスクを選びます。
8. スナップショットの保存に使う **[アカウントの種類]** を選びます。 スナップショットを高パフォーマンスのディスクに保存する必要がある場合を除き、**[Standard_HDD]** を選択します。
9. **作成**を選択します。

## <a name="use-powershell"></a>PowerShell の使用

次の手順では、VHD ディスクのコピー方法、スナップショット構成の作成方法、[New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot) コマンドレットを使用したディスクのスナップショットの取得方法を示します。 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

1. パラメーターを設定します。 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. VM を取得します。

   ```azurepowershell-interactive
   $vm = get-azvm `
   -ResourceGroupName $resourceGroupName 
   -Name $vmName
   ```

3. スナップショットの構成を作成します。 この例では、スナップショットは OS ディスクのものです。

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig 
   -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id 
   -Location $location 
   -CreateOption copy
   ```
   
   > [!NOTE]
   > スナップショットをゾーン回復性のあるストレージに格納する場合は、[可用性ゾーン](../../availability-zones/az-overview.md)をサポートするリージョンにストレージを作成し、`-SkuName Standard_ZRS` パラメーターを含めます。   
   
4. スナップショットを取得します。

   ```azurepowershell-interactive
   New-AzSnapshot 
   -Snapshot $snapshot 
   -SnapshotName $snapshotName 
   -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>次の手順

スナップショットからマネージド ディスクを作成し、その新しいマネージド ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 詳細については、「[PowerShell でスナップショットから仮想マシンを作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)」のサンプルを参照してください。
