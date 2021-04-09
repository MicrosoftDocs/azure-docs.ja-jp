---
title: PowerShell - Azure DevTest Labs でラボにマーケットプレース イメージを追加する
description: この PowerShell スクリプトは、Azure DevTest Labs でラボにマーケットプレース イメージを追加します。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 543f20af270769dd16e4a1ecf6ee93e9259cdfd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022281"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>PowerShell を使用して、Azure DevTest Labs でラボにマーケットプレース イメージを追加します。

このサンプル PowerShell スクリプトは、Azure DevTest Labs でラボにマーケットプレース イメージを追加します。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>前提条件
* **ラボ** 。 スクリプトを実行するには、既存のラボが必要です。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| command | メモ |
|---|---|
| Find-AzResource | 指定したパラメーターに基づいて、リソースを検索します。 |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | リソースを取得します。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | リソースを変更します。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | リソースを作成します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。

Azure Lab Services のその他の PowerShell のサンプル スクリプトについては、[Azure Lab Services の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。
