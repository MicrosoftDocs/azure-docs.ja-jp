---
title: Azure SQL Data Warehouse の復元 (Azure Portal) | Microsoft Docs
description: Azure SQL Data Warehouse を復元するための Azure ポータル タスク。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2d59235b067d9571bc8b64c33799431be6489502
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087212"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Azure SQL Data Warehouse の復元 (Portal)
> [!div class="op_single_selector"]
> * [概要][Overview]
> * [ポータル][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 
> この記事では、Azure Portal を使用して Azure SQL Data Warehouse を復元する方法について説明します。

## <a name="before-you-begin"></a>開始する前に
**DTU 容量を確認します。** 各 SQL Data Warehouse インスタンスは、既定の DTU クォータが割り当てられている SQL サーバー (例: myserver.database.windows.net) でホストされます。 SQL Data Warehouse を復元する前に、データベースの復元に必要な量の DTU クォータがSQL Server に残っていることを確認してください。 必要な DTU クォータを計算する方法と DTU を要求する方法については、[DTU クォータの変更の要求][Request a DTU quota change]に関するトピックをご覧ください。

## <a name="restore-an-active-or-paused-database"></a>アクティブまたは一時停止中のデータベースを復元する
データベースを復元するには:

1. [Azure Portal][Azure portal] にサインインします。
2. 画面の左側にある **[参照]** をクリックし、**[SQL Server]** を選択します。

    ![[参照] > [SQL Server] を選択](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. 使用しているサーバーを見つけて選択します。

    ![サーバーを選択](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. 復元する SQL Data Warehouse インスタンスを見つけて選択します。

    ![復元する SQL Data Warehouse のインスタンスを選択](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Data Warehouse ブレードの上部にある **[復元]** を選択します。

    ![[復元] を選択](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. 新しい**データベース名**を指定します。
7. 最新の**復元ポイント**を選択します。

   最新の復元ポイントを選択していることを確認します。 復元ポイントは協定世界時 (UTC) で表示されるため、表示される既定のオプションが最新の復元ポイントでない場合があります。

      ![復元ポイントを選択](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. **[OK]** を選択します。
9. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます。

> [!NOTE]
> 復元が完了したら、「[復旧後のデータベースの構成][Configure your database after recovery]」の手順に従って、復旧されたデータベースを構成できます。
>
>

## <a name="restore-a-deleted-database"></a>削除されたデータベースの復元
削除されたデータベースを復元するには:

1. [Azure Portal][Azure portal] にサインインします。
2. 画面の左側にある **[参照]** をクリックし、**[SQL Server]** を選択します。

    ![[参照] > [SQL Server] を選択](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. 使用しているサーバーを見つけて選択します。

    ![サーバーを選択](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. サーバーのブレードで下にスクロールして **[操作]** セクションに移動します。
5. **[削除済みデータベース]** タイルを選択します。

    ![[削除済みデータベース] タイルを選択](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. 復元する削除済みデータベースを選択します。

    ![復元するデータベースの選択](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. 新しい**データベース名**を指定します。

    ![データベースの名前を追加](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. **[OK]** を選択します。
9. データベースの復元処理が開始され、**[通知]** を使用して処理を監視することができます。

> [!NOTE]
> 復元が完了した後にデータベースを構成する方法については、「[復旧後のデータベースの構成][Configure your database after recovery]」をご覧ください。
>
>

## <a name="next-steps"></a>次の手順
Azure SQL Database の各エディションのビジネス継続性機能については、[Azure SQL Database のビジネス継続性の概要][Azure SQL Database business continuity overview]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
