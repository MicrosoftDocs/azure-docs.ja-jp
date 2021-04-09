---
title: Synapse SQL に接続する
description: Synapse SQL に接続します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 3165b72ae5612aaeaa66a95299db97182fef2232
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677580"
---
# <a name="connect-to-synapse-sql"></a>Synapse SQL に接続する
Azure Synapse Analytics の Synapse SQL 機能に接続します。

## <a name="supported-tools-for-serverless-sql-pool"></a>サーバーレス SQL プールでサポートされるツール

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) は、バージョン 1.18.0 以降で完全にサポートされます。 SSMS は、バージョン 18.5 以降で部分的にサポートされ、接続してクエリを実行する場合にのみ使用できます。

> [!NOTE]
> クエリの実行時に AAD ログインの接続が 1 時間以上開いたままになっていると、AAD に依存するクエリは失敗します。 これには、AAD パススルーと、AAD とやり取りするステートメント (CREATE EXTERNAL PROVIDER など) を使用したストレージのクエリが含まれます。 これは、SSMS や ADS のクエリ エディターなど、接続を開いたまま保持するすべてのツールに影響します。 Synapse Studio のようにクエリを実行するために新しい接続を開くツールには影響しません。

> この問題を軽減するには、SSMS を再起動するか、ADS で接続および切断します。 

## <a name="find-your-server-name"></a>サーバー名を検索する

次の例の専用 SQL プールのサーバー名は、showdemoweu.sql.azuresynapse.net です。
次の例のサーバーレス SQL プールのサーバー名は、showdemoweu-ondemand.sql.azuresynapse.net です。

完全修飾サーバー名を検索するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. **[Synapse ワークスペース]** を選択します。
3. 接続先のワークスペースを選択します。
4. [概要] に移動します。
5. サーバーの完全名を見つけます。

## <a name="sql-pool"></a>**SQL プール**

![Full server name](./media/connect-overview/server-connect-example.png)

## <a name="serverless-sql-pool"></a>**サーバーレス SQL プール**

![サーバーの完全名 (サーバーレス SQL プール)](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>サポートされるドライバーと接続文字列
Synapse SQL プールでは、[ADO.NET](/dotnet/framework/data/adonet/)、[ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows)、[PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396)、および [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) がサポートされています。 最新のバージョンとドキュメントを確認するには、これらドライバーのいずれかを選択してください。 使用しているドライバーの接続文字列を Azure portal から自動的に生成するには、上の例にある **[データベース接続文字列の表示]** を選択します。 以下に、各ドライバーの接続文字列の例を示します。

> [!NOTE]
> 断続的に切断された場合でも接続を保持できるように、接続のタイムアウトを 300 秒に設定することを検討してください。

### <a name="adonet-connection-string-example"></a>ADO.NET 接続文字列の例

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>ODBC 接続文字列の例

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>PHP 接続文字列の例

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>JDBC 接続文字列の例

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>接続の設定
Synapse SQL では、接続とオブジェクトの作成時に一部の設定が標準化されます。 これらの設定をオーバーライドすることはできません。設定には次のものがあります。

| データベースの設定 | 値 |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?view=azure-sqldw-latest&preserve-view=true) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?view=azure-sqldw-latest&preserve-view=true) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?view=azure-sqldw-latest&preserve-view=true) |7 |

## <a name="recommendations"></a>Recommendations

**サーバーレス SQL プール** クエリを実行するために推奨されるツールは、[Azure Data Studio](get-started-azure-data-studio.md) と Azure Synapse Studio です。

## <a name="next-steps"></a>次のステップ
Visual Studio を使用して接続とクエリを行うには、 [Visual Studio を使用したクエリ](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページをご覧ください。 認証オプションの詳細については、[Synapse SQL に対する認証](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関するページを参照してください。