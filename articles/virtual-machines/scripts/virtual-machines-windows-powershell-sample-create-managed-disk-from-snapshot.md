---
title: "Azure PowerShell サンプル スクリプト - スナップショットから管理ディスクを作成する | Microsoft Docs"
description: "Azure PowerShell サンプル スクリプト - スナップショットから管理ディスクを作成する"
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
ms.openlocfilehash: b475516694d120b7ea05d0892b6789710eec171e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>PowerShell でスナップショットから管理ディスクを作成する

このスクリプトでは、スナップショットから管理ディスクを作成します。 このスクリプトを使用して、OS またはデータ ディスクのスナップショットから仮想マシンを復元します。 OS およびデータ管理ディスクをそれぞれのスナップショットから作成してから、管理ディスクを接続することで新しい仮想マシンを作成します。 スナップショットから作成されたデータ ディスクを接続することで既存の VM のデータ ディスクを復元することもできます。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[メイン](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "スナップショットから管理ディスクを作成する")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを使ってスナップショットから管理ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/Get-AzureRmSnapshot) | スナップショットのプロパティを取得します。  |
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | ディスクの作成に使用するディスク構成を作成します。 この構成には、親スナップショットのリソース ID、親スナップショットと同じ場所、ストレージの種類が含まれます。  |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | パラメーターとして渡されたディスク構成、ディスク名、リソース グループ名を使ってディスクを作成します。 |


## <a name="next-steps"></a>次のステップ

[管理ディスクから仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../../app-service-web/app-service-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
