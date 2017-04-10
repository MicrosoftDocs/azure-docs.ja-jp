---
title: "Azure Portal: 特定の時点に Azure SQL Database を復元する | Microsoft Docs"
description: "Azure Portal を使って以前の時点に Azure SQL Database を復元します"
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 342df453a2e4cc573117fd59fd5c3aa899295439
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Azure Portal で以前の時点に Azure SQL Database を復元する

この記事では、[SQL Database 自動バックアップ](sql-database-automated-backups.md)から以前の特定の時点にデータベースを復元する方法について説明します。 PowerShell の使用。 このタスクは、[PowerShell を使って](sql-database-point-in-time-restore-powershell.md)行うこともできます。

## <a name="restore-to-a-previous-point-in-time"></a>以前の特定の時点に復元する 

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

## <a name="next-steps"></a>次のステップ
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)


