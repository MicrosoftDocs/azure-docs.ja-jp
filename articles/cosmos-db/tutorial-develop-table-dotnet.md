---
title: "Azure Cosmos DB: .NET での Table API を使用した開発 | Microsoft Docs"
description: ".NET を使用した Azure Cosmos DB の Table API による開発方法について"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: arramac
ms.custom: mvc
ms.openlocfilehash: 5322fa5cc8e841ecea97a69d15cf130a4426ad95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: .NET での Table API を使用した開発

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。

このチュートリアルに含まれるタスクは次のとおりです。 

> [!div class="checklist"] 
> * Azure Cosmos DB アカウントを作成する 
> * app.config ファイルで機能を有効にする 
> * [Table API](table-introduction.md) (プレビュー) を使用してテーブルを作成する
> * エンティティをテーブルに追加する 
> * エンティティのバッチを挿入する 
> * 単一のエンティティを取得する 
> * 自動セカンダリ インデックスを使用してエンティティのクエリを行う 
> * エンティティを置換する 
> * エンティティを削除する 
> * テーブルを削除する
 
## <a name="tables-in-azure-cosmos-db"></a>Azure Cosmos DB のテーブル 

Azure Cosmos DB では、スキーマレス設計のキー/値ストアを必要とするアプリケーションのために [Table API](table-introduction.md) (プレビュー) が提供されます。 Azure Cosmos DB で [Azure Table Storage](../storage/common/storage-introduction.md) SDK および REST API を使用できます。 Azure Cosmos DB を使用して、高スループット要件のテーブルを作成できます。 Azure Cosmos DB ではスループットが最適化されたテーブル (通称 "Premium テーブル") がサポートされ、現在パブリック プレビューが行われています。 

Azure Table Storage は、ストレージ要件が高くスループット要件が低いテーブルに対して、引き続き使用できます。 Azure Cosmos DB では、将来の更新時にストレージ最適化テーブルのサポートが導入される予定であり、既存および新規の Azure Table Storage アカウントは Azure Cosmos DB にシームレスにアップグレードされます。

現在 Azure Table Storage を使用している場合、"Premium テーブル" のプレビューには次のメリットがあります。

- マルチホーム機能と[自動または手動のフェールオーバー](regional-failover.md)を備えた、ターンキー [グローバル分散](distribute-data-globally.md)
- すべてのプロパティ ("セカンダリ インデックス") に対するスキーマ非依存自動インデックス作成と高速クエリのサポート 
- 任意の数のリージョンにわたる[ストレージとスループットの独立した拡張](partition-data.md)のサポート
- [テーブルごとの専用スループット](request-units.md)のサポート (1 秒あたりの要求数を数百件から数百万件まで拡張できる)
- [調整可能な 5 つの一貫性レベル](consistency-levels.md)のサポート。アプリケーションのニーズに基づいて、可用性、待機時間、一貫性のトレードオフを行います。
- 1 リージョンで 99.99% の可用性 (可用性を上げるためにリージョンを追加することもでき、一般的な可用性に関して[業界をリードする包括的な SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) が提供される)
- 既存の Azure Storage .NET SDK の利用、アプリケーションのコード変更不要

