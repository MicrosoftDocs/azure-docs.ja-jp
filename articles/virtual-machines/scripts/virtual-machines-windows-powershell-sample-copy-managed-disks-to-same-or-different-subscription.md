---
title: "Azure PowerShell サンプル スクリプト - 管理ディスクを同じまたは別のサブスクリプションにコピー (移動) する | Microsoft Docs"
description: "Azure PowerShell サンプル スクリプト - 管理ディスクを同じまたは別のサブスクリプションにコピー (移動) する"
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
ms.date: 06/06/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 75beb35dc19fa530d9b2c19aed6040f74afafbc0
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>PowerShell で管理ディスクを同じサブスクリプションまたは別のサブスクリプションにコピーする

このスクリプトは、既存の管理ディスクのコピーを同じサブスクリプションまたは別のサブスクリプションに作成します。 新しいディスクは、親の管理ディスクと同じリージョンに作成されます。   

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、コピー元の管理ディスクの ID を使ってコピー先のサブスクリプションに新しい管理ディスクを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | ディスクの作成に使用するディスク構成を作成します。 この構成には、親ディスクのリソース ID および親ディスクと同じ場所が含まれます。  |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | パラメーターとして渡されたディスク構成、ディスク名、リソース グループ名を使ってディスクを作成します。 |


## <a name="next-steps"></a>次のステップ

[管理ディスクから仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../../app-service-web/app-service-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
