---
title: 仮想ハード ドライブのスナップショットを Azure 上に作成する
description: バックアップまたは問題のトラブルシューティングに使うために、Azure VM のコピーを作成する方法について説明します。
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e5ecb99c7f64d81d57c5d6d2cb25967913a752b4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074133"
---
# <a name="create-a-snapshot"></a>スナップショットの作成

スナップショットは、仮想ハード ドライブ (VHD) の完全な読み取り専用コピーです。 バックアップ、または仮想マシン (VM) の問題を解決するために、OS またはデータ ディスク VHD のスナップショットを取得できます。

スナップショットを使用して新しい VM を作成しようとしている場合は、進行中のすべてのプロセスを消去するため、スナップショットを作成する前に VM を正常にシャット ダウンすることをお勧めします。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用 

スナップショットを作成するには、次の手順を完了します。 
1.  [Azure portal](https://portal.azure.com) で、 **[リソースの作成]** を選択します。
2. **[スナップショット]** を探して選択します。
3. **スナップショット** ウィンドウで、 **[作成]** を選択します。 **[スナップショットの作成]** ウィンドウが表示されます。
4. スナップショットの **[名前]** を入力します。
5. 既存の[リソース グループ](../../azure-resource-manager/management/overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。 
6. Azure データセンターの**場所**を選択します。  
7. **[ソース ディスク]** で、スナップショットを作成するマネージド ディスクを選びます。
8. スナップショットの保存に使う **[アカウントの種類]** を選びます。 スナップショットを高パフォーマンスのディスクに保存する必要がある場合を除き、 **[Standard_HDD]** を選択します。
9. **作成** を選択します。

## <a name="use-powershell"></a>PowerShell の使用

次の手順は、VHD ディスクをコピーし、スナップショット構成を作成する方法を示しています。 その後、[New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) コマンドレットを使用して、ディスクのスナップショットを取得できます。 

 

1. パラメーターを設定します。 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. VM を取得します。

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. スナップショットの構成を作成します。 この例では、スナップショットは OS ディスクのものです。

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > スナップショットをゾーン回復性のあるストレージに格納する場合は、[可用性ゾーン](../../availability-zones/az-overview.md)をサポートするリージョンにストレージを作成し、`-SkuName Standard_ZRS` パラメーターを含めます。   
   
4. スナップショットを取得します。

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>次のステップ

スナップショットからマネージド ディスクを作成し、その新しいマネージド ディスクを OS ディスクとして接続することで、スナップショットから仮想マシンを作成します。 詳細については、「[PowerShell でスナップショットから仮想マシンを作成する](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)」のサンプルを参照してください。
