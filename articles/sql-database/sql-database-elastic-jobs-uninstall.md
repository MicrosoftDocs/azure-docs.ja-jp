---
title: "エラスティック データベース ジョブ ツールのアンインストール方法"
description: "エラスティック データベース ジョブ ツールのアンインストール方法"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: bfc9d820-edbd-4fca-bfbf-1f339cfcc448
ms.service: sql-database
ms.workload: sql-database
ms.custom: multiple databases
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: aa438680348748a771f9016ecdcc68372c6c5e86


---
# <a name="uninstall-elastic-database-jobs-components"></a>エラスティック データベース ジョブ コンポーネントのアンインストール
**エラスティック データベース ジョブ** コンポーネントのアンインストールには、ポータルまたは PowerShell を使用できます。

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Azure ポータルを使用してエラスティック データベース ジョブ コンポーネントをアンインストールする
1. [Azure ポータル](https://portal.azure.com/)を開きます。
2. **エラスティック データベース ジョブ** コンポーネントを含むサブスクリプション (つまりエラスティック データベース ジョブ コンポーネントをインストールしたサブスクリプション) に移動します。
3. **[参照]** をクリックして、**[リソース グループ]** をクリックします。
4. "__ElasticDatabaseJob" という名前のリソース グループを選択します。
5. リソース グループを削除します。

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>PowerShell を使用して Elastic Database ジョブ コンポーネントをアンインストールする
1. Microsoft Azure PowerShell コマンド ウィンドウを開き、 **cd tools**と入力して、Microsoft.Azure.SqlDatabase.Jobs.x.x.xxｘx.x フォルダー以下の tools サブディレクトリに移動します。
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools
2. .\UninstallElasticDatabaseJobs.ps1 PowerShell スクリプトを実行します。
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

または、単純に、コンポーネントのインストール時に使用された既定値があるという想定で、次のスクリプトを実行します。

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
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>次のステップ
エラスティック データベース ジョブを再インストールする方法については、「 [エラスティック データベース ジョブ コンポーネントのインストール](sql-database-elastic-jobs-service-installation.md)

エラスティック データベース ジョブの概要については、「 [エラスティック データベース ジョブの概要](sql-database-elastic-jobs-overview.md)」を参照してください。

<!--Image references-->





<!--HONumber=Nov16_HO3-->


