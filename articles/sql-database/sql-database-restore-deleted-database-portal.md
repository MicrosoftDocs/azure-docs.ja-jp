---
title: "削除された Azure SQL Database の復元 (Azure Portal) | Microsoft Docs"
description: "削除された Azure SQL Database の復元 (Azure Portal)。"
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
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: 9ea5287884cd7f1eb7314002b3ae98c9259686c3
ms.lasthandoff: 02/16/2017


---
# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Azure ポータルを使用した、削除済み Azure SQL データベースの復元

## <a name="select-the-database-to-restore"></a>復元するデータベースの選択
Azure Portal で削除済みデータベースを復元するには:

1. [Azure Portal](https://portal.azure.com) で **[その他のサービス]** > **[SQL Server]** の順にクリックします。
2. 復元するデータベースを包含するサーバーを選択します。
3. 下へスクロールしてサーバー ブレードの **[操作]** セクションに移動し、**[削除済みデータベース]** を選択します。![Azure SQL Database の復元](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. 復元するデータベースを選択します。
5. データベースの名前を指定し、**[OK]** をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="next-steps"></a>次のステップ
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)
* より迅速な復旧オプションについては、 [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)  
* 自動バックアップを使用したアーカイブについては、 [データベースのコピー](sql-database-copy.md)


