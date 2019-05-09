---
title: エラスティック データベース ジョブ ツールのアンインストール方法
description: Azure ポータルまたは PowerShell を使用してエラスティック データベース ジョブ コンポーネントをアンインストールする方法を説明します。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 12f923724616378a6ea6c83a947bd5362840a697
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768866"
---
# <a name="uninstall-elastic-database-jobs-components"></a>エラスティック データベース ジョブ コンポーネントのアンインストール


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Elastic Database ジョブ** コンポーネントのアンインストールには、ポータルまたは PowerShell を使用できます。

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Azure ポータルを使用してエラスティック データベース ジョブ コンポーネントをアンインストールする
1. [Azure Portal](https://portal.azure.com/)を開きます。
2. **エラスティック データベース ジョブ** コンポーネントを含むサブスクリプション (つまりエラスティック データベース ジョブ コンポーネントをインストールしたサブスクリプション) に移動します。
3. **[参照]** をクリックして、**[リソース グループ]** をクリックします。
4. "__ElasticDatabaseJob" という名前のリソース グループを選択します。
5. リソース グループを削除します。

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>PowerShell を使用して Elastic Database ジョブ コンポーネントをアンインストールする
1. Microsoft Azure PowerShell コマンド ウィンドウを開き、Microsoft.Azure.SqlDatabase.Jobs.x.x.xxｘx.x フォルダー以下の tools サブディレクトリに移動します:(**cd tools** と入力します)。
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools
2. .\UninstallElasticDatabaseJobs.ps1 PowerShell スクリプトを実行します。
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

または、単純に、コンポーネントのインストール時に使用された既定値があるという想定で、次のスクリプトを実行します。

```powershell
$ResourceGroupName = "__ElasticDatabaseJob"
Switch-AzureMode AzureResourceManager

$resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
if(!$resourceGroup)
{
     Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
     return
}

Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
Remove-AzureResourceGroup -Name $ResourceGroupName -Force
Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job components are now uninstalled."
```

## <a name="next-steps"></a>次の手順
エラスティック データベース ジョブを再インストールする方法については、「 [エラスティック データベース ジョブ コンポーネントのインストール](sql-database-elastic-jobs-service-installation.md)

エラスティック データベース ジョブの概要については、「 [エラスティック データベース ジョブの概要](sql-database-elastic-jobs-overview.md)」を参照してください。
