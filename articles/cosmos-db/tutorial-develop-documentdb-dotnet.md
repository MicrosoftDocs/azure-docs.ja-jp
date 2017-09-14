---
title: "Azure Cosmos DB: .NET での DocumentDB API を使用した開発 | Microsoft Docs"
description: ".NET を使用した Azure Cosmos DB の DocumentDB API による開発方法について"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 2eed74ae9bd173b0944ec190dfe5d9a4bdc54c37
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="azure-cosmosdb-develop-with-the-documentdb-api-in-net"></a>Azure Cosmos DB: .NET での DocumentDB API を使用した開発

Azure Cosmos DB は、Microsoft のグローバルに配布されるマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバル配布と水平方向のスケール機能を活用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成および照会できます。 

このチュートリアルでは、Azure Portal を使用して Azure Cosmos DB アカウントを作成する方法と、[DocumentDB .NET API](documentdb-introduction.md) を使用して[パーティション キー](documentdb-partition-data.md#partition-keys)でドキュメント データベースおよびコレクションを作成する方法を説明します。 コレクションの作成時にパーティション キーを定義することで、データの増加に合わせて、労力をかけずにアプリケーションのスケールを準備できます。 

このチュートリアルでは、[DocumentDB .NET API](documentdb-sdk-dotnet.md) を使用して、次のタスクを説明します。

> [!div class="checklist"]
> * Azure Cosmos DB アカウントの作成
> * パーティション キーを使用したデータベースとコレクションの作成
> * JSON ドキュメントの作成
> * ドキュメントの更新
> * パーティション分割コレクションへのクエリの実行
> * ストアド プロシージャの実行
> * ドキュメントの削除
> * データベースの削除

## <a name="prerequisites"></a>前提条件
以下のものがそろっていることを確認してください。

* アクティブな Azure アカウント。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。 
    * または、開発目的で Azure DocumentDB サービスをエミュレートするローカル環境を使用する場合は、このチュートリアルの [Azure Cosmos DB エミュレーター](local-emulator.md)を使用することもできます。
* [Visual Studio](http://www.visualstudio.com/)。

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントの作成

まず最初に、Azure Portal で Azure Cosmos DB アカウントを作成します。

> [!TIP]
> * 既に Azure Cosmos DB アカウントをお持ちですか。 その場合は、「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進みます。
> * 既に Azure DocumentDB アカウントをお持ちでしたか。 この場合、そのアカウントが Azure Cosmos DB アカウントになります。「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進んでください。  
> * Azure Cosmos DB Emulator を使用する場合は、[Azure Cosmos DB Emulator](local-emulator.md) に関する記事に記載されている手順に従って、エミュレーターをセットアップし、「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進んでください。 
>
>

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Visual Studio ソリューションをセットアップする
1. コンピューターで **Visual Studio** を開きます。
2. **[ファイル]** メニューで、**[新規]**、**[プロジェクト]** の順に選択します。
3. **[新しいプロジェクト]** ダイアログで、**[テンプレート]** / **[Visual C#]** / **[コンソール アプリ (.NET Framework)]** の順に選択し、プロジェクトの名前を指定して、**[OK]** をクリックします。
   ![[新しいプロジェクト] ウィンドウのスクリーン ショット](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-new-project-2.png)

4. **ソリューション エクスプローラー**で、Visual Studio ソリューションの下にある新しいコンソール アプリケーションを右クリックし、**[NuGet パッケージの管理]** をクリックします。
    
    ![プロジェクトの右クリック メニューのスクリーン ショット](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. **[NuGet]** タブの **[参照]** をクリックし、検索ボックスに「**documentdb**」と入力します。
<!---stopped here--->
6. 結果で **Microsoft Azure DocumentDB** を探し、**[インストール]** をクリックします。
   Azure Cosmos DB クライアント ライブラリのパッケージ ID は [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB) です。
   ![Azure Cosmos DB クライアント SDK を見つける NuGet メニューのスクリーン ショット](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    ソリューションの変更の確認に関するメッセージが表示されたら、**[OK]** をクリックします。 ライセンスの同意に関するメッセージが表示されたら、**[同意する]** をクリックします。

## <a id="Connect"></a>プロジェクトに参照を追加する
このチュートリアルの以降の手順では、プロジェクトでの Azure Cosmos DB リソースの作成および更新に必要な DocumentDB API コード スニペットが提供されます。

最初に、アプリケーションにこれらの参照を追加します。
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>アプリに接続する

次に、アプリケーションにこれら 2 つの定数と*クライアント*変数を追加します。

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

追加したら、[Azure Portal](https://portal.azure.com) に戻り、エンドポイント URL とプライマリ キーを取得します。 エンドポイント URL とプライマリ キーは、アプリケーションが接続先を認識し、Azure Cosmos DB がアプリケーションの接続を信頼するために必要です。

Azure Portal で Azure Cosmos DB アカウントに移動し、**[キー]** をクリックしてから **[読み取り/書き込みキー]** をクリックします。

ポータルから URI をコピーし、program.cs ファイルの上の `<your endpoint URL>` に貼り付けます。 次に、ポータルからプライマリ キーをコピーし、`<your primary key>` に貼り付けます。 `<` と `>` は削除してください。

![C# コンソール アプリケーションを作成するために NoSQL チュートリアルで使用される Azure Portal のスクリーンショット。 Azure Cosmos DB アカウント ブレードで KEYS が強調表示された Azure Cosmos DB アカウントと、[キー] ブレードで強調表示された URI と PRIMARY KEY の値を示します](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>DocumentClient をインスタンス化する

ここでは、**DocumentClient** の新しいインスタンスを作成します。

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>データベースを作成する

次に、Azure Cosmos DB [データベース](documentdb-resources.md#databases)を作成します。これには、[DocumentDB .NET SDK](documentdb-sdk-dotnet.md) に含まれる **DocumentClient** クラスの [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) メソッドまたは [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) メソッドを使用します。 データベースは、コレクションに分割された JSON ドキュメント ストレージの論理上のコンテナーです。

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>パーティション キーを決める 

コレクションは、ドキュメントを格納するためのコンテナーです。 コレクションは論理リソースであり、[1 つ以上の物理パーティションにまたがる](partition-data.md)ことができます。 [パーティション キー](documentdb-partition-data.md)は、DocumentDB が複数のサーバーまたはパーティション間にデータを分散するために使用されるドキュメント内のプロパティ (またはパス) です。 同じパーティション キーを持つすべてのドキュメントは、同じパーティションに格納されます。 

コレクションを作成する前にパーティション キーを指定することは、重要な決定事項の 1 つです。 パーティション キーは、Azure Cosmos DB が複数のサーバーまたはパーティション間にデータを分散するために使用できるドキュメント内のプロパティ (またはパス) です。 Cosmos DB がパーティション キー値をハッシュし、そのハッシュした結果を基にドキュメントを格納するパーティションを決定します。 同じパーティション キーを持つすべてのドキュメントは同じパーティションに格納され、コレクションを一度作成したら、パーティション キーを変更することはできません。 

このチュートリアルでは、単一のデバイスのすべてのデータが単一のパーティションに格納されるように、パーティション キーを `/deviceId` に設定します。 多数の値を持つパーティション キーを選択した場合、データの増加に合わせて Cosmos DB が確実に負荷分散でき、コレクションの完全なスループットを実現するために、各値がほぼ同じ頻度で使用されます。 

パーティション分割について詳しくは、[Azure Cosmos DB でのパーティション分割とスケールの方法](partition-data.md)に関する記事をご覧ください。 

## <a id="CreateColl"></a>コレクションを作成する 

パーティション キーが `/deviceId` に決まったところで、**DocumentClient** クラスの [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) メソッドまたは [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) メソッドを使用して[コレクション](documentdb-resources.md#collections)を作成しましょう。 コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。 

> [!WARNING]
> コレクションの作成は料金に影響します。これは、Azure Cosmos DB と通信するためにアプリケーションのスループットを予約するためです。 詳しくは、[価格のページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。
> 
> 

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

このメソッドでは、Azure Cosmos DB に対して REST API を呼び出します。サービスにより、要求されたスループットに基づいた数のパーティションがプロビジョニングされます。 パフォーマンス ニーズの変化に応じて、SDK または [Azure Portal](set-throughput.md) を使用して、コレクションのスループットを変更できます。

## <a id="CreateDoc"></a>JSON ドキュメントを作成する
Azure Cosmos DB に JSON ドキュメントを挿入してみましょう。 [ドキュメント](documentdb-resources.md#documents)は、**DocumentClient** クラスの [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) メソッドを使用して作成できます。 ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。 このサンプル クラスには、デバイスの新しい読み取りをコレクションに挿入するための、デバイス読み取りデータと CreateDocumentAsync への呼び出しが含まれます。

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted 
// as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```
## <a name="read-data"></a>データの読み取り

ReadDocumentAsync メソッドを使用して、パーティション キーと ID を使ってドキュメントを読み取ってみましょう。 読み取りには (REST API 内の `x-ms-documentdb-partitionkey` 要求ヘッダーに対応する) PartitionKey 値が含まれることにご注意ください。

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>データの更新

ここでは、ReplaceDocumentAsync メソッドを使用して、一部のデータを更新します。

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);
```

## <a name="delete-data"></a>データの削除

DeleteDocumentAsync メソッドを使用して、パーティション キーと ID を使ってドキュメントを削除してみましょう。

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>パーティション分割コレクションへのクエリの実行

パーティション分割コレクション内のデータを照会する際に、Azure Cosmos DB により、フィルターに指定したパーティション キー値に対応するパーティションにクエリが自動ルーティングされます。 たとえば、ここでのクエリはパーティション キー "XMS-0001" を含むパーティションのみにルーティングされます。

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
次のクエリにはパーティション キー (DeviceId) にフィルターがなく、パーティションのインデックスに対してこのクエリが実行されるすべてのパーティションにファン アウトされます。 パーティション全体に対して SDK にクエリを実行させるために、EnableCrossPartitionQuery (REST API 内の`x-ms-documentdb-query-enablecrosspartition` ) を指定する必要があることにご注意ください。

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>並列クエリの実行
Azure Cosmos DB DocumentDB SDK 1.9.0 以降では、並列クエリ実行オプションがサポートされています。そのため、多数のパーティションにタッチする必要がある場合でも、パーティションのコレクションに対して少ない待ち時間でクエリを実行できます。 たとえば、次のクエリはパーティション全体で並列に実行されるように構成されています。

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
次のパラメーターを調整することで、並列クエリの実行を管理できます。

* `MaxDegreeOfParallelism`を設定すると、並列処理次数、つまりコレクションのパーティションに同時ネットワーク接続できる数の上限を制御することができます。 このパラメーターを -1 に設定した場合、並列処理次数は SDK によって管理されます。 `MaxDegreeOfParallelism` が指定されていないか、0 (既定値) に設定されている場合、コレクションのパーティションへのネットワーク接続は 1 つのみです。
* `MaxBufferedItemCount` を設定すると、クエリの待ち時間とクライアント側のメモリ使用率のバランスを取ることができます。 このパラメーターを省略するか、このパラメーターに -1 を設定した場合、並列クエリの実行中にバッファリングされる項目の数は SDK によって管理されます。

コレクションが同じ状態の場合、並列クエリでは順次実行と同じ順序で結果が返されます。 並べ替え (ORDER BY、TOP、またはその両方) を含むクロスパーティション クエリを実行したときは、DocumentDB SDK からパーティション全体に並列クエリが発行され、部分的に並べ替えられた結果がクライアント側でマージされて、グローバルに並べ替えられた結果が作成されます。

## <a name="execute-stored-procedures"></a>ストアド プロシージャを実行する
最後に、単一のドキュメントでデバイスの集計や最新状態を管理するといった場合に、同じデバイス ID を持つドキュメントに対してアトミック トランザクションを実行することができます。このためには、プロジェクトに次のコードを追加します。

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```

これで終了です。 これらは、パーティション全体にデータ分散を効率的にスケールするためにパーティション キーを使用する Azure Cosmos DB アプリケーションの主要なコンポーネントです。  

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、このチュートリアルで作成したすべてのリソースを Azure Portal で削除してください。

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックしてから、作成したリソースの一意の名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の手順を行いました。 

> [!div class="checklist"]
> * Azure Cosmos DB アカウントの作成
> * パーティション キーを使用したデータベースとコレクションの作成
> * JSON ドキュメントの作成
> * ドキュメントの更新
> * パーティション分割コレクションへのクエリの実行
> * ストアド プロシージャの実行
> * ドキュメントの削除
> * データベースの削除

次のチュートリアルに進んで、Cosmos DB アカウントに追加のデータをインポートできます。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](import-data.md)

