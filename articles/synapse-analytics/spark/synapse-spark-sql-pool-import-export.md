---
title: サーバーレス Apache Spark プールと SQL プールの間でのデータのインポートとエクスポート
description: この記事では、カスタム コネクタを使用して、専用 SQL プールとサーバーレス Apache Spark プールの間でデータを移動する方法について説明します。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 66a804f645ee56cce62053a51e6aa4fc25b3c1a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671323"
---
# <a name="introduction"></a>はじめに

Azure Synapse Apache Spark から Synapse SQL へのコネクタは、Azure Synapse のサーバーレス Apache Spark プールと専用 SQL プールの間でデータを効率的に転送するように設計されています。 Synapse SQL コネクタへの Azure Synapse の Apache Spark は、専用 SQL プールでのみ機能し、サーバーレス SQL プールでは機能しません。

> [!WARNING]
> **sqlanalytics()** という関数名が **synapsesql()** に変更されました。 sqlanalytics 関数は引き続き機能しますが、今後非推奨となります。  将来中断が発生しないように、すべての参照を **sqlanalytics()** から **synapsesql()** に変更してください。

## <a name="design"></a>デザイン

Spark プールと SQL プールの間でのデータの転送は、JDBC を使用して行うことができます。 ただし、Spark プールや SQL プールといった 2 つの分散システムでは、JDBC はシリアル データ転送のボトルネックになる傾向があります。

Synapse SQL コネクタへの Azure Synapse の Apache Spark プールは、Apache Spark 用のデータ ソースの実装です。 これにより、Azure Data Lake Storage Gen2 と専用 SQL プールの PolyBase が使用され、Spark クラスターと Synapse の専用 SQL インスタンスの間でデータが効率的に転送されます。

