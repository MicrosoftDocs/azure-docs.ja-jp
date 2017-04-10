---
title: "SSMS: データベースを BACPAC ファイルにエクスポートする (Azure) | Microsoft Docs"
description: "この記事では、SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードを使用して SQL Server データベースを BACPAC ファイルにエクスポートする方法について説明します。"
keywords: "Microsoft Azure SQL Database、データベースの移行、データベースのエクスポート、BACPAC ファイルのエクスポート、データ層アプリケーションのエクスポート ウィザード"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 19c2dab4-81a6-411d-b08a-0ef79b90fbce
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3f0af43c103e34535fd114e33c40da010ea69d69
ms.openlocfilehash: 87d9ae3a5c9c8b8edb02e212f652de5f2f796188


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>SQL Server Management Studio を使用して Azure SQL Database または SQL Server データベースを BACPAC ファイルにエクスポートする        
        
この記事では、SQL Server Management Studio のデータ層アプリケーションのエクスポート ウィザードを使用して Azure SQL Database または SQL Server データベースを [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) ファイルにエクスポートする方法について説明します。 BACPAC ファイルへのエクスポートの概要については、[BACPAC へのエクスポート](sql-database-export.md)に関する記事をご覧ください。    
        
1. 最新バージョンの SQL Server Management Studio があることを確認します。 Management Studio は毎月新しいバージョンに更新されて、Azure ポータルの更新との同期が維持されます。        
           
   > [!IMPORTANT]        
   > 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。 [最新バージョンの SSMS](https://msdn.microsoft.com/library/mt238290.aspx)。        
   >         
         
 2. Management Studio を開き、オブジェクト エクスプローラーで移行元データベースに接続します。        
            
     ![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)        
 3. オブジェクト エクスプローラーで移行元データベースを右クリックし、**[タスク]** をポイントして **[データ層アプリケーションのエクスポート...]** をクリックします。        
            
     ![[タスク] メニューの [データ層アプリケーションのエクスポート]](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)        
 4. エクスポート ウィザードで、BACPAC ファイルがローカル ディスクまたは Azure BLOB に保存されるようにエクスポートを構成します。 エクスポートされた BACPAC には、常にデータベース スキーマ全体が含まれ、既定ではすべてのテーブルのデータが含まれます。 一部またはすべてのテーブルからデータを除外する場合は、[詳細設定] タブを使用してください。 たとえば、すべてのテーブルではなく参照テーブルのデータのみをエクスポートするように選択できます。        
         
     ![設定のエクスポート](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)        
         
    > [!IMPORTANT]        
    > BACPAC を Azure Blob Storage にエクスポートする場合は、Standard Storage を使用します。 Premium Storage からの BACPAC のインポートはサポートされていません。        
    >        
            
## <a name="next-steps"></a>次のステップ        
* [最新バージョンの SSMS](https://msdn.microsoft.com/library/mt238290.aspx)
* SQL Server データベースへの BACPAC のインポートについては、 [SQL Server データベースへの BACPAC のインポート](https://msdn.microsoft.com/library/hh710052.aspx)
* パフォーマンスの推奨事項も含む、SQL Server データベースの移行プロセス全体の詳細については、[Azure SQL Database への SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。
* データベースをアーカイブ目的でエクスポートする方法の代わりとしての、Azure SQL データベース バックアップの長期バックアップ リテンション期間については、[長期バックアップ リテンション期間](sql-database-long-term-retention.md)に関する記事をご覧ください。


    



<!--HONumber=Feb17_HO2-->


