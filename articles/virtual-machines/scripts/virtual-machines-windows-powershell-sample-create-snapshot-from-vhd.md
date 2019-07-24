---
title: Azure PowerShell サンプル スクリプト - VHD からスナップショットを作成してまったく同じ複数のマネージド ディスクを短時間で作成する | Microsoft Docs
description: Azure PowerShell サンプル スクリプト - VHD からスナップショットを作成してまったく同じ複数のマネージド ディスクを短時間で作成する
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 970ff0594ee5a4f8db0f58044c94cc928ee29f43
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978819"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>PowerShell で VHD からスナップショットを作成してまったく同じ複数のマネージド ディスクを短時間で作成する

このスクリプトでは、同じまたは別のサブスクリプションのストレージ アカウントに VHD ファイルからスナップショットを作成します。 sysprep された汎用的な VHD ではなく特殊化された VHD をインポートしてスナップショットを作成し、そのスナップショットを使ってまったく同じ複数のマネージド ディスクを短時間で作成するには、このスクリプトを使います。 また、データ VHD をスナップショットにインポートし、そのスナップショットを使って複数のマネージド ディスクを短時間で作成する際にも使うことができます。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを使って別のサブスクリプションに VHD からマネージド ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | ディスクの作成に使用するディスク構成を作成します。 この構成には、ストレージの種類、場所、親 VHD が格納されているストレージ アカウントのリソース ID、親 VHD の URI が含まれます。 |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | パラメーターとして渡されたディスク構成、ディスク名、リソース グループ名を使ってディスクを作成します。 |

## <a name="next-steps"></a>次の手順

[スナップショットからマネージド ディスクを作成する](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[マネージド ディスクを OS ディスクとして接続することで仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
