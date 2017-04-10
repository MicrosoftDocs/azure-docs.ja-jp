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
ms.date: 02/28/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: c0dd85570f052c4a9651faecbaeb3eaa181f9017
ms.openlocfilehash: b00b2cf1d3a54bba716d7470643aa1a5e6a58633
ms.lasthandoff: 03/01/2017


---

# <a name="manage-azure-analysis-services-with-powershell"></a>PowerShell で Azure Analysis Services を管理する

この記事では、Azure Analysis Services サーバーおよびデータベース管理タスクを実行するときに使用する PowerShell コマンドレットについて説明します。 

サーバーの作成/削除、サーバー操作の中断/開始、サービス レベルの変更などのサーバー管理タスクでは、Azure Resource Manager (AzureRM) が使用されます。 ロール メンバーの追加や削除、処理、パーティション分割など、その他のデータベース管理タスクについては、SQL Server Analysis Services と同じ [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) モジュールのコマンドレットが使用されます。

## <a name="permissions"></a>アクセス許可
ほとんどの PowerShell タスクでは、管理している Analysis Services サーバーに対する管理者権限が必要となります。 スケジュールされた PowerShell タスクは無人操作です。 スケジューラを実行するアカウントにも、Analysis Services サーバーに対する管理者権限が必要です。 

AzureRm コマンドレットを使用したサーバー操作の場合、自分のアカウントまたはスケジューラを実行するアカウントが、[Azure のロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-what-is.md) でリソースの所有者ロールに属していることも必要になります。 

## <a name="server-operations"></a>サーバーの操作 
Azure Analysis Services コマンドレットは、[AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) コンポーネント モジュールに含まれます。 AzureRM コマンドレット モジュールをインストールする場合は、PowerShell ギャラリーの [Azure Resource Manager コマンドレット](https://docs.microsoft.com/powershell/resourcemanager/)を確認してください。

|コマンドレット|Description| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/get-azurermanalysisservicesserver)|サーバー インスタンスの詳細を取得します。|  
|[New-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/new-azurermanalysisservicesserver)|新しいサーバー インスタンスを作成します。|
|[Remove-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/remove-azurermanalysisservicesserver)|サーバー インスタンスを削除します。|  
|[Suspend-AzureRmAnalysisServicesServe](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/suspend-azurermanalysisservicesserver)|サーバー インスタンスを中断します。| 
|[Resume-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/resume-azurermanalysisservicesserver)|サーバー インスタンスを再開します。|  
|[Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/set-azurermanalysisservicesserver)|サーバー インスタンスを変更します。|   
|[Test-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.analysisservices/v3.0.0/test-azurermanalysisservicesserver)|サーバー インスタンスの存在をテストします。| 

## <a name="database-operations"></a>データベース操作
Azure Analysis Services のデータベース操作では、SQL Server Analysis Services と同じ [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) モジュールが使用されます。 ただし、すべてのコマンドレットが、Azure Analysis Services プレビューでサポートされているわけではありません。 

SQLASCMDLETS モジュールには、タスク固有のデータベース管理コマンドレットと、Tabular Model Scripting Language (TMSL) クエリまたはスクリプトを受け入れる汎用 Invoke-ASCmd コマンドレットが用意されています。 以下の SQLASCMDLETS モジュールのコマンドレットは、Azure Analysis Services プレビューでサポートされます。
  
|コマンドレット|説明|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|データベース ロールにメンバーを追加します。| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|データベース ロールからメンバーを削除します。|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|TMSL スクリプトを実行します。|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|データベースを処理します。|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|パーティションを処理します。| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|テーブルを処理します。|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|パーティションをマージします。|  
  

## <a name="related-information"></a>関連情報
* [Analysis Services の PowerShell スクリプト](https://msdn.microsoft.com/library/hh213141.aspx)。
* [互換性レベル 1200 の表形式モデル プログラミング](https://msdn.microsoft.com/library/mt712541.aspx)
