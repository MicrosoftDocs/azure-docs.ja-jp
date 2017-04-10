---
title: "Azure SQL Data Warehouse の復元 (REST API) | Microsoft Docs"
description: "Azure SQL Data Warehouse を復元するための REST API タスク。"
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: a957a70be915459baa8c687c92e251c6011b6172
ms.openlocfilehash: 7bd71144cd2c96fcfb6636ca8d24fc354f86584d
ms.lasthandoff: 12/06/2016


---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Azure SQL Data Warehouse の復元 (REST API)
> [!div class="op_single_selector"]
> * [概要][Overview]
> * [ポータル][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

この記事では、REST API を使用して Azure SQL Data Warehouse を復元する方法について説明します。

## <a name="before-you-begin"></a>開始する前に
**DTU 容量を確認します。** 各 SQL Data Warehouse は、既定の DTU クォータが割り当てられている SQL サーバー (例: myserver.database.windows.net) でホストされます。  SQL Data Warehouse を復元する前に、データベースの復元に必要な量の DTU クォータがSQL server に残っていることを確認してください。 必要な DTU を計算する方法と DTU を要求する方法については、[DTU クォータの変更の要求][Request a DTU quota change]に関するトピックをご覧ください。

## <a name="restore-an-active-or-paused-database"></a>アクティブまたは一時停止中のデータベースを復元する
データベースを復元するには:

1. 「データベース復元ポイントの取得」操作を使用して、データベースの復元ポイントの一覧を取得します。
2. 「[データベースの復元要求の作成][Create database restore request]」の操作を使用して、復元を開始します。
3. 「[データベース操作の状態][Database operation status]」の操作を使用して、復元の状態を追跡します。

> [!NOTE]
> 復元が完了したら、「[復旧後のデータベースの構成][Configure your database after recovery]」の手順に従って、復旧されたデータベースを構成できます。
> 
> 

## <a name="restore-a-deleted-database"></a>削除されたデータベースの復元
削除されたデータベースを復元するには:

1. 「[削除された復元可能なデータベースの一覧表示][List restorable dropped databases]」の操作を使用して、復元可能なすべての削除済みデータベースの一覧を表示します。
2. 「[削除された復元可能なデータベースの取得][Get restorable dropped database]」の操作を使用して、復元する削除済みデータベースの詳細を取得します。
3. 「[データベースの復元要求の作成][Create database restore request]」の操作を使用して、復元を開始します。
4. 「[データベース操作の状態][Database operation status]」の操作を使用して、復元の状態を追跡します。

> [!NOTE]
> 復元が完了した後にデータベースを構成する方法については、「[復旧後のデータベースの構成][Configure your database after recovery]」をご覧ください。
> 
> 

## <a name="next-steps"></a>次のステップ
Azure SQL Database の各エディションのビジネス継続性機能については、[Azure SQL Database のビジネス継続性の概要][Azure SQL Database business continuity overview]に関する記事をご覧ください。

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps

