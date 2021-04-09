---
title: Azure PowerShell のサンプル - データ ディスクを接続して使用する
description: このスクリプトでは、PowerShell を使用して Azure 仮想マシン スケール セットを作成し、データ ディスクを接続して準備します。
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 2a2c93578d8cb4b40e504dd83e3f603bffcccaf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89069648"
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-powershell"></a>PowerShell を使用して、仮想マシン スケール セットがあるデータ ディスクを接続して使用する
このスクリプトでは、仮想マシン スケール セットを作成し、データ ディスクを接続して準備します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.ps1 "Create a virtual machine scale set with data disks")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
次のコマンドを実行して、リソース グループ、スケール セット、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | 仮想マシン スケール セットと、仮想ネットワーク、ロード バランサー、NAT 規則などのすべてのサポート リソースを作成します。 |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | 仮想マシン スケール セットに関する情報を取得します。 |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | 基本的な Web アプリケーションをインストールするために、カスタム スクリプト用の VM 拡張機能を追加します。 |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | VM 拡張機能を適用するために、仮想マシン スケール セット モデルを更新します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ
Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。