![コネクタ アーキテクチャ](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Azure Synapse Analytics での認証

システム間の認証は、Azure Synapse Analytics でシームレスに行われます。 トークン サービスは Azure Active Directory に接続し、ストレージ アカウントまたはデータ ウェアハウス サーバーにアクセスするときに使用するセキュリティ トークンを取得します。

このため、ストレージ アカウントとデータ ウェアハウス サーバーで Azure AD 認証が構成されている限り、資格情報を作成したり、コネクタ API でそれらを指定したりする必要はありません。 そうでない場合は、SQL 認証を指定できます。 詳細については、「[使用法](#usage)」セクションを参照してください。

## <a name="constraints"></a>制約

- このコネクタは Scala でのみ動作します。
- PySpark の詳細については、[Python の使用](#use-pyspark-with-the-connector)に関するセクションを参照してください。
- このコネクタでは、SQL ビューに対するクエリの実行はサポートされません。

## <a name="prerequisites"></a>前提条件

- データの転送先および転送元となるデータベースおよび SQL プールにある、**db_exporter** ロールのメンバーである必要があります。
- 既定のストレージ アカウントのストレージ BLOB データ共同作成者ロールのメンバーである必要があります。

ユーザーを作成するには、SQL プール データベースに接続し、これらの例に従います。

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

ロールを割り当てるには:

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## <a name="usage"></a>使用法

import ステートメントは必要ありません。ノートブック エクスペリエンスのために事前にインポートされています。

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-attached-within-the-workspace"></a>ワークスペースに接続されている専用 SQL プールとの間でのデータを転送する

> [!NOTE]
> **import はノートブック エクスペリエンスには必要ありません**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Read API

```scala
val df = spark.read.synapsesql("<DBName>.<Schema>.<TableName>")
```

上記の API は、SQL プールの内部 (マネージド) テーブルと外部テーブルの両方に対して機能します。

#### <a name="write-api"></a>Write API

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

Write API では、専用 SQL プールにテーブルを作成してから、Polybase を呼び出してデータを読み込みます。  テーブルは専用 SQL プールに存在することはできません。存在する場合、"... という名前のオブジェクトが既に存在します" という内容のエラーが返されます。

TableType の値

- Constants.INTERNAL - 専用 SQL プール内のマネージド テーブル
- Constants.EXTERNAL - 専用 SQL プール内の外部テーブル

SQL プールのマネージド テーブル

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

SQL プールの外部テーブル

専用 SQL プールの外部テーブルに書き込むには、EXTERNAL DATA SOURCE と EXTERNAL FILE FORMAT が専用 SQL プールに存在している必要があります。  詳細については、専用 SQL プールでの[外部データ ソース](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)および[外部ファイル形式](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)の作成に関するページを参照してください。  専用 SQL プールに外部データ ソースと外部ファイル形式を作成する例を以下に示します。

```sql
--For an external table, you need to pre-create the data source and file format in dedicated SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

ストレージ アカウントへの Azure Active Directory パススルー認証を使用する場合、EXTERNAL CREDENTIAL オブジェクトは必要ありません。  自分がストレージ アカウントの "Storage BLOB データ共同作成者" ロールのメンバーであることを確認します。

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    synapsesql("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-or-database-outside-the-workspace"></a>ワークスペースの外部にある専用 SQL プールまたはデータベースとの間でデータを転送する

> [!NOTE]
> import はノートブック エクスペリエンスには必要ありません

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Read API

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Write API

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-sql-auth-instead-of-azure-ad"></a>Azure AD ではなく SQL 認証を使用する

#### <a name="read-api"></a>Read API

現在、コネクタでは、ワークスペースの外部にある専用 SQL プールに対するトークンベースの認証はサポートされていません。 SQL 認証の使用が必要になります。

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Write API

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-pyspark-with-the-connector"></a>PySpark とコネクタを使用する

> [!NOTE]
> この例は、ノートブック エクスペリエンスのみを考慮して提供されています。

DW に書き込むデータフレーム "pyspark_df" があるとします。

PySpark のデータフレームを使用して一時テーブルを作成します。

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

マジックを使用して PySpark Notebook で Scala セルを実行します。

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

scala_df.write.synapsesql("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

同様に、読み取りシナリオでは Scala を使用してデータを読み取り、それを一時テーブルに書き込んでから、PySpark の Spark SQL を使用して一時テーブルをデータフレームに照会します。

## <a name="allow-other-users-to-use-the-azure-synapse-apache-spark-to-synapse-sql-connector-in-your-workspace"></a>他のユーザーが Azure Synapse Apache Spark を使用して、ワークスペース内の Synapse SQL コネクタを使用できるようにする

他のユーザーにアクセス許可がないように変更するには、ワークスペースに接続されている ADLS Gen2 ストレージ アカウントのストレージ BLOB データ所有者である必要があります。 そのワークスペースへのアクセス権とノートブックを実行するためのアクセス許可をそのユーザーが持っていることを確認します。

### <a name="option-1"></a>方法 1

- ユーザーをストレージ BLOB データの共同作成者または所有者にする

### <a name="option-2"></a>方法 2

- フォルダー構造に次の ACL を指定する

| Folder | / | synapse | workspaces  | \<workspacename> | sparkpools | \<sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| アクセス許可 | --X | --X | --X | --X | --X | --X | -WX |
| 既定の権限 | ---| ---| ---| ---| ---| ---| ---|

- "synapse" 以下のすべてのフォルダーの ACL を Azure portal から設定できる必要があります。 ルート "/" フォルダーに ACL を設定するには、以下の手順に従ってください。

- Azure AD を使用して Storage Explorer からワークスペースに接続されているストレージ アカウントに接続します。
- アカウントを選択し、ワークスペースの ADLS Gen2 URL と既定のファイル システムを指定します。
- 一覧表示されたストレージ アカウントが表示されたら、一覧表示されているワークスペースを右クリックし、[アクセスの管理] を選択します。
- "実行" のアクセス許可がある / フォルダーにユーザーを追加します。 [OK] を選択します。

> [!IMPORTANT]
> 意図しない場合は、"既定" を選択しないようにしてください。


## <a name="next-steps"></a>次のステップ

- [Azure portal を使用して専用 SQL プールを作成する](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Azure portal を使用して新しい Apache Spark プールを作成する](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
