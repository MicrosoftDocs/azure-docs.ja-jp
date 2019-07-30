---
title: Azure SQL Database と SQL Server 用の Spark Connector の使用 | Microsoft Docs
description: Azure SQL Database と SQL Server 用の Spark コネクタを使用する方法について説明します
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 192387958785e4032a1cb549d0ba071fa406a60e
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228220"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Azure SQL Database と SQL Server 用の Spark コネクタを使用してビッグ データのリアルタイム分析を高速化する

Azure SQL Database と SQL Server 用の Spark コネクタを使用して、Azure SQL Database と SQL Server を含む SQL データベースを Spark ジョブの入力データ ソースまたは出力データ シンクとして機能させることができます。 ビッグ データ分析の中でリアルタイム トランザクション データを利用でき、アドホック クエリの結果やレポートを保持できます。 組み込みの JDBC コネクタに比べて、このコネクタには、SQL データベースにデータを一括挿入する機能があります。 行単位の挿入に比べ、パフォーマンスを 10 倍から 20 倍も向上させることができます。 Azure SQL Database と SQL Server 用の Spark コネクタは、AAD 認証もサポートします。 AAD アカウントを使用して Azure Databricks から Azure SQL データベースに安全に接続できます。 組み込みの JDBC コネクタと同様のインターフェイスを備えています。 この新しいコネクタを使用するための既存の Spark ジョブの移行は簡単に実行できます。

## <a name="download"></a>ダウンロード
最初に、GitHub の [azure-sqldb-spark リポジトリ](https://github.com/Azure/azure-sqldb-spark) から Spark to SQL DB コネクタをダウンロードします。

## <a name="official-supported-versions"></a>正式にサポートされているバージョン

| コンポーネント                            |Version                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 以降           |
| Scala                                |2.10 以降            |
| SQL Server 用 Microsoft JDBC ドライバー |6.2 以降             |
| Microsoft SQL Server                 |SQL Server 2008 以降 |
| Azure SQL Database                   |サポートされています                |

Azure SQL Database と SQL Server 用の Spark コネクタは、SQL Server 用の Microsoft JDBC ドライバーを使用して、Spark ワーカー ノードと SQL データベース間でデータを移動します。
 
データフローは次のとおりです。
1. Spark のマスター ノードが、SQL Server または Azure SQL Database に接続し、特定のテーブルまたは特定の SQL クエリを使用してデータを読み込みます。
2. Spark のマスター ノードが、変換のためにワーカー ノードにデータを分散します。 
3. ワーカー ノードが、SQL Server または Azure SQL Database に接続し、データベースにデータを書き込みます。 ユーザーは、使用する挿入方法 (行単位または一括) を選択できます。

次の図にデータ フローを示します。

   ![アーキテクチャ](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Spark to SQL DB コネクタをビルドする
現在、コネクタ プロジェクトでは Maven を使用します。 依存関係なしにコネクタを構築するには、次のコードを実行します。

- mvn クリーン パッケージ
- releases フォルダーから最新バージョンの JAR をダウンロードします
- SQL DB Spark JAR をインクルードします

## <a name="connect-spark-to-sql-db-using-the-connector"></a>コネクタを使用して Spark to SQL DB に接続する
Spark ジョブから Azure SQL Database または SQL Server に接続して、データの読み取りまたは書き込みを実行できます。 Azure SQL データベースまたは SQL Server で DML または DDL クエリを実行することもできます。

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Azure SQL Database または SQL Server からデータを読み取る

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>指定した SQL クエリによる Azure SQL Database または SQL Server からのデータの読み込み
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Azure SQL Database または SQL Server にデータを書き込む
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Azure SQL Database または SQL Server で DML または DDL クエリを実行する
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>AAD 認証を使用して Spark を Azure SQL Database に接続する
Azure Active Directory (AAD) 認証を使用して SQL Database に接続できます。 AAD 認証を使用して、SQL Server 認証の代わりとしてデータベース ユーザーの ＩＤ を一元的に管理します。
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>ActiveDirectoryPassword 認証モードを使用した接続
#### <a name="setup-requirement"></a>設定要件
ActiveDirectoryPassword 認証モードを使用する場合は、[azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) とその依存関係をダウンロードし、それらを Java ビルド パスに含める必要があります。

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>アクセス トークンを使用した接続
#### <a name="setup-requirement"></a>設定要件
アクセス トークンに基づく認証モードを使用する場合は、[azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) とその依存関係をダウンロードし、それらを Java ビルド パスに含める必要があります。

Azure SQL データベースのアクセス トークンを取得する方法については、[Azure Active Directory 認証を使用した SQL Database の認証](sql-database-aad-authentication.md)に関する記事を参照してください。

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>一括挿入を使用して Azure SQL データベースまたは SQL Server にデータを書き込む
従来の JDBC コネクタは、行単位の挿入を使用して Azure SQL データベースまたは SQL Server にデータを書き込みます。 Spark to SQL DB コネクタでは、一括挿入を使用して SQL Ｄatabase にデータを書き込むことができます。 大きなデータ セットを読み込むとき、または列ストア インデックスが使用されているテーブルにデータを読み込むときの書き込みのパフォーマンスが大幅に向上します。

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>次の手順
Azure SQL Database と SQL Server 用の Spark コネクタをまだダウンロードしていない場合は、[GitHub の azure-sqldb-spark リポジトリ](https://github.com/Azure/azure-sqldb-spark)からダウンロードし、リポジトリのその他のリソースを調べます。

-   [Azure Databricks ノートブックのサンプル](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [サンプル スクリプト (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

また、[Apache Spark SQL、DataFrames、データセット ガイド](https://spark.apache.org/docs/latest/sql-programming-guide.html)と [Azure Databricks ドキュメント](https://docs.microsoft.com/azure/azure-databricks/)を確認することもできます。

