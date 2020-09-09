---
title: サブスクリプションのストレージ アカウントに VHD ファイルからマネージド ディスクを作成する - PowerShell のサンプル
description: Azure PowerShell サンプル スクリプト -  同じまたは別のサブスクリプションのストレージ アカウントに VHD ファイルからマネージド ディスクを作成する
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 1d743d840afc4505cdc39b258b21856b6c3e7aa9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322694"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>PowerShell で同じまたは別のサブスクリプションのストレージ アカウントに VHD ファイルからマネージド ディスクを作成する

このスクリプトでは、同じまたは別のサブスクリプションのストレージ アカウントに VHD ファイルからマネージド ディスクを作成します。 sysprep された汎用的な VHD ではなく特殊化された VHD を OS の管理ディスクにインポートして仮想マシンを作成するには、このスクリプトを使います。 また、マネージド データ ディスクにデータ VHD をインポートするときにも使うことができます。 

短時間に 1 つの VHD ファイルからまったく同じ複数のマネージド ディスクを作成することは避けてください。 vhd ファイルからマネージド ディスクを作成する際は、vhd ファイルの BLOB スナップショットが作成され、それを使ってマネージド ディスクが作成されます。 すぐに作成できる BLOB スナップショットは 1 つだけであるため、ディスク作成でスロットルに起因するエラーが発生します。 短時間に複数のマネージド ディスクを作成する場合は、このスロットルを回避するために、[vhd ファイルから管理スナップショット](virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)を作成したうえで、その管理スナップショットを使ってマネージド ディスクを作成するようにしてください。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを使って別のサブスクリプションに VHD からマネージド ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | ディスクの作成に使用するディスク構成を作成します。 この構成には、ストレージの種類、場所、親 VHD が格納されているストレージ アカウントのリソース ID、親 VHD の URI が含まれます。 |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | パラメーターとして渡されたディスク構成、ディスク名、リソース グループ名を使ってディスクを作成します。 |

## <a name="next-steps"></a>次のステップ

[マネージド ディスクを OS ディスクとして接続することで仮想マシンを作成する](virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
