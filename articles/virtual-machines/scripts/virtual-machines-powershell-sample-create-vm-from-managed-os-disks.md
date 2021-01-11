---
title: マネージド ディスクを OS ディスクとして接続して VM を作成する - PowerShell
description: Azure PowerShell のサンプル スクリプト - マネージド ディスクを OS ディスクとして接続することで VM を作成します
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 038ec433a4f27eec5ace537ff9b08e5ff6c2f6a9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322734"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>PowerShell で既存の管理 OS ディスクを使用して仮想マシンを作成する 

このスクリプトは、既存のマネージド ディスクを OS ディスクとして接続することで仮想マシンを作成します。 このスクリプトは、次のシナリオで使用します。
* 別のサブスクリプションのマネージド ディスクからコピーされた既存の管理 OS ディスクから VM を作成する
* 特化された VHD ファイルから作成された既存のマネージド ディスクから VM を作成する 
* スナップショットから作成された既存の管理 OS ディスクから VM を作成する 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用して、マネージド ディスクのプロパティを取得し、マネージド ディスクを新しい VM に接続して VM を作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Get-AzDisk](/powershell/module/az.compute/get-azdisk) | ディスクの名前とリソース グループに基づいてディスク オブジェクトを取得します。 返されたディスク オブジェクトの Id プロパティは、ディスクを新しい VM に接続するために使用されます。 |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | VM 構成を作成します。 この構成には、VM 名、オペレーティング システム、管理資格情報などの情報が含まれます。 この構成は、VM の作成時に使用されます。 |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | ディスクの Id プロパティを使用して、マネージド ディスクを OS ディスクとして新しい仮想マシンに接続します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | パブリック IP アドレスを作成します。 |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | ネットワーク インターフェイスを作成します。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 仮想マシンを作成します。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

マーケットプレース イメージの場合は、[Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) を使用してプラン情報を設定します。

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
