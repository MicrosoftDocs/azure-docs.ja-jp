---
title: sqlcmd を使用して接続する
description: sqlcmd コマンド ライン ユーティリティを使用して Synapse SQL プールに接続し、クエリを実行します。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2e76f48466d084d448cceea0490e44041e7d062e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212141"
---
# <a name="connect-to-synapse-sql-pool-with-sqlcmd"></a>sqlcmd を使用して Synapse SQL プールに接続する

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

[sqlcmd][sqlcmd] コマンド ライン ユーティリティを使用して SQL プールに接続し、クエリを実行します。  

## <a name="1-connect"></a>1.接続する

[sqlcmd][sqlcmd] の使用を開始するには、コマンド プロンプトを開いて「**sqlcmd**」と入力し、続けて SQL プール データベースの接続文字列を入力します。 接続文字列では、次のパラメーターが必要になります。

* **サーバー (-S):** サーバー。`<`Server Name`>`.database.windows.net の形式で指定します。
* **データベース (-d):** データベース名。
* **引用符で囲まれた ID の有効化 (-I):** SQL プール インスタンスに接続するには、引用符で囲まれた ID を有効にする必要があります。

SQL Server 認証を使用するには、ユーザー名とパスワードのパラメーターを追加する必要があります。

* **ユーザー (-U):** サーバーのユーザー。`<`User`>` の形式で指定します。
* **パスワード (-P):** ユーザーに関連付けられているパスワード。

たとえば、接続文字列は次のようになります。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Azure Active Directory 統合認証を使用するには、Azure Active Directory パラメーターを追加する必要があります。

* **Azure Active Directory Authentication (-G):** Azure Active Directory を認証に使用します。

たとえば、接続文字列は次のようになります。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Active Directory を使用して認証を行うには、 [Azure Active Directory 認証を有効にする](sql-data-warehouse-authentication.md) 必要があります。

## <a name="2-query"></a>2.クエリ

接続後、インスタンスに対してサポートされているすべての Transact-SQL ステートメントを発行できます。  この例では、クエリは対話モードで送信されます。

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

以下の例は、-Q オプションを使用する方法または SQL を sqlcmd にパイプ処理で渡す方法を使用して、バッチ モードでクエリを実行する方法を示しています。

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>次のステップ

sqlcmd で使用可能なオプションの詳細については、[sqlcmd に関するドキュメント](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)を参照してください。