プレビュー中、Azure Cosmos DB は Table API をサポートするために .NET SDK を使用します。 [Azure Storage Preview SDK](https://aka.ms/premiumtablenuget) は NuGet からダウンロードできます。これには、[Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage) と同じクラスおよびメソッド シグネチャが含まれます。ただし、Table API を使用して Azure Cosmos DB アカウントに接続することもできます。

Azure Table Storage の複雑なタスクについては、次を参照してください。

* [Azure Cosmos DB の概要: Table API](table-introduction.md)
* [.NET 用ストレージ クライアント ライブラリ リファレンス](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)の Table service リファレンス ドキュメント (利用可能な API の詳細について)

### <a name="about-this-tutorial"></a>このチュートリアルについて
このチュートリアルは、Azure Table Storage SDK の知識があり、Azure Cosmos DB を使用して提供される Premium 機能を使用する予定がある開発者が対象です。 「[.NET を使用して Azure Table Storage を使用する](table-storage-how-to-use-dotnet.md)」に基づいて、セカンダリ インデックス、プロビジョニング済みスループット、マルチホームなどの追加機能を活用する方法を説明します。 Azure Portal を使用して Azure Cosmos DB アカウントを作成し、Table アプリケーションを構築およびデプロイする方法も説明します。 テーブルの作成と削除や、テーブル データの挿入、更新、削除、クエリを実行する .NET の例についても説明します。 

まだ Visual Studio 2017 をインストールしていない場合は、**無料**の [Visual Studio 2017 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>How to create a DocumentDB account (DocumentDB アカウントの作成方法)

まず最初に、Azure Portal で Azure Cosmos DB アカウントを作成します。  

> [!TIP]
> * もう Azure Cosmos DB アカウント作成しましたか。 その場合は、「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進みます。
> * 既に Azure DocumentDB アカウントを持っていましたか。 この場合、そのアカウントが Azure Cosmos DB アカウントになります。「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進んでください。  
> * Azure Cosmos DB Emulator を使用する場合は、[Azure Cosmos DB Emulator](local-emulator.md) に関する記事に記載されている手順に従って、エミュレーターをセットアップし、「[Visual Studio ソリューションをセットアップする](#SetupVS)」に進んでください。
<!---Loc Comment: Please, check link [Set up your Visual Studio solution] since it's not redirecting to any location.---> 
>
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

github で Table アプリの複製を作成し、接続文字列を設定して実行します。

1. git ターミナル ウィンドウ (git bash など) を開き、`cd` を実行して作業ディレクトリに移動します。  

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started
    ```

3. Visual Studio でソリューション ファイルを開きます。

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。

1. [Azure Portal](http://portal.azure.com/) で、Azure Cosmos DB アカウントの左のナビゲーションから、**[キー]** をクリックしてから **[読み取り/書き込みキー]** をクリックします。 次の手順では、画面右側のコピー ボタンを使用して、接続文字列を app.config ファイルにコピーします。

2. Visual Studio で app.config ファイルを開きます。 

3. ポータルの URI 値をコピーし (コピー ボタンを使用して)、app.config の account-key の値に設定します。app.config の account-name では、前に作成したアカウント名を使用します。
  
```
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;TableEndpoint=https://account-name.documents.azure.com" />
```

> [!NOTE]
> このアプリを標準の Azure Table Storage で仕様するには、`app.config file` の接続文字列を変更する必要があります。 アカウント名には Table アカウント名、キーには Azure Storage プライマリ キーを使用します。 <br>
>`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;EndpointSuffix=core.windows.net" />`
> 
>

## <a name="build-and-deploy-the-app"></a>アプリを構築してデプロイする
1. Visual Studio の**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 

2. NuGet の **[参照]** ボックスに「***WindowsAzure.Storage-PremiumTable***」と入力します。 **[Include prerelease versions (プレリリースを含める)]** をオンにしてください。

3. 結果から、**WindowsAzure.Storage-PremiumTable** をインストールし、プレビュー ビルド `0.0.1-preview` を選択します。 これにより、Azure Table Storage パッケージとすべての依存関係がインストールされます。

4. Ctrl + F5 キーを押してアプリケーションを実行します。 

これで、データ エクスプローラーに戻って、このテーブル データの表示、クエリ、変更、操作を行うことができます。 

> [!NOTE]
> `app.config file` の接続文字列を変更するだけで、このアプリを Azure Cosmos DB Emulator で使用することができます。 Emulator では次の値を使用してください。 <br>
>`<add key="StorageConnectionString" value=DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=<insertkey>==;TableEndpoint=https://localhost -->`
> 
>

## <a name="azure-cosmos-db-capabilities"></a>Azure Cosmos DB の機能
Azure Cosmos DB では、Azure Table Storage API に含まれないいくつもの機能がサポートされます。 新しい機能は、次の `appSettings` 構成値で有効にすることができます。 プレビューの Azure Storage SDK には、新しいシグネチャまたはオーバーロードを含めていません。 このため、Standard テーブルと Premium テーブルの両方に接続でき、他の Azure Storage サービス (BLOB やキューなど) と連携できます。 


| キー | Description |
| --- | --- |
| TableConnectionMode  | Azure Cosmos DB では 2 つの接続モードがサポートされます。 `Gateway` モードでは、要求は常に Azure Cosmos DB ゲートウェイに対して行われ、そこから対応するデータ パーティションに転送されます。 `Direct` 接続モードでは、クライアントがテーブルとパーティションのマッピングをフェッチし、要求がデータ パーティションに直接行われます。 既定値の `Direct` をお勧めします。  |
| TableConnectionProtocol | Azure Cosmos DB では 2 つの接続プロトコル、`Https` と `Tcp` がサポートされます。 軽量であるため、既定の `Tcp` をお勧めします。 |
| TablePreferredLocations | 読み取りの優先場所 (マルチホーム) をコンマで区切って指定します。 各 Azure Cosmos DB アカウントは 1 から 30 以上のリージョンに関連付けることができます。 各クライアント インスタンスは、低待機時間で読み取りを行えるように、優先順位を付けてこれらのリージョンのサブセットを指定できます。 リージョンは[表示名](https://msdn.microsoft.com/library/azure/gg441293.aspx) (`West US` など) を使用して指定する必要があります。 [マルチホーム API](tutorial-global-distribution-table.md)に関する記事も参照してください。
| TableConsistencyLevel | 適切に定義された 5 個の一貫性レベル、`Strong`、`Session`、`Bounded-Staleness`、`ConsistentPrefix`、`Eventual` から選択して、待機時間、一貫性、可用性のトレードオフを行うことができます。 既定値は `Session`です。 一貫性レベルの選択肢によって、複数リージョンのセットアップでパフォーマンスに大きな違いが生じます。 詳しくは、[一貫性レベル](consistency-levels.md)に関する記事をご覧ください。 |
| TableThroughput | テーブルの予約スループット。1 秒あたりの要求ユニット数 (RU) で表されます。 1 つのテーブルで 1 秒あたり数億の RU をサポートすることができます。 [要求ユニット](request-units.md)に関する記事をご覧ください。 既定値は `400` です。 |
| TableIndexingPolicy | テーブル内のすべての列のセカンダリ インデックス作成が整合性を保って自動的に行われます。 | インデックス作成ポリシーの仕様に準拠する JSON 文字列。 インデックス作成ポリシーを変更して特定の列を含めたり除外したりするには、[インデックス作成ポリシー](indexing-policies.md)に関する記事をご覧ください。 | すべてのプロパティの自動インデックス作成 (文字列に対してハッシュ、数値に対して範囲) |
| TableQueryMaxItemCount | 1 回のラウンド トリップでテーブル クエリごとに返される最大項目数を構成します。 既定値の `-1` では、Azure Cosmos DB が実行時に値を動的に決定できます。 |
| TableQueryEnableScan | クエリがすべてのフィルターでインデックスを使用できない場合、スキャンを使用して実行します。 既定値は `false`です。|
| TableQueryMaxDegreeOfParallelism | クロス パーティション クエリを実行するための並列処理の次数。 `0` は、プリフェッチなしの直列処理、`1` はプリフェッチありの直列処理です。これより値が大きくなるにつれて、並列処理の速度が向上します。 既定値の `-1` では、Azure Cosmos DB が実行時に値を動的に決定できます。 |

既定値を変更するには、Visual Studio のソリューション エクスプローラーから `app.config` ファイルを開きます。 次に示す `<appSettings>` 要素の内容を追加します。 `account-name` をストレージ アカウントの名前に置き換え、`account-key` をアカウントのアクセス キーに置き換えます。 

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
      <!-- Client options -->
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />
      <add key="TableConnectionMode" value="Direct"/>
      <add key="TableConnectionProtocol" value="Tcp"/>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>
      <add key="TableConsistencyLevel" value="Eventual"/>

      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
      <add key="TableIndexingPolicy" value="{""indexingMode"": ""Consistent""}"/>

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

アプリで何が行われているかを簡単に確認してみましょう。 `Program.cs` ファイルを開くと、これらのコード行によって Table リソースが作成されることがわかります。 

## <a name="create-the-table-client"></a>テーブル クライアントを作成する
テーブル アカウントに接続するための `CloudTableClient` を初期化してします。

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
このクライアントは、`TableConnectionMode``TableConnectionProtocol``TableConsistencyLevel`、`TablePreferredLocations` の各構成値を使用して初期化されます (アプリの設定に指定されている場合)。
    
## <a name="create-a-table"></a>テーブルを作成する
次に、`CloudTable` を使用してテーブルを作成します。 Azure Cosmos DB のテーブルは、ストレージとスループットそれぞれを個別に拡張できます。パーティション化はサービスによって自動的に処理されます。 Azure Cosmos DB では、固定サイズのテーブルと無制限のテーブルの両方がサポートされます。 詳しくは、[Azure Cosmos DB でのパーティション分割](partition-data.md)に関する記事をご覧ください。 

```csharp
CloudTable table = tableClient.GetTableReference("people");

table.CreateIfNotExists();
```

テーブルの作成方法に大きな違いがあります。 Azure Cosmos DB ではスループットが予約されます。これはトランザクションの使用量に基づく Azure Storage のモデルとは異なります。 予約モデルには、2 つの主な利点があります。

* スループットは専用として予約されるため、要求レートがプロビジョニングされたスループット以下である場合にもスロットルされることはありません。
* 予約モデルは、[スループットが高いワークロードではコスト効率に優れています](key-value-store-cost.md)。

既定のスループットを構成するには、`TableThroughput` の設定で 1 秒あたりの RU (要求ユニット数) を構成します。 

1 KB のエンティティの読み取りは 1 RU に正規化され、他の操作は、CPU、メモリ、IOPS の消費量に基づく固定 RU 値に正規化されます。 [Azure Cosmos DB の要求ユニット](request-units.md)の詳細についてはこちらを参照してください。

> [!NOTE]
> 現在 Table Storage SDK ではスループットの変更はサポートされませんが、Azure Portal または Azure CLI を使用するといつでもスループットをすぐに変更できます。

次には、Azure Table Storage SDK を使用する単純な読み取りと書き込み (CRUD) の操作について説明します。 このチュートリアルでは、Azure Cosmos DB によって数ミリ秒という低待機時間と高速クエリが実現することが予測されます。

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
Azure Table Storage のエンティティは `TableEntity` クラスから拡張されます。`PartitionKey` プロパティと `RowKey` プロパティを持つ必要があります。 ここでカスタマー エンティティの定義例を示します。

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

次のスニペットは、Azure Storage SDK を使用してエンティティを挿入する方法を示します。 Azure Cosmos DB は、世界中であらゆるスケールで低待機時間を保証するように設計されています。

Azure Cosmos DB アカウントと同じリージョンで実行するアプリケーションの場合、書き込みが完了する時間は p99 では 15 ミリ秒未満、p50 では 6 ミリ秒以内です。 この待機時間から、書き込みがクライアントで確認されるのは、同期レプリケートと永続コミットが行われ、すべてのコンテンツのインデックスが作成された後であることがわかります。

Azure Cosmos DB の Table API はプレビュー段階です。 一般提供では、他の Azure Cosmos DB API と同様に p99 の待機時間が SLA によって保証されます。 

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>エンティティのバッチを挿入する
Azure Table Storage ではバッチ操作 API がサポートされます。これを使用すると、更新、削除、挿入を 1 つのバッチ操作で組み合わせることができます。 Azure Table Storage のバッチ API には制限事項がありますが、Azure Cosmos DB にはその一部がありません。 たとえば、1 つのバッチ内で複数の読み取りを実行し、1 つのバッチ内で同じエンティティに対して複数の書き込みを実行できます。1 つのバッチで 100 件の操作という制限もありません。 

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>単一のエンティティを取得する
Azure Cosmos DB での取得 (GET) は、同じ Azure リージョンの場合、p99 で 10 ミリ秒未満、p50 では 1 ミリ秒以内で完了します。 低待機時間読み取りのためアカウントに任意の数のリージョンを追加することができ、`TablePreferredLocations` を設定して、ローカル リージョンから読み取るようにアプリケーションをデプロイできます ("マルチホーム")。 

単一のエンティティを取得するには、次のスニペットを使用します。

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> マルチホーム API について詳しくは、[複数リージョンの開発](tutorial-global-distribution-table.md)に関する記事をご覧ください。
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>自動セカンダリ インデックスを使用してエンティティのクエリを行う
テーブルのクエリには `TableQuery` クラスを使用します。 Azure Cosmos DB には、書き込みに対して最適化されたデータベース エンジンがあり、テーブル内のすべての列のインデックスを自動的に作成します。 Azure Cosmos DB でのインデックス作成はスキーマに依存しません。 したがって、複数の行でスキーマが異なる場合、またはスキーマが時間経過に伴って変化する場合でも、自動的にインデックスが作成されます。 Azure Cosmos DB では自動セカンダリ インデックスがサポートされるため、すべてのプロパティに対するクエリはこのインデックスを使用することができ、効率よく処理されます。

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

プレビューでは、Azure Cosmos DB は、Table API について Azure Table Storage と同じクエリの機能をサポートします。 また、並べ替え、集計、地理空間クエリ、階層、さまざまな組み込み関数もサポートしています。 将来のサービス更新プログラムで Table API に追加機能が提供される予定です。 このような機能の概要については、[Azure Cosmos DB のクエリ](documentdb-sql-query.md)に関する記事をご覧ください。 

## <a name="replace-an-entity"></a>エンティティを置換する
エンティティを更新するには、そのエンティティを Table service から取得し、エンティティ オブジェクトを変更して、変更を Table service に戻して保存します。 次のコードは、既存のユーザーの電話番号を変更します。 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
同様に、`InsertOrMerge` または `Merge` の操作を実行できます。  

## <a name="delete-an-entity"></a>エンティティを削除する
エンティティは、取得後に簡単に削除できます。エンティティの更新のときと同じパターンを使用します。 次のコードは、ユーザー エンティティを取得して削除します。

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>テーブルを削除する
最後に、次のコード例は、ストレージ アカウントからテーブルを削除します。 Azure Cosmos DB では、テーブルを削除してからすぐに再作成できます。

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

このアプリの使用を続けない場合は、以下の手順に従い、このチュートリアルで作成したすべてのリソースを Azure Portal で削除してください。   

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックしてから、作成したリソースの名前をクリックします。  
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Cosmos DB で Table API の使用を開始する方法について説明し、次のタスクを実行しました。 

> [!div class="checklist"] 
> * Azure Cosmos DB アカウントの作成 
> * app.config ファイルでの機能の有効化 
> * テーブルの作成 
> * エンティティのテーブルへの追加する 
> * エンティティのバッチの挿入 
> * 単一のエンティティの取得 
> * 自動セカンダリ インデックスを使用したエンティティのクエリ 
> * エンティティの置換 
> * エンティティの削除 
> * テーブルの削除  

次のチュートリアルに進み、テーブル データのクエリについて学ぶことができます。 

> [!div class="nextstepaction"]
> [Table API を使用したクエリ](tutorial-query-table.md)
