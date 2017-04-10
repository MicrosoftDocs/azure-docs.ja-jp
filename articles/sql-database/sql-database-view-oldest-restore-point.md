---
title: "Azure SQL データベースのサービスによって生成されたバックアップから最も古い復元ポイントを確認する | Microsoft Docs"
description: "サービスによって生成されたデータベース バックアップから最も古い復元ポイントを確認する方法のクイック リファレンス"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: ae35dd15c386bbbf4e77a143d245efcd1f44f238


---
# <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-an-azure-sql-database"></a>Azure SQL データベースのサービスによって生成されたバックアップから最も古い復元ポイントを確認する

この操作方法トピックでは、Azure SQL データベースのサービスによって生成されたバックアップから最も古い復元ポイントを確認する方法について説明します。

## <a name="view-the-oldest-restore-point-using-the-azure-portal"></a>Azure Portal を使用して、最も古い復元ポイントを確認する

1. データベースの **[SQL database]** ブレードを開きます。

    ![新しいサンプル データベースのブレード](./media/sql-database-get-started/new-sample-db-blade.png)

2. ツール バーの **[復元]** をクリックします。

    ![ツール バーの [復元]](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. [復元] ブレードで、最も古い復元ポイントを確認します。

    ![最も古い復元ポイント](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery.md)」をご覧ください。
>

## <a name="next-steps"></a>次のステップ

- サービスによって生成された自動バックアップについては、[自動バックアップ](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)に関する記事を参照してください。
- バックアップの長期保存については、[バックアップの長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
- バックアップからの復元については、[バックアップからの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。


<!--HONumber=Dec16_HO2-->


