---
title: PowerShell で Azure Analysis Services を管理する | Microsoft Docs
description: サーバーの作成、操作の中断、サービス レベルの変更などの一般的な管理タスクのための Azure Analysis Services PowerShell コマンドレットについて説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3e64ffe5007d27a44167f08807a9694875fe48c4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050442"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>PowerShell で Azure Analysis Services を管理する

この記事では、Azure Analysis Services サーバーおよびデータベース管理タスクを実行するときに使用する PowerShell コマンドレットについて説明します。 

サーバーの作成または削除、操作の中断または再開、サービスレベル（層）の変更などのサーバー リソース管理タスクでは、Azure Analysis Services コマンドレットを使用します。 ロール メンバーの追加や削除、処理、パーティション分割など、その他のデータベース管理タスクでは、SQL Server Analysis Services と同じ SqlServer モジュールに含まれるコマンドレットが使われます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>アクセス許可

ほとんどの PowerShell タスクでは、管理している Analysis Services サーバーに対する管理者権限が必要となります。 スケジュールされた PowerShell タスクは無人操作です。 スケジューラを実行するアカウントまたはサービス プリンシパルには、Analysis Services サーバーに対する管理者特権が必要です。 

Azure PowerShell コマンドレットを使用したサーバー操作の場合、自分のアカウントまたはスケジューラを実行するアカウントが、[Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) でリソースの所有者ロールに属していることも必要になります。 

## <a name="resource-and-server-operations"></a>リソースとサーバーの操作 

モジュールのインストール - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
ドキュメント - [Az.AnalysisServices リファレンス](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>データベース操作

Azure Analysis Services のデータベース操作では、SQL Server Analysis Services と同じ SqlServer モジュールが使われます。 ただし、すべてのコマンドレットが、Azure Analysis Services でサポートされているわけではありません。 

SqlServer モジュールには、タスク固有のデータベース管理コマンドレットと、Tabular Model Scripting Language (TMSL) クエリまたはスクリプトを受け入れる汎用 Invoke-ASCmd コマンドレットが用意されています。 以下の SqlServer モジュールのコマンドレットは、Azure Analysis Services でサポートされます。

モジュールのインストール - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
ドキュメント - [SqlServer リファレンス](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>サポートされているコマンドレット

|コマンドレット|説明|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|データベース ロールにメンバーを追加します。| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Analysis Services データベースをバックアップします。|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|データベース ロールからメンバーを削除します。|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|TMSL スクリプトを実行します。|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|データベースを処理します。|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|パーティションを処理します。| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|テーブルを処理します。|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|パーティションをマージします。|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Analysis Services データベースを復元します。| 
  

## <a name="related-information"></a>関連情報

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [SQL Server PowerShell モジュールのダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS のダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [PowerShell ギャラリーの Sql Server モジュール](https://www.powershellgallery.com/packages/SqlServer)    
* [テーブル モデルのプログラミング互換性レベル 1200 以降](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
