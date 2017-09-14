---
title: "Azure PowerShell サンプル スクリプト - VHD からスナップショットを作成してまったく同じ複数の管理ディスクを短時間で作成する | Microsoft Docs"
description: "Azure PowerShell サンプル スクリプト - VHD からスナップショットを作成してまったく同じ複数の管理ディスクを短時間で作成する"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 02a69abd6c17ce765996379309e22afad82c4e10
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>PowerShell で VHD からスナップショットを作成してまったく同じ複数の管理ディスクを短時間で作成する

このスクリプトでは、同じまたは別のサブスクリプションのストレージ アカウントに VHD ファイルからスナップショットを作成します。 sysprep された汎用的な VHD ではなく特殊化された VHD をインポートしてスナップショットを作成し、そのスナップショットを使ってまったく同じ複数の管理ディスクを短時間で作成するには、このスクリプトを使います。 また、データ VHD をスナップショットにインポートし、そのスナップショットを使って複数の管理ディスクを短時間で作成する際にも使うことができます。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[メイン](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "VHD からスナップショットを作成する")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを使って別のサブスクリプションに VHD から管理ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | ディスクの作成に使用するディスク構成を作成します。 この構成には、ストレージの種類、場所、親 VHD が格納されているストレージ アカウントのリソース ID、親 VHD の URI が含まれます。 |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | パラメーターとして渡されたディスク構成、ディスク名、リソース グループ名を使ってディスクを作成します。 |

## <a name="next-steps"></a>次のステップ

[スナップショットから管理ディスクを作成する](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[管理ディスクを OS ディスクとしてアタッチして仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../../app-service-web/app-service-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
