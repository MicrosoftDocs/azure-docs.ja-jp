---
title: sqlcmd を使用して Synapse SQL に接続する
description: sqlcmd コマンド ライン ユーティリティを使用してサーバーレス SQL プールおよび専用 SQL プールに接続し、クエリを実行します。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 7d6675f9584f90b67d8520091dcd4b04dd89e462
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667581"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>sqlcmd を使用して Synapse SQL に接続する

> [!div class="op_single_selector"]
> * [Azure Data Studio)](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

[sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) コマンド ライン ユーティリティを使用して、Synapse SQL 内のサーバーレス SQL プールおよび専用 SQL プールに接続し、クエリを実行することができます。  

## <a name="1-connect"></a>1.接続する
[sqlcmd](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true) の使用を開始するには、コマンド プロンプトを開いて「**sqlcmd**」と入力し、続けて Synapse SQL データベースの接続文字列を入力します。 接続文字列では、次のパラメーターが必要になります。

* **サーバー (-S):** サーバー。`<`Server Name`>`.database.windows.net の形式で指定します。
* **データベース (-d):** データベース名
* **引用符で囲まれた ID の有効化 (-I):** Synapse SQL インスタンスに接続するには、引用符で囲まれた ID を有効にする必要があります。

SQL Server 認証を使用するには、ユーザー名とパスワードのパラメーターを追加する必要があります。

* **ユーザー (-U):** サーバーのユーザー。`<`User`>` の形式で指定します。
* **パスワード (-P):** ユーザーに関連付けられているパスワード

たとえば、接続文字列は次のようになります。

**サーバーレス SQL プール**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**専用 SQL プール**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Azure Active Directory 統合認証を使用するには、Azure Active Directory パラメーターを追加する必要があります。

* **Azure Active Directory Authentication (-G):** Azure Active Directory を認証に使用します。

たとえば、接続文字列は次のようになります。

**サーバーレス SQL プール**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**専用 SQL プール**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Active Directory を使用して認証を行うには、 [Azure Active Directory 認証を有効にする](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 必要があります。

## <a name="2-query"></a>2.クエリ

### <a name="use-dedicated-sql-pool"></a>専用 SQL プールを使用する

接続後、インスタンスに対してサポートされているすべての [Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) ステートメントを発行できます。 この例では、クエリは対話モードで送信されます。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

次の例は、専用 SQL プールに対し、-Q オプションを使用するか、SQL を sqlcmd にパイプ処理で渡して、バッチ モードでクエリを実行する方法を示しています。

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>サーバーレス SQL プールを使用する

接続後、インスタンスに対してサポートされているすべての [Transact-SQL](/sql/t-sql/language-reference?view=azure-sqldw-latest&preserve-view=true) (T-SQL) ステートメントを発行できます。  次の例では、クエリが対話モードで送信されます。

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

次の例は、サーバーレス SQL プールに対し、-Q オプションを使用するか、SQL を sqlcmd にパイプ処理で渡して、バッチ モードでクエリを実行する方法を示しています。

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>次のステップ

sqlcmd のオプションの詳細については、[sqlcmd のドキュメント](/sql/tools/sqlcmd-utility?view=azure-sqldw-latest&preserve-view=true)を参照してください。
