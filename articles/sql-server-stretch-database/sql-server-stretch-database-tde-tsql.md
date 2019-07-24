---
title: Stretch Database TSQL に対する Transparent Data Encryption の有効化 - Azure | Microsoft Docs
description: Azure TSQL での SQL Server Stretch Database に対する Transparent Data Encryption (TDE) の有効化
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: douglasl
ms.openlocfilehash: 634e1b097f2ca16a8c52da02445b3f5f2af7ff3d
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744787"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Azure (Transact-SQL) での Stretch Database に対する Transparent Data Encryption (TDE) の有効化
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

Transparent Data Encryption (TDE) を使用すると、データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、悪意のあるアクティビティの脅威からデータを保護できます。アプリケーションを変更する必要はありません。

TDE は、データベース暗号化キーと呼ばれる対称キーを使用してデータベース全体のストレージを暗号化します。 データベース暗号化キーは組み込まれているサーバー証明書によって保護されます。 組み込みのサーバー証明書は、Azure サーバーごとに一意です。 Microsoft は、少なくとも 90 日ごとにこれらの証明書を自動的にローテーションします。 TDE の一般的な説明については、「 [透過的なデータ暗号化 (TDE)]」を参照してください。

## <a name="enabling-encryption"></a>暗号化の有効化
Stretch 対応 SQL Server データベースから移行したデータを格納している Azure データベースの TDE を有効にするには、次の操作を行います。

1. 管理者のログインまたは master データベースの *dbmanager* ロールのメンバーであるログインを使用して、データベースをホストしている Azure サーバーの **master** データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>暗号化の無効化
Stretch 対応 SQL Server データベースから移行したデータを格納している Azure データベースの TDE を無効にするには、次の操作を行います。

1. 管理者のログインまたは master データベースの *dbmanager* ロールのメンバーであるログインを使用して、 **master** データベースに接続します
2. データベースの暗号化するには、次のステートメントを実行します。

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>暗号化の検証
Stretch 対応 SQL Server データベースから移行したデータを格納している Azure データベースの暗号化ステータスを確認するには、次の操作を行います。

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

<!--Anchors-->
[透過的なデータ暗号化 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
