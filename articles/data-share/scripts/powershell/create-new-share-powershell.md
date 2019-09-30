---
title: 'PowerShell スクリプト: 新しい Azure Data Share を作成する | Microsoft Docs'
description: この PowerShell スクリプトでは、既存の Data Share アカウント内に新しいデータ共有を作成します。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307258"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>PowerShell を使用して Azure 内にデータ共有を作成する

この PowerShell スクリプトでは、既存の Data Share アカウント内に新しいデータ共有を作成します。

## <a name="sample-script"></a>サンプル スクリプト

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| command | メモ |
|---|---|
| [New-AzDataShare](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | データ共有を作成します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Data Share のその他の PowerShell サンプル スクリプトについては、[Azure Data Share の PowerShell のサンプル](../../samples-powershell.md)に関する記事をご覧ください。
