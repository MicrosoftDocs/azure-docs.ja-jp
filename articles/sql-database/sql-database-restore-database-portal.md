---
title: "Azure Portal: Azure SQL Database の復元 | Microsoft Docs"
description: "Azure SQL Database を復元します (Azure Portal)。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 33b0c9e6-1cd2-4fd9-9b0d-70ecf6e54821
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 49800be1a61e45f64a77f8d2662ca9edeae29de9
ms.lasthandoff: 03/10/2017


---
# <a name="restore-an-azure-sql-database-using-the-azure-portal"></a>Azure Portal を使用して Azure SQL Database を復元する

次の手順では、削除されたデータベースおよび geo 冗長バックアップから、特定の時点に Azure SQL データベースを復元する方法を示します。

## <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>以前の時点に Azure SQL Database を復元する 

> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery-portal.md)」を参照してください。
>

Azure Portal で、復元するデータベースを選択します。

1. [Azure ポータル](https://portal.azure.com)を開きます。
2. 画面の左側で、**[その他のサービス]** > **[SQL データベース]** を選択します。
3. 復元するデータベースをクリックします。
4. データベースのページの上部で **[復元]** を選択します。
   
   ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore.png)
5. **[復元]** ページで、データベースの復元ポイント (UTC での日付と時刻) を選択し、**[OK]** をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. 前の手順で **[OK]** をクリックした後、ページの右上にある通知アイコンをクリックし、**[SQL データベースを復元しています]** という通知をクリックします。
   
    ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. [SQL データベースを復元しています] ページが開き、復元操作の状態に関する情報が表示されます。 項目をクリックすると、その詳細を確認できます。
   
    ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/inprogress.png)


## <a name="restore-a-deleted-azure-sql-database-from-backups"></a>バックアップからの削除された Azure SQL Database の復元
Azure Portal で削除済みデータベースを復元するには:

1. [Azure Portal](https://portal.azure.com) で **[その他のサービス]** > **[SQL Server]** の順にクリックします。
2. 復元するデータベースを包含するサーバーを選択します。
3. 下へスクロールしてサーバー ブレードの **[操作]** セクションに移動し、**[削除済みデータベース]** を選択します。![Azure SQL Database の復元](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. 復元するデータベースを選択します。
5. データベースの名前を指定し、**[OK]** をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>geo 冗長バックアップからの Azure SQL Database の復元

Azure ポータルでデータベースを geo リストアするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 画面の左側で、**[+ 新規]** > **[Databases]** > **[SQL Database]** を選択します。
   
   ![Azure SQL データベースの復元](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. ソースとして **[バックアップ]** を選択し、復元するバックアップを選択します。 データベース名と、データベースの復元先サーバーを指定し、**[作成]** をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-geo-restore-portal/geo-restore.png)

4. ページの右上の通知アイコンをクリックして、復元操作の状態を確認します。

## <a name="next-steps"></a>次のステップ
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)
* より迅速な復旧オプションについては、 [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)  
* 自動バックアップを使用したアーカイブについては、 [データベースのコピー](sql-database-copy.md)


