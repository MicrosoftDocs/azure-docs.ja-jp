---
title: Azure Cosmos DB で Bulk Executor Java ライブラリを使用して一括インポートおよび更新操作を実行する
description: Bulk Executor Java ライブラリを使用して Azure Cosmos DB ドキュメントを一括インポートおよび更新する
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 08/26/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-java
ms.openlocfilehash: d0ccaf15df62b566b5a52ee7a4031ba7e270a4ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218113"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Bulk Executor Java ライブラリを使用して Azure Cosmos DB で一括操作を実行する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

このチュートリアルでは、Azure Cosmos DB の BulkExecutor Java ライブラリを使用して、Azure Cosmos DB ドキュメントをインポートおよび更新する方法について説明します。 Bulk Executor ライブラリについてと、それを大規模なスループットおよびストレージの活用に役立てる方法については、[Bulk Executor ライブラリの概要](bulk-executor-overview.md)に関する記事を参照してください。 このチュートリアルでは、ランダムなドキュメントを生成し、Azure Cosmos コンテナーに一括インポートする Java アプリケーションを作成します。 インポートした後、ドキュメントの一部のプロパティを一括更新します。 

現在、Bulk Executor ライブラリは、Azure Cosmos DB SQL API および Gremlin API アカウントによってのみサポートされています。 この記事では、SQL API アカウントで Bulk Executor Java ライブラリを使用する方法について説明します。 Gremlin API での Bulk Executor .NET ライブラリの使用の詳細については、[Azure Cosmos DB Gremlin API での一括操作の実行](bulk-executor-graph-dotnet.md)に関するページを参照してください。 説明されているバルク エグゼキューター ライブラリは、[Azure Cosmos DB Java sync SDK v2](sql-api-sdk-java.md) でのみ使用することができ、Java バルク サポートで現在推奨されているソリューションとなります。 現在、3.x、4.x、またはより上位のその他のバージョンの SDK では使用できません。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。  

