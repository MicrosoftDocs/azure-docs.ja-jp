---
title: Apache Spark 向け Azure Data Explorer コネクタを使用し、Azure Data Explorer と Spark クラスターの間でデータを移動します。
description: このトピックでは、Azure Data Explorer と Spark クラスターの間でデータを移動する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 6a95cbad161906bd12a608880ac694d6bdf1ed27
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383046"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Apache Spark 用の Azure Data Explorer コネクタ (プレビュー)

[Apache Spark](https://spark.apache.org/) は、"大規模なデータ処理のための統合された分析エンジン" です。 Azure Data Explorer は、大量のデータのリアルタイム分析を実現するフル マネージドの高速データ分析サービスです。 

Spark 用 Azure Data Explorer コネクタでは、Azure Data Explorer と Spark クラスターの両方の機能を使用する目的で両者の間でデータを移動するためのデータ ソースとデータ シンクが実装されます。 Azure Data Explorer と Apache Spark を使用して、機械学習 (ML)、Extract-Transform-Load (ETL)、および Log Analytics などのデータ駆動型シナリオをターゲットとする、高速でスケーラブルなアプリケーションを作成することができます。 Azure Data Explorer への書き込みは、バッチおよびストリーミング モードで実行できます。
Azure Data Explorer からの読み取りでは、列の排除と述語のプッシュダウンがサポートされています。Azure Data Explorer でデータをフィルターして除外することで、転送するデータの量を減らします。

Azure Data Explorer Spark コネクタは、あらゆる Spark クラスターで実行できる[オープン ソース プロジェクト](https://github.com/Azure/azure-kusto-spark)です。 Azure Data Explorer Spark コネクタによって、Azure Data Explorer は、書き込み、読み取り、writeStream などの標準的な Spark のソースおよびシンク操作に有効なデータ ストアになります。 

> [!NOTE]
> 下の例のいくつかでは [Azure Databricks](https://docs.azuredatabricks.net/) Spark クラスターが登場しますが、Azure Data Explorer Spark コネクタは、Databricks やその他の Spark ディストリビューションに直接依存しません。

## <a name="prerequisites"></a>前提条件

* [Azure Data Explorer クラスターとデータベースを作成します](/azure/data-explorer/create-cluster-database-portal) 
* Spark クラスターの作成
* Azure Data Explorer コネクタ ライブラリと、次の [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) ライブラリを含む、[依存関係](https://github.com/Azure/azure-kusto-spark#dependencies)に記載されているライブラリをインストールします。
    * [Kusto Data Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto Ingest Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* [Spark 2.4、Scala 2.11 向けに事前構築されたライブラリ](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Spark コネクタのビルド方法

Spark コネクタは、下の詳細にあるように[ソース](https://github.com/Azure/azure-kusto-spark)から構築できます。

> [!NOTE]
> この手順は省略可能です。 事前構築されたライブラリを使用する場合、[Spark クラスターの設定](#spark-cluster-setup)にお進みください。

### <a name="build-prerequisites"></a>構築の前提条件

* Java 1.8 SDK がインストールされていること
* [Maven 3.x](https://maven.apache.org/download.cgi) がインストールされていること
* Apache Spark バージョン 2.4.0 以降

> [!TIP]
> 2.3.x バージョンもサポートされていますが、pom.xml の依存関係を場合によっては変更する必要があります。

Maven プロジェクト定義を使用する Scala/Java アプリケーションの場合は、アプリケーションを次の成果物にリンクしてください (最新版は異なる場合があります)。

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>ビルド コマンド

Jar をビルドして、すべてのテストの実行するには:

```
mvn clean package
```

Jar をビルドし、すべてのテストを実行し、ローカルの Maven リポジトリに jar をインストールするには:

```
mvn clean install
```

詳細については、[コネクタの使用](https://github.com/Azure/azure-kusto-spark#usage)に関するページを参照してください。

## <a name="spark-cluster-setup"></a>Spark クラスターの設定

> [!NOTE]
> 次の手順を実行するとき、最新の Azure Data Explorer Spark コネクタ リリースを使用することをお勧めします。

1. Spark 2.4 と Scala 2.11 を使用し、Azure Databricks に基づいて Spark クラスターを次のように設定します。 

    ![Databricks クラスターの設定](media/spark-connector/databricks-cluster.png)

1. Azure Data Explorer コネクタ ライブラリのインポート:

    ![Azure Data Explorer ライブラリをインポートする](media/spark-connector/db-create-library.png)

1. 依存関係をさらに追加します (maven から使用する場合は必ずしもする必要はありません):

    ![依存関係を追加する](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > 各 Spark リリースの正しい java リリース バージョンは[こちら](https://github.com/Azure/azure-kusto-spark#dependencies)にあります。

1. 必要なライブラリがすべてインストールされていることを確認します。

    ![ライブラリがインストールされていることを確認します](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>認証

Azure Data Explorer Spark コネクタでは、[Azure AD アプリケーション](#azure-ad-application-authentication)、[Azure AD アクセス トークン](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)、[デバイス認証](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (非運用シナリオ)、または [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) を使用して Azure Active Directory (Azure AD) で認証できます。 ユーザーは azure-keyvault パッケージをインストールし、Key Vault リソースにアクセスするためのアプリケーション資格情報を指定する必要があります。

### <a name="azure-ad-application-authentication"></a>Azure AD アプリケーション認証

最も簡単で一般的な認証方法。 この方法は Azure Data Explorer Spark コネクタを使用するときにお勧めです。

|properties  |説明  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD アプリケーション (クライアント) ID。      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD 認証機関。 Azure AD Directory (テナント) ID。        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    クライアントの Azure AD アプリケーション キー。     |

### <a name="azure-data-explorer-privileges"></a>Azure Data Explorer の特権

Azure Data Explorer クラスターで次の特権を付与する必要があります。

* 読み取りには (データ ソース)、Azure AD アプリケーションに対象データベースの*ビューアー*特権を与えるか、対象テーブルの*管理者*特権を与える必要があります。
* 書き込みには (データ シンク)、Azure AD アプリケーションに対象データベースの*インジェスター*特権を与える必要があります。 新しいテーブルを作成するには、対象データベースの*ユーザー*特権も与える必要があります。 対象テーブルが既に存在する場合、対象テーブルの*管理者*特権を設定できます。
 
Azure Data Explorer のプリンシパル ロールの詳細については、[ロールベースの承認](/azure/kusto/management/access-control/role-based-authorization)に関するページを参照してください。 セキュリティ ロールの管理については、「[security roles management](/azure/kusto/management/security-roles)」(セキュリティ ロールの管理) を参照してください。

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark シンク:Azure Data Explorer に書き込む

1. シンクのパラメーターの設定:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Spark DataFrame を Azure Data Explorer クラスターにバッチとして書き込む:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    val conf = Map(
            KustoSinkOptions.KUSTO_CLUSTER -> cluster,
            KustoSinkOptions.KUSTO_TABLE -> table,
            KustoSinkOptions.KUSTO_DATABASE -> database,
            KustoSinkOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId)
    
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .options(conf)
      .save()
      
    ```
    
   または、簡略化された構文を使用します。
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. ストリーミング データを書き込む:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark ソース:Azure Data Explorer から読み込む

1. 少量のデータを読み込むとき、データ クエリを定義します。

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. 大量のデータを読み込むとき、一時的な BLOB ストレージを指定する必要があります。 ストレージ コンテナーの SAS キー、またはストレージ アカウント名、アカウント キー、コンテナー名を指定します。 この手順は、Spark コネクタの現行のプレビュー リリースにのみ必要です。

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    上記の例では、コネクタ インターフェイスを利用して Key Vault にアクセスしていません。 代替方法として、Databricks シークレットを使用するという、より簡単な方法を使用しています。

1. Azure Data Explorer から読み込む:

    ```scala
     val conf3 = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
          KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
