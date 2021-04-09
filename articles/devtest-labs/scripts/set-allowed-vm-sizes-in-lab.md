---
title: 'PowerShell スクリプト: Azure Lab Services で許可される VM のサイズを設定する | Microsoft Docs'
description: この記事には、Azure Lab Services で許可される仮想マシン (VM) のサイズを設定するサンプル PowerShell スクリプトが含まれています。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998136"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>PowerShell を使用して Azure Lab Services で許可される VM のサイズを設定する

この PowerShell のサンプル スクリプトは、Azure Lab Services で許可される仮想マシン (VM) のサイズを設定します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>前提条件
* **ラボ** 。 スクリプトを実行するには、既存のラボが必要です。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | メモ |
|---|---|
| Find-AzResource | 指定したパラメーターに基づいて、リソースを検索します。 |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | リソースを取得します。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | リソースを変更します。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | リソースを作成します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。

Azure Lab Services のその他の PowerShell のサンプル スクリプトについては、[Azure Lab Services の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。