* Azure サブスクリプションを必要とせず、課金や契約もなく [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)ことができます。 または、`https://localhost:8081` エンドポイントで [Azure Cosmos DB Emulator](./local-emulator.md) を使用できます。 主キーは、[要求の認証](local-emulator.md#authenticate-requests)で与えられます。  

* [Java Development Kit (JDK) 1.7 以降](/java/azure/jdk/)  
  - Ubuntu で `apt-get install default-jdk` を実行して JDK をインストールします。  

  - 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。

* [Maven](https://maven.apache.org/) バイナリ アーカイブの[ダウンロード](https://maven.apache.org/download.cgi)と[インストール](https://maven.apache.org/install.html)  
  
  - Ubuntu で `apt-get install maven` を実行して Maven をインストールします。

* Java クイック スタート記事の「[データベース アカウントの作成](create-sql-api-java.md#create-a-database-account)」セクションで説明されている手順に従って、Azure Cosmos DB SQL API アカウントを作成します。

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

それでは、GitHub からサンプル Java アプリケーションをダウンロードして、コードの作業を行います。 このアプリケーションでは、Azure Cosmos DB データに対して一括操作を実行します。 アプリケーションを複製するために、コマンド プロンプトを開き、コピー先のディレクトリに移動し、次のコマンドを実行します。

```bash
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

複製されたリポジトリの "\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor" フォルダーには、2 つのサンプル "bulkimport" と "bulkupdate" が含まれています。 "bulkimport" アプリケーションは、ランダムなドキュメントを生成し、Azure Cosmos DB にインポートします。 "bulkupdate" アプリケーションは、Azure Cosmos DB の一部のドキュメントを更新します。 以下のセクションでは、これらのサンプル アプリケーションのコードを確認します。 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Azure Cosmos DB へのデータの一括インポート

1. Azure Cosmos DB の接続文字列は、引数として読み取られ、CmdLineConfiguration.java ファイルで定義されている変数に代入されます。  

2. 次に、以下のステートメントを使って DocumentClient オブジェクトが初期化されます。  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. DocumentBulkExecutor オブジェクトが初期化され、待機時間とスロットルされた要求の再試行回数が大きい値に設定されます。 その後、有効期間にわたって DocumentBulkExecutor に輻輳制御を渡すために、0 に設定されます。  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. Azure Cosmos コンテナーに一括インポートするランダムなドキュメントを生成する importAll API を呼び出します。 CmdLineConfiguration.java ファイルで、コマンド ライン構成を構成できます。

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   一括インポート API は、JSON でシリアル化されたドキュメントのコレクションを受け取ります。構文は次のとおりです。詳しくは、[API のドキュメント](/java/api/com.microsoft.azure.documentdb.bulkexecutor)をご覧ください。

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   importAll メソッドは、次のパラメーターを受け取ります。
 
   |**パラメーター**  |**説明**  |
   |---------|---------|
   |isUpsert    |   ドキュメントのアップサートを有効にするフラグ。 指定された ID のドキュメントが既に存在する場合は、更新されます。  |
   |disableAutomaticIdGeneration     |   ID の自動生成を無効にするフラグ。 既定値は true です。   |
   |maxConcurrencyPerPartitionRange    |  パーティション キーの範囲ごとのコンカレンシーの最大限度。 既定値は 20 です。  |

   **一括インポート応答オブジェクトの定義** 一括インポート API 呼び出しの結果には、次の get メソッドが含まれています。

   |**パラメーター**  |**説明**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   一括インポート API 呼び出しに渡されたドキュメントのうち正常にインポートされたドキュメントの総数。      |
   |double getTotalRequestUnitsConsumed()   |  一括インポート API 呼び出しによって使用された要求ユニット (RU) の合計数。       |
   |Duration getTotalTimeTaken()   |    一括インポート API 呼び出しが実行を完了するまでに要した合計時間。     |
   |List\<Exception> getErrors() |  一括インポート API 呼び出しに渡されたバッチの一部のドキュメントの挿入が失敗した場合、エラーの一覧を取得します。       |
   |List\<Object> getBadInputDocuments()  |    一括インポート API 呼び出しで正常にインポートされなかった、形式が正しくないドキュメントの一覧。 ユーザーは返されたドキュメントを修正し、インポートを再試行する必要があります。 形式が正しくないドキュメントには、ID 値が文字列ではないドキュメントがあります (null やその他のデータ型は無効と見なされます)。     |

5. 一括インポート アプリケーションの準備ができたら、"mvn clean package" コマンドを使用して、ソースからコマンド ライン ツールをビルドします。 このコマンドは、ターゲット フォルダーに jar ファイルを生成します。  

   ```bash
   mvn clean package
   ```

6. ターゲットの依存関係が生成されたら、次のコマンドを使用して一括インポーター アプリケーションを呼び出すことができます。  

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB's endpoint>*  -masterKey *<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   一括インポーターにより、App.config ファイルで指定したデータベース名、コレクション名、およびスループット値を使用して、新しいデータベースとコレクションが作成されます。 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Azure Cosmos DB でのデータの一括更新

BulkUpdateAsync API を使用すると、既存のドキュメントを更新できます。 この例では、Name フィールドを新しい値に設定し、既存のドキュメントから Description フィールドを削除します。 サポートされているすべてのフィールド更新操作については、[API ドキュメント](/java/api/com.microsoft.azure.documentdb.bulkexecutor)を参照してください。 

1. 更新項目と、対応するフィールド更新操作を定義します。 この例では、SetUpdateOperation を使用して Name フィールドを更新し、UnsetUpdateOperation を使用してすべてのドキュメントから Description フィールドを削除します。 ドキュメント フィールドの値に特定の値を加算したり、特定の値を配列フィールドにプッシュしたり、配列フィールドから特定の値を削除したりするなど、他の操作を実行することもできます。 一括更新 API が提供するさまざまなメソッドについては、[API ドキュメント](/java/api/com.microsoft.azure.documentdb.bulkexecutor)を参照してください。  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                        
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. Azure Cosmos コンテナーに一括インポートするランダムなドキュメントを生成する updateAll API を呼び出します。 CmdLineConfiguration.java ファイルで渡されるコマンド ライン構成を構成できます。

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   一括更新 API は、更新する項目のコレクションを受け取ります。 各更新項目では、ID とパーティション キーの値で識別されるドキュメントに対して実行するフィールド更新操作のリストが指定されています。 詳しくは、[API ドキュメント](/java/api/com.microsoft.azure.documentdb.bulkexecutor)をご覧ください。

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   updateAll メソッドは、次のパラメーターを受け取ります。

   |**パラメーター** |**説明** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  パーティション キーの範囲ごとのコンカレンシーの最大限度。 既定値は 20 です。  |
 
   **一括インポート応答オブジェクトの定義** 一括インポート API 呼び出しの結果には、次の get メソッドが含まれています。

   |**パラメーター** |**説明**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   一括更新 API 呼び出しに渡されたドキュメントのうち正常に更新されたドキュメントの総数。      |
   |double getTotalRequestUnitsConsumed() |  一括更新 API 呼び出しによって使用された要求ユニット (RU) の合計数。       |
   |Duration getTotalTimeTaken()  |   一括更新 API 呼び出しが実行を完了するまでに要した合計時間。      |
   |List\<Exception> getErrors()   |       更新操作に関連する操作またはネットワークの問題の一覧を取得します。      |
   |List\<BulkUpdateFailure> getFailedUpdates()   |       失敗につながる特定の例外を伴う、完了できなかった更新プログラムの一覧を取得します。|

3. 一括更新アプリケーションの準備ができたら、"mvn clean package" コマンドを使用して、ソースからコマンド ライン ツールをビルドします。 このコマンドは、ターゲット フォルダーに jar ファイルを生成します。  

   ```bash
   mvn clean package
   ```

4. ターゲットの依存関係が生成されたら、次のコマンドを使用して一括更新アプリケーションを呼び出すことができます。

   ```bash
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB's endpoint>* -masterKey **<Fill in your Azure Cosmos DB's primary key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>パフォーマンスに関するヒント 

Bulk Executor ライブラリを使用する場合は、パフォーマンスを向上させるために、次の点を考慮してください。

* パフォーマンスを最大限に高めるには、Cosmos DB アカウントの書き込みリージョンと同じリージョンにある Azure VM からアプリケーションを実行します。  
* 高いスループットを実現するには、次のようにします。  

   * 多数のドキュメントを処理するときのメモリの問題を回避するため、JVM のヒープ サイズを十分に大きい値に設定します。 推奨されるヒープ サイズ: max(3GB, 3 * sizeof(1 回のバッチで一括インポート API に渡されるすべてのドキュメント))。  
   * 前処理にかかる時間があるため、多数のドキュメントで一括操作を実行すると、スループットが高くなります。 そのため、10,000, 000 個のドキュメントをインポートする場合、100,000 ドキュメントずつ 100 回の一括インポートを実行するより、1,000,000 ドキュメントずつ 10 回の一括インポートを実行する方が効率的です。  

* 特定の Azure Cosmos コンテナーに対応する単一の仮想マシン内でアプリケーション全体に対して 1 つの DocumentBulkExecutor オブジェクトをインスタンス化することをお勧めします。  

* 1 つの一括操作 API 実行でクライアント マシンの CPU とネットワーク IO が大量に消費されます。 これは、内部的に複数のタスクを生成することで、一括操作 API 呼び出しを実行するたびにアプリケーション プロセス内で複数の同時実行タスクが生成されないようにするためです。 単一の仮想マシンで実行される 1 つの一括操作 API 呼び出しでコンテナー全体のスループットを消費できない場合 (コンテナーのスループットが 100 万 RU/秒を超える場合)、別個の仮想マシンを作成して、一括操作 API 呼び出しを同時に実行することをお勧めします。

    
## <a name="next-steps"></a>次のステップ
* Maven パッケージの詳細と Bulk Executor Java ライブラリのリリース ノートについては、[Bulk Executor SDK の詳細](sql-api-sdk-bulk-executor-java.md)に関するページをご覧ください。