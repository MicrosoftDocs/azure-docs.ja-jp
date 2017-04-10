---
title: "SQL Data Warehouse での Transparent Data Encryption (T-SQL) | Microsoft Docs"
description: "SQL Data Warehouse での Transparent Data Encryption (TDE) (T-SQL)"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: barbkess
editor: 
ms.assetid: 8ccefef3-1308-41ee-b336-5e491d1098ae
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: rortloff;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fb915cd9abb83b10af17046591ce313dcc84fd47


---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE) の概要
> [!div class="op_single_selector"]
> * [セキュリティの概要](sql-data-warehouse-overview-manage-security.md)
> * [認証](sql-data-warehouse-authentication.md)
> * [暗号化 (ポータル)](sql-data-warehouse-encryption-tde.md)
> * [暗号化 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permssions"></a>必要なアクセス許可
Transparent Data Encryption (TDE) を有効にするには、管理者か dbmanager ロールのメンバーである必要があります。

## <a name="enabling-encryption"></a>暗号化の有効化
SQL Data Warehouse の TDE を有効にするには、次の手順を実行します。

1. 管理者のログインまたは master データベースの *dbmanager* ロールのメンバーであるログインを使用して、データベースをホストしているサーバーの **master** データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>暗号化の無効化
SQL Data Warehouse の TDE を無効にするには、次の手順を実行します。

1. 管理者のログインまたは master データベースの *dbmanager* ロールのメンバーであるログインを使用して、 **master** データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> 一時停止した SQL Data Warehouse は、TDE 設定を変更する前に再開する必要があります。
> 
> 

## <a name="verifying-encryption"></a>暗号化の検証
SQL Data Warehouse の暗号化状態を確認するには、次の手順を実行します。

1. 管理者のログインまたは master データベースの *dbmanager* ロールのメンバーであるログインを使用して、 **master** データベースまたはインスタンス データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

結果が ```1``` の場合はデータベースが暗号化されていることを示し、```0``` の場合は暗号化されていないことを示します。

## <a name="encryption-dmvs"></a>暗号化の DMV
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[透過的なデータ暗号化 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->



<!--HONumber=Nov16_HO3-->


