---
title: "Stretch Database を無効にしてリモート データを戻す | Microsoft Docs"
description: "テーブルの Stretch Database を無効にし、必要に応じてリモート データを戻す方法について説明します。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 08291748-0dfd-4a7d-a6a4-a5618e9c248d
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a140e89058f6a246e80b71248b82d0f6767337cb


---
# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Stretch Database を無効にしてリモート データを戻す
テーブルの Stretch Database を無効にするには、SQL Server Management Studio でテーブルに **[Stretch]** を選択します。 次のいずれかを選択します。

* **無効にする | Azure からデータを戻す**。 Azure から SQL Server にテーブルのリモート データをコピーし、テーブルの Stretch Database を無効にします。 この操作にはデータ転送コストが発生し、キャンセルできません。
* **無効にする | Azure にデータを残す**。 テーブルの Stretch Database を無効にします。  Azure のテーブルのリモート データを破棄します。

Transact-SQL を利用し、テーブルまたはデータベースの Stretch Database を無効にすることもできます。

テーブルの Stretch Database を無効にすると、データ移行が停止し、クエリ結果にリモート テーブルからの結果が含まれなくなります。

データ移行を一時停止する場合は、 [Stretch Database の一時停止と再開](sql-server-stretch-database-pause.md)に関する記事をご覧ください。

> [!NOTE]
> テーブルまたはデータベースの Stretch Database を無効にしても、リモート オブジェクトは削除されません。 リモート テーブルまたはリモート データベースを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。 リモート オブジェクトを削除するまで、Azure のコストが引き続き発生します。 詳細については、「 [SQL Server Stretch Database の価格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)」をご覧ください。
> 
> 

## <a name="disable-stretch-database-for-a-table"></a>テーブルの Stretch Database を無効にする
### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>SQL Server Management Studio を使用し、テーブルの Stretch Database を無効にします。
1. SQL Server Management Studio のオブジェクト エクスプローラーで、Stretch Database を無効にするテーブルを選択します。
2. 右クリックして **[Stretch]** を選択し、次のいずれかを選択します。
   
   * **無効にする | Azure からデータを戻す**。 Azure から SQL Server にテーブルのリモート データをコピーし、テーブルの Stretch Database を無効にします。 このコマンドはキャンセルできません。
     
     > [!NOTE]
     > Azure から SQL Server にテーブルのリモート データをコピーすると、データ転送コストが発生します。 詳細については、「 [Data Transfers (データ転送) の料金詳細](https://azure.microsoft.com/pricing/details/data-transfers/)」をご覧ください。
     > 
     > 
     
     Azure から SQL Server にすべてのリモート データがコピーされると、テーブルの Stretch が無効になります。
   * **無効にする | Azure にデータを残す**。 テーブルの Stretch Database を無効にします。  Azure のテーブルのリモート データを破棄します。
   
   > [!NOTE]
   > テーブルの Stretch Database を無効にしても、リモート データおよびリモート テーブルは削除されません。 リモート テーブルを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。 リモート テーブルを削除するまで、Azure のコストが引き続き発生します。 詳細については、「 [SQL Server Stretch Database の価格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)」をご覧ください。
   > 
   > 

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Transact-SQL を利用し、テーブルの Stretch Database を無効にする
* テーブルの Stretch を無効にして Azure から SQL Server にテーブルのリモート データをコピーするには、次のコマンドを実行します。 Azure から SQL Server にすべてのリモート データがコピーされると、テーブルの Stretch が無効になります。
  
  このコマンドはキャンセルできません。
  
  ```tsql
  USE <Stretch-enabled database name>;
  GO
  ALTER TABLE <Stretch-enabled table name>  
     SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
  GO
  ```
  > [!NOTE]
  > Azure から SQL Server にテーブルのリモート データをコピーすると、データ転送コストが発生します。 詳細については、「 [Data Transfers (データ転送) の料金詳細](https://azure.microsoft.com/pricing/details/data-transfers/)」をご覧ください。
  > 
  > 
* テーブルの Stretch を無効にしてリモート データを破棄するには、次のコマンドを実行します。
  
  ```tsql
  ALTER TABLE <table_name>
     SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
  ```

> [!NOTE]
> テーブルの Stretch Database を無効にしても、リモート データおよびリモート テーブルは削除されません。 リモート テーブルを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。 リモート テーブルを削除するまで、Azure のコストが引き続き発生します。 詳細については、「 [SQL Server Stretch Database の価格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)」をご覧ください。
> 
> 

## <a name="disable-stretch-database-for-a-database"></a>データベースの Stretch Database を無効にする
データベースの Stretch Database を無効にする前に、データベースの個々の Stretch 対応テーブルで Stretch Database を無効にする必要があります。

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>SQL Server Management Studio を使用し、データベースの Stretch Database を無効にする
1. SQL Server Management Studio のオブジェクト エクスプローラーで、Stretch Database を無効にするデータベースを選択します。
2. 右クリックして **[タスク]** を選択します。次に、**[Stretch]** を選択し、**[無効化]** を選択します。

> [!NOTE]
> データベースの Stretch Database を無効にしても、リモート データベースは削除されません。 リモート データベースを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。 リモート データベースを削除するまで、Azure のコストが引き続き発生します。 詳細については、「 [SQL Server Stretch Database の価格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)」をご覧ください。
> 
> 

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Transact\-SQL を利用し、データベースの Stretch Database を無効にする
次のコマンドを実行します。

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

> [!NOTE]
> データベースの Stretch Database を無効にしても、リモート データベースは削除されません。 リモート データベースを削除する場合は、Microsoft Azure 管理ポータルを使用して削除する必要があります。 リモート データベースを削除するまで、Azure のコストが引き続き発生します。 詳細については、「 [SQL Server Stretch Database の価格](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)」をご覧ください。
> 
> 

## <a name="see-also"></a>関連項目
[ALTER DATABASE SET のオプション (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Stretch Database を一時停止し、再開します。](sql-server-stretch-database-pause.md)




<!--HONumber=Nov16_HO3-->


