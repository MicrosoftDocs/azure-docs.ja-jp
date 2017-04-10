---
title: "Azure Portal: Azure SQL Database を BACPAC ファイルにエクスポートする | Microsoft Docs"
description: "Azure Portal を使って BACPAC ファイルに Azure SQL Database をエクスポートする"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: ed983134e468a69c0e89387a3211479630a278e3


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Azure Portal を使って BACPAC ファイルに Azure SQL Database をエクスポートする

この記事では、[Azure Portal](https://portal.azure.com) を使って、Azure SQL Database を (Azure Blob Storage に格納されている) BACPAC ファイルにエクスポートする方法について説明します。 BACPAC ファイルへのエクスポートの概要については、[BACPAC へのエクスポート](sql-database-export.md)に関する記事をご覧ください。

> [!NOTE]
> また、[SQL Server Management Studio](sql-database-export-ssms.md)、[PowerShell](sql-database-export-powershell.md) または [SQLPackage](sql-database-export-sqlpackage.md) を使用して、Azure SQL データベース フィルを BACPAC ファイルにエクスポートすることもできます。
>

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Azure サブスクリプション。
* Azure SQL Database。 
* [Azure Standard Storage アカウント](../storage/storage-create-storage-account.md) (標準的なストレージに BACPAC を格納する BLOB コンテナーを含む)。

## <a name="export-your-database"></a>データベースのエクスポート

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. エクスポートするデータベースの [SQL Database] ブレードを開きます。
3. エクスポート中にトランザクションが発生しないようにします。 

   > [!IMPORTANT]
   > トランザクションに関する BACPAC ファイルの一貫性を保証するには、[データベースのコピーを作成](sql-database-copy.md)してエクスポートすることをお勧めします。 
   > 

4. **[SQL Database]**をクリックします。
5. アーカイブするデータベースをクリックします。
6. [SQL Database] ブレードで、**[エクスポート]** をクリックして **[データベースのエクスポート]** ブレードを開きます。
   
   ![[エクスポート] ボタン][1]
7. **[Storage]** をクリックし、ストレージ アカウントと BACPAC が格納される BLOB コンテナーを選択します。
   
   ![[データベースのエクスポート]][2]
8. 認証の種類を選択します。 
9. エクスポートするデータベースを含む Azure SQL Server の適切な認証資格情報を入力します。
10. **[OK]** をクリックしてデータベースをエクスポートします。 **[OK]** をクリックすると、データベースのエクスポート要求が作成され、それがサービスに送信されます。 エクスポートの所要時間の長さは、サイズ、データベースの複雑さ、およびサービス レベルによって異なります。 
11. 受信した通知を確認します。
   
   ![通知のエクスポート][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>エクスポート操作の進行状況の監視
1. **[SQL Server]**をクリックします。
2. アーカイブした元 (ソース) のデータベースを含むサーバーをクリックします。
3. 下にスクロールして、[操作] に移動します。
4. [SQL Server] ブレードで、 **[インポート/エクスポート履歴]**をクリックします。
   
   ![インポート/エクスポート履歴][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>BACPAC がストレージ コンテナーにあることの確認
1. **[ストレージ アカウント]**をクリックします。
2. BACPAC アーカイブを格納したストレージ アカウントをクリックします。
3. **[コンテナー]** をクリックし、データベースをエクスポートしたコンテナーを選択して詳細を参照します (ここから BACPAC をダウンロードして保存できます)。
   
   ![.bacpac ファイルの詳細][5]    

## <a name="next-steps"></a>次のステップ

* データベースをアーカイブ目的でエクスポートする方法の代わりとしての、Azure SQL データベース バックアップの長期バックアップ リテンション期間については、[長期バックアップ リテンション期間](sql-database-long-term-retention.md)に関する記事をご覧ください。
* SQL Server データベースへの BACPAC のインポートについては、 [SQL Server データベースへの BACPAC のインポート](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png




<!--HONumber=Feb17_HO2-->


