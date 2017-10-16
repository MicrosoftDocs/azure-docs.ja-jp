---
title: "Azure PowerShell サンプル スクリプト -  同じまたは別のサブスクリプションのストレージ アカウントに VHD ファイルから管理ディスクを作成する | Microsoft Docs"
description: "Azure PowerShell サンプル スクリプト -  同じまたは別のサブスクリプションのストレージ アカウントに VHD ファイルから管理ディスクを作成する"
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
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 39bc737d2c0fda2de42ae6d87358d0fb7fecbeca
ms.contentlocale: ja-jp
ms.lasthandoff: 09/20/2017

---

# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>PowerShell で同じまたは別のサブスクリプションのストレージ アカウントに VHD ファイルから管理ディスクを作成する

このスクリプトでは、同じまたは別のサブスクリプションのストレージ アカウントに VHD ファイルから管理ディスクを作成します。 sysprep された汎用的な VHD ではなく特殊化された VHD を OS の管理ディスクにインポートして仮想マシンを作成するには、このスクリプトを使います。 また、データの管理ディスクにデータ VHD をインポートするときにも使うことができます。 

短時間に 1 つの VHD ファイルからまったく同じ複数の管理ディスクを作成することは避けてください。 vhd ファイルから管理ディスクを作成する際は、vhd ファイルの BLOB スナップショットが作成され、それを使って管理ディスクが作成されます。 すぐに作成できる BLOB スナップショットは 1 つだけであるため、ディスク作成でスロットルに起因するエラーが発生します。 短時間に複数の管理ディスクを作成する場合は、このスロットルを回避するために、[vhd ファイルから管理スナップショット](virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)を作成したうえで、その管理スナップショットを使って管理ディスクを作成するようにしてください。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを使って別のサブスクリプションに VHD から管理ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | ディスクの作成に使用するディスク構成を作成します。 この構成には、ストレージの種類、場所、親 VHD が格納されているストレージ アカウントのリソース ID、親 VHD の URI が含まれます。 |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | パラメーターとして渡されたディスク構成、ディスク名、リソース グループ名を使ってディスクを作成します。 |

## <a name="next-steps"></a>次のステップ

[管理ディスクを OS ディスクとしてアタッチして仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
