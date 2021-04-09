---
title: Synapse SQL の接続文字列
description: Synapse SQL の接続文字列
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 5ce411f3a2f3be162f9af16422d20b3e8b536fee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120922"
---
# <a name="connection-strings-for-synapse-sql"></a>Synapse SQL の接続文字列

[ADO.NET](/dotnet/framework/data/adonet/)、[ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows)、[PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)、[JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) などの各種アプリケーション プロトコルを使用して、Synapse SQL に接続できます。 各プロトコルの接続文字列の例を以下に示します。 

接続文字列は、Azure ポータルを使用してビルドすることもできます。  Azure portal を使用して接続文字列を作成するには、データベースのブレードに移動して、 *[要点]* の下にある *[データベース接続文字列の表示]* を選択します。

## <a name="sample-adonet-connection-string"></a>ADO.NET 接続文字列の例

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>ODBC 接続文字列の例

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>PHP の接続文字列の例

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>JDBC 接続文字列の決定

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> 短時間の利用不可状態になったときに接続を保持できるように、接続のタイムアウトを 300 秒に設定することを検討してください。

## <a name="recommendations"></a>Recommendations

**サーバーレス SQL プール** クエリを実行するために推奨されるツールは、[Azure Data Studio](get-started-azure-data-studio.md) と Azure Synapse Studio です。

## <a name="next-steps"></a>次のステップ

Visual Studio またはその他のアプリケーションを使用して分析に対するクエリを開始するには、[Visual Studio を使用したクエリ](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページをご覧ください。