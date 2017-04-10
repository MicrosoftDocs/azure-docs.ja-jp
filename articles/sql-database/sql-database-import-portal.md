---
title: "BACPAC ファイルをインポートして Azure SQL Database を作成する | Microsoft Docs"
description: "既存の BACPAC ファイルをインポートして、Azure SQL データベースを作成します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: ca15248d7ac5232e6308d8188332975f1d21c09b
ms.lasthandoff: 03/14/2017


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-using-the-azure-portal"></a>Azure Portal を使用して BACPAC ファイルをインポートして Azure SQL データベースを作成する

この記事は、 [Azure Portal](https://portal.azure.com)を使用して BACPAC ファイルから Azure SQL データベースを作成する手順について説明します。

## <a name="prerequisites"></a>前提条件

BACPAC から SQL Database をインポートするには、以下が必要です。

* Azure サブスクリプション。 
* Azure SQL Database V12 サーバー。 V12 サーバーがない場合は、「 [最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って&1; つ作成してください。
* [Azure Storage アカウント (標準)](../storage/storage-create-storage-account.md) の BLOB コンテナーにインポートするデータベースの .bacpac ファイル。

> [!IMPORTANT]
> BACPAC を Azure Blob Storage からインポートする場合は、Standard Storage を使用します。 Premium Storage からの BACPAC のインポートはサポートされていません。
> 

## <a name="import-the-database"></a>データベースのインポート
次のように、[SQL Server] ブレードを開きます。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. **[SQL Server]**をクリックします。
3. データベースを復元するサーバーをクリックします。
4. [SQL Server] ブレードで、**[データベースのインポート]** をクリックして、**[データベースのインポート]** ブレードを開きます。
   
   ![[データベースのインポート]][1]
5. **[Storage]** をクリックし、ストレージ アカウント、BLOB コンテナー、および .bacpac ファイルを選択して、**[OK]** をクリックします。
   
   ![ストレージ オプションの構成][2]
6. 新しいデータベースの価格レベルを選択し、 **[選択]**をクリックします。 エラスティック プールへのデータベースの直接インポートはサポートされていませんが、最初スタンドアロン データベースとしてインポートしてからプールにデータベースを移動できます。
   
   ![価格レベルの選択][3]
7. **[データベース名]** に BACPAC ファイルから作成するデータベースの名前を入力します。
8. 認証の種類を選択し、サーバーの認証情報を指定します。 
9. **[作成]** をクリックして、BACPAC からデータベースを作成します。
   
   ![データベースの作成][4]

**[作成]** をクリックすると、サービスにデータベースのインポート要求が送信されます。 データベースのサイズに応じて、インポート操作の完了に時間がかかる場合があります。

## <a name="monitor-the-progress-of-the-import-operation"></a>インポート操作の進行状況の監視
1. **[SQL Server]**をクリックします。
2. 復元先のサーバーをクリックします。
3. [SQL Server] ブレードの [操作] 領域で、 **[インポート/エクスポート履歴]**をクリックします。
   
   ![インポート/エクスポート履歴][5]
   ![インポート/エクスポート履歴][6]

4. データベースがサーバーで稼働していることを確認するには、**[SQL データベース]** をクリックし、新しいデータベースが **[オンライン]** であることを確認します。

## <a name="next-steps"></a>次のステップ
* インポートされた SQL データベースへの接続とクエリの実行については、「[SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)」を参照してください。
* BACPAC ファイルを使用した移行に関する SQL Server Customer Advisory Team のブログについては、「[Migrating from SQL Server to Azure SQL Database using BACPAC Files (BACPAC ファイルを使用した SQL Server から Azure SQL Database への移行)](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)」を参照してください。
* パフォーマンスの推奨事項も含む、SQL Server データベースの移行プロセス全体の詳細については、[Azure SQL Database への SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページを参照してください。


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

