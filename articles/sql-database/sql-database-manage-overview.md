---
title: "Azure SQL Database の管理ツールと開発ツール | Microsoft Docs"
description: "Azure SQL Database で管理と開発を行うためのツールとオプションについて説明します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 9b0a3a314e576db2133e5c63fada43bb11a4e520
ms.lasthandoff: 03/04/2017


---
# <a name="overview-tools-to-manage--develop-with-azure-sql-database"></a>概要: Azure SQL Database の管理ツールと開発ツール
このトピックでは、Azure SQL Database で管理と開発を行うためのツールを紹介します。

> [!IMPORTANT]
> このドキュメント セットには、クイック スタート、サンプル、および操作ガイドが含まれており、以下のパラグラフで紹介するツールを使って Azure SQL Database で管理と開発を行う方法を示します。 Azure Portal、PowerShell、および T-SQL 向けの特定のコンテンツを探すには、左側のナビゲーション ウィンドウとフィルター ボックスを使用してください。
>

## <a name="azure-portal"></a>Azure ポータル
[Azure ポータル](https://portal.azure.com) は、データベースや論理サーバーを作成、更新、削除したり、データベース アクティビティを監視したりできる、Web ベースのアプリケーションです。 このツールは、Azure を使い始めたばかりの場合、管理対象のデータベースの数が少ない場合、すばやく作業を行う必要がある場合に便利です。

## <a name="sql-server-management-studio-and-transact-sql"></a>SQL Server Management Studio と Transact-SQL
SQL Server Management Studio (SSMS) は、コンピューター上で実行されるクライアント ツールです。Transact-SQL を使用してクラウドのデータベースを管理することができます。 多くのデータベース管理者は、Azure SQL データベースで使用できる SSMS に精通しています。 [SSMS の最新バージョンをダウンロード](https://msdn.microsoft.com/library/mt238290) し、Azure SQL Database で作業する場合は常に最新のリリースを使用してください。 

> [!IMPORTANT]
> 必ず最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) を使用してください。
>  

## <a name="sql-server-data-tools-in-visual-studio"></a>Visual Studio での SQL Server Data Tools
SQL Server Data Tools (SSDT) は、クラウドのデータベース開発に使用できる、コンピューター上で実行されるクライアント ツールです。 Visual Studio やその他の統合開発環境 (IDE) を使い慣れているアプリケーション開発者には、 [Visual Studio の SSDT の使用をお勧めします](https://msdn.microsoft.com/library/mt204009.aspx)。  

> [!IMPORTANT]
> 常に最新バージョンの [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持する必要があります。
>  
## <a name="powershell"></a>PowerShell
PowerShell を使って、データベースやエラスティック プールを管理したり、Azure のリソースのデプロイを自動化したりできます。 Microsoft は、多数のデータベースを管理する場合や運用環境でリソースの変更およびデプロイを自動化する場合にこのツールをお勧めします。

## <a name="elastic-database-tools"></a>Elastic Database ツール
エラスティック データベース ツールを使用して、次の操作を行います。 

* [エラスティック ジョブ](sql-database-elastic-jobs-overview.md)
* [split-merge ツール](sql-database-elastic-scale-overview-split-and-merge.md)
* [エラスティック スケール クライアント ライブラリ](sql-database-elastic-database-client-library.md)を使用して、シングルテナント モデルまたはマルチテナント モデルでデータベースを管理する。

## <a name="additional-resources"></a>その他のリソース
* [Azure リソース マネージャー](https://azure.microsoft.com/features/resource-manager/)
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation/)
* [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)


