---
title: 'PowerShell スクリプト: Azure Lab Services で許可される VM のサイズを設定する | Microsoft Docs'
description: この PowerShell スクリプトは、Azure Lab Services で許可される VM のサイズを設定します。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 0c82e304d3e3d8df1206c7c05883399b74229af7
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192192"
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

| command | メモ |
|---|---|
| Find-AzResource | 指定したパラメーターに基づいて、リソースを検索します。 |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | リソースを取得します。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | リソースを変更します。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | リソースを作成します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Lab Services のその他の PowerShell のサンプル スクリプトについては、[Azure Lab Services の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。
