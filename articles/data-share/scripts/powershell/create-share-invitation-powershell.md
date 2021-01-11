---
title: 'PowerShell スクリプト: Azure Data Share の招待を作成する | Microsoft Docs'
description: この PowerShell スクリプトでは、データ共有の招待を送信します。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 220677df3bd0c221f61efd28a80b05cfd76e2734
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086644"
---
# <a name="use-a-powershell-script-to-monitor-the-usage-of-a-sent-data-share"></a>送信されたデータ共有の使用状況を監視するために PowerShell スクリプトを使用する

この PowerShell スクリプトでは、データ共有の招待を作成します。

## <a name="sample-script"></a>サンプル スクリプト


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| command | Notes |
|---|---|
| [New-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | データ共有の招待を作成します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Data Share のその他の PowerShell サンプル スクリプトについては、[Azure Data Share の PowerShell のサンプル](../../samples-powershell.md)に関する記事をご覧ください。
