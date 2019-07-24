---
title: Azure PowerShell のサンプル - データ ディスクを接続して使用する | Microsoft Docs
description: Azure PowerShell のサンプル
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0d0a3e949f4803470a6fc2318ab222e20d8eccae
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976812"
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-powershell"></a>PowerShell を使用して、仮想マシン スケール セットがあるデータ ディスクを接続して使用する
このスクリプトでは、仮想マシン スケール セットを作成し、データ ディスクを接続して準備します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.ps1 "Create a virtual machine scale set with data disks")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
次のコマンドを実行して、リソース グループ、スケール セット、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | 仮想マシン スケール セットと、仮想ネットワーク、ロード バランサー、NAT 規則などのすべてのサポート リソースを作成します。 |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | 仮想マシン スケール セットに関する情報を取得します。 |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | 基本的な Web アプリケーションをインストールするために、カスタム スクリプト用の VM 拡張機能を追加します。 |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | VM 拡張機能を適用するために、仮想マシン スケール セット モデルを更新します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次の手順
Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン スケール セット用の PowerShell サンプル スクリプトは、[Azure 仮想マシン スケール セットのドキュメント](../powershell-samples.md)にあります。
