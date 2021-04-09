---
title: Azure PowerShell のサンプル - ホスト ベースの自動スケールを有効にする
description: このスクリプトでは、Windows Server 2016 を実行する仮想マシン スケール セットを作成し、ホスト ベースのメトリックを使用して、CPU 負荷の変化に合わせて自動的にスケールします。
author: ju-shim
ms.author: jushiman
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/25/2020
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: 9530f34ee919547049df06fb0974971c3ba4b2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079627"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>PowerShell を使用して仮想マシン スケール セットを自動的にスケールする
このスクリプトでは、Windows Server 2016 を実行する仮想マシン スケール セットを作成し、ホスト ベースのメトリックを使用して、CPU 負荷の変化に合わせて自動的にスケールします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>サンプル スクリプト


[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.ps1 "Automatically scale a virtual machine scale set")]

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
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | ロード バランサーによって使用される、割り当てられたパブリック IP アドレスについての情報を取得します。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ
Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

