---
title: マネージド ディスクをサブスクリプションにコピーする - PowerShell のサンプル
description: Azure PowerShell サンプル スクリプト - マネージド ディスクを同じまたは別のサブスクリプションにコピーまたは移動する
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: e682a1546297e7715a00c7c174ad9a17021e6029
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320481"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>PowerShell でマネージド ディスクを同じサブスクリプションまたは別のサブスクリプションにコピーする

このスクリプトは、既存のマネージド ディスクのコピーを同じサブスクリプションまたは別のサブスクリプションに作成します。 新しいディスクは、親のマネージド ディスクと同じリージョンに作成されます。   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、コピー元のマネージド ディスクの ID を使ってコピー先のサブスクリプションに新しいマネージド ディスクを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | ディスクの作成に使用するディスク構成を作成します。 この構成には、親ディスクのリソース ID および親ディスクと同じ場所が含まれます。  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | パラメーターとして渡されたディスク構成、ディスク名、リソース グループ名を使ってディスクを作成します。 |


## <a name="next-steps"></a>次のステップ

[マネージド ディスクから仮想マシンを作成する](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
