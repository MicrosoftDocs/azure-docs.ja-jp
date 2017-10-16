---
title: "PowerShell で Azure Analysis Services を管理する | Microsoft Docs"
description: "PowerShell を使用した Azure Analysis Services 管理。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: owend
ms.openlocfilehash: 95593053950f96a83e093c29516e9f66ebad53bf
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2017
---
# <a name="manage-azure-analysis-services-with-powershell"></a>PowerShell で Azure Analysis Services を管理する

この記事では、Azure Analysis Services サーバーおよびデータベース管理タスクを実行するときに使用する PowerShell コマンドレットについて説明します。 

サーバーの作成/削除、サーバー操作の中断/開始、サービス レベルの変更などのサーバー管理タスクでは、Azure Resource Manager (AzureRM) が使用されます。 ロール メンバーの追加や削除、処理、パーティション分割など、その他のデータベース管理タスクでは、SQL Server Analysis Services と同じ SqlServer モジュールに含まれるコマンドレットが使われます。

## <a name="permissions"></a>アクセス許可
ほとんどの PowerShell タスクでは、管理している Analysis Services サーバーに対する管理者権限が必要となります。 スケジュールされた PowerShell タスクは無人操作です。 スケジューラを実行するアカウントにも、Analysis Services サーバーに対する管理者権限が必要です。 

AzureRm コマンドレットを使用したサーバー操作の場合、自分のアカウントまたはスケジューラを実行するアカウントが、[Azure のロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-what-is.md) でリソースの所有者ロールに属していることも必要になります。 

## <a name="server-operations"></a>サーバーの操作 
Azure Analysis Services コマンドレットは、[AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) コンポーネント モジュールに含まれます。 AzureRM コマンドレット モジュールをインストールする場合は、PowerShell ギャラリーの [Azure Resource Manager コマンドレット](/powershell/azure/overview)を確認してください。

|コマンドレット|Description| 
|------------|-----------------| 
|[Export-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/export-azureanalysisservicesinstancelog)|ログをファイルにエクスポートします。| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|サーバー インスタンスの詳細を取得します。|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|サーバー インスタンスを作成します。|
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|サーバー インスタンスを削除します。|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|サーバー インスタンスを中断します。| 
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|サーバー インスタンスを再開します。|  
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|サーバー インスタンスを変更します。|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|サーバー インスタンスの存在をテストします。| 

## <a name="database-operations"></a>データベース操作

Azure Analysis Services のデータベース操作では、SQL Server Analysis Services と同じ [SqlServer](https://www.powershellgallery.com/packages/SqlServer) モジュールが使われます。 ただし、すべてのコマンドレットが、Azure Analysis Services でサポートされているわけではありません。 

SqlServer モジュールには、タスク固有のデータベース管理コマンドレットと、Tabular Model Scripting Language (TMSL) クエリまたはスクリプトを受け入れる汎用 Invoke-ASCmd コマンドレットが用意されています。 以下の SqlServer モジュールのコマンドレットは、Azure Analysis Services でサポートされます。

  
|コマンドレット|説明|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|データベース ロールにメンバーを追加します。| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Analysis Services データベースをバックアップします。|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|データベース ロールからメンバーを削除します。|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|TMSL スクリプトを実行します。|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|データベースを処理します。|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|パーティションを処理します。| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|テーブルを処理します。|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|パーティションをマージします。|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Analysis Services データベースを復元します。| 
  

## <a name="related-information"></a>関連情報

* [SQL Server PowerShell モジュールのダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SQL Server Management Studio (SSMS) のダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [PowerShell ギャラリーの Sql Server モジュール](https://www.powershellgallery.com/packages/SqlServer)    
* [互換性レベル 1200 以上の表形式モデル プログラミング](https://msdn.microsoft.com/library/mt712541.aspx)
