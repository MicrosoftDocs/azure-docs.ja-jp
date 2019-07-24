---
title: Azure SQL Data Warehouse への接続 | Microsoft Docs
description: Azure SQL Data Warehouse に接続します。
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 85bce59563cf0424816661c97406060bcbb423ec
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460450"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse への接続
Azure SQL Data Warehouse に接続します。

## <a name="find-your-server-name"></a>サーバー名を検索する
次の例の場合、サーバー名は samplesvr.database.windows.net です。 完全修飾サーバー名を検索するには、次の手順に従います。

1. [Azure Portal][Azure portal] にアクセスします。
2. **[SQL Data Warehouse]** をクリックします。
3. 接続先のデータ ウェアハウスをクリックします。
4. サーバーの完全名を見つけます。
   
    ![Full server name][1]

## <a name="supported-drivers-and-connection-strings"></a>サポートされるドライバーと接続文字列
Azure SQL Data Warehouse では、[ADO.NET][ADO.NET]、[ODBC][ODBC]、[PHP][PHP]、[JDBC][JDBC] がサポートされています。 最新のバージョンとドキュメントを確認するには、これらドライバーのいずれかをクリックしてください。 使用しているドライバーの接続文字列を Azure Portal から自動的に生成するには、上の例にある **[データベース接続文字列の表示]** をクリックします。 以下に、各ドライバーの接続文字列の例を示します。

> [!NOTE]
> 断続的に切断された場合でも接続を保持できるように、接続のタイムアウトを 300 秒に設定することを検討してください。
> 
> 

### <a name="adonet-connection-string-example"></a>ADO.NET 接続文字列の例
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>ODBC 接続文字列の例
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>PHP 接続文字列の例
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>JDBC 接続文字列の例
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>接続の設定
SQL Data Warehouse では、接続とオブジェクトの作成中にいくつかの設定が標準化されます。 これらの設定をオーバーライドすることはできません。設定には次のものがあります。

| データベースの設定 | 値 |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ON |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ON |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>次の手順
Visual Studio を使用して接続とクエリの実行を行うには、[Visual Studio を使用したクエリ][Query with Visual Studio]に関するページをご覧ください。 認証オプションの詳細については、「[Azure SQL Data Warehouse への認証][Authentication to Azure SQL Data Warehouse]」をご覧ください。

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


