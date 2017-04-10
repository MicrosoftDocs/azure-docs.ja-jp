---
title: "データ移行の一時停止および再開 (Stretch Database) | Microsoft Docs"
description: "Azure へのデータ移行を一時停止または再開する方法について説明します。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: d45c05ad-254e-4950-a652-3d5cc40ed967
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8b5f4461a20c1407ba2ce834ec6dd970f4e7544


---
# <a name="pause-and-resume-data-migration-stretch-database"></a>データ移行の一時停止および再開 (Stretch Database)
Azure へのデータ移行を一時停止または再開するには、SQL Server Management Studio でテーブルの **[Stretch]** を選択してから、**[一時停止]** を選択してデータ移行を一時停止するか、**[再開]** を選択してデータ移行を再開します。 Transact\-SQL を使用してデータ移行を一時停止または再開することもできます。

ローカル サーバー上の問題をトラブルシューティングするか、使用可能なネットワーク帯域幅を最大化する場合は、各テーブルでデータ移行を一時停止します。

## <a name="pause-data-migration"></a>データ移行を一時停止する
### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>SQL Server Management Studio を使用してデータ移行を一時停止する
1. SQL Server Management Studio のオブジェクト エクスプローラーで、データ移行を一時停止する Stretch が有効なテーブルを選択します。
2. 右クリックして **[Stretch]** を選択し、**[一時停止]** を選択します。

### <a name="use-transact-sql-to-pause-data-migration"></a>Transact\-SQL を使用してデータ移行を一時停止する
次のコマンドを実行します。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>データ移行を再開する
### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>SQL Server Management Studio を使用してデータ移行を再開する
1. SQL Server Management Studio のオブジェクト エクスプローラーで、データ移行を再開する Stretch が有効なテーブルを選択します。
2. 右クリックして **[Stretch]** を選択し、**[再開]** を選択します。

### <a name="use-transact-sql-to-resume-data-migration"></a>Transact\-SQL を使用してデータ移行を再開する
次のコマンドを実行します。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>移行がアクティブか一時停止しているかを確認する
### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>SQL Server Management Studio を使用して、移行がアクティブか一時停止しているかを確認する
SQL Server Management Studio で **Stretch Database Monitor** を開き、**[移行状態]** 列の値を確認します。 詳細については、「 [データ移行の監視とトラブルシューティング](sql-server-stretch-database-monitor.md)」をご覧ください。

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Transact-SQL を使用して、移行がアクティブか一時停止しているかを確認する
カタログ ビュー **sys.remote_data_archive_tables** に対してクエリを実行し、**is_migration_paused** 列の値を確認します。 詳細については、「[sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx)」をご覧ください。

## <a name="see-also"></a>関連項目
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[データ移行の監視とトラブルシューティング](sql-server-stretch-database-monitor.md)




<!--HONumber=Nov16_HO3-->


