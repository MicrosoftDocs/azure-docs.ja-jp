---
title: "Azure PowerShell サンプル スクリプト - 管理ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する | Microsoft Docs"
description: "Azure PowerShell サンプル スクリプト - 管理ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する"
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
ms.openlocfilehash: 064b5355da10fe683563fa078cfafc65080f7ea2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>管理ディスクのスナップショットを PowerShell で同じサブスクリプションまたは別のサブスクリプションにコピーする

このスクリプトは、スナップショットのコピーを同じサブスクリプションまたは別のサブスクリプションに作成します。 データの保存を目的にスナップショットを別のサブスクリプションに移動するには、このスクリプトを使います。 スナップショットを別のサブスクリプションに格納することで、メイン サブスクリプションでの誤削除を防止します。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、コピー元スナップショットの ID を使って、コピー先のサブスクリプションにスナップショットを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmSnapshotConfig](/powershell/module/azurerm.compute/New-AzureRmSnapshotConfig) | スナップショットの作成に使用するスナップショット構成を作成します。 この構成には、親スナップショットのリソース ID および親スナップショットと同じ場所が含まれます。  |
| [New-AzureRmSnapshot](/powershell/module/azurerm.compute/New-AzureRmDisk) | パラメーターとして渡されたスナップショット構成、スナップショット名、リソース グループ名を使ってスナップショットを作成します。 |


## <a name="next-steps"></a>次のステップ

[スナップショットから仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。