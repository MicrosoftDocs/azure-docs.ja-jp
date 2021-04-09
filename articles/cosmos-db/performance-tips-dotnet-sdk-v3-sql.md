---
title: .NET SDK v3 用の Azure Cosmos DB のパフォーマンスに関するヒント
description: Azure Cosmos DB .NET v3 SDK のパフォーマンスを向上させるために役立つクライアント構成オプションについて説明します。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: jawilley
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 06fb087744ff4ecd96bee7a26e4a796e87866322
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433677"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Azure Cosmos DB と .NET のパフォーマンスに関するヒント
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB は、高速で柔軟性に優れた分散データベースです。待ち時間とスループット レベルが保証されており、シームレスにスケーリングできます。 Azure Cosmos DB でデータベースをスケーリングするために、アーキテクチャを大きく変更したり、複雑なコードを記述したりする必要はありません。 スケールアップとスケールダウンは、API 呼び出しを 1 回行うだけの簡単なものです。 詳細については、[コンテナーのスループットのプロビジョニング](how-to-provision-container-throughput.md)または[データベースのスループットのプロビジョニング](how-to-provision-database-throughput.md)に関するページを参照してください。 

Azure Cosmos DB にはネットワーク呼び出しによってアクセスするため、[SQL .NET SDK](sql-api-sdk-dotnet-standard.md) を使うと、最高のパフォーマンスを実現するためにクライアント側の最適化を行うことができます。

データベースのパフォーマンスを向上させる場合は、次のセクションで示すオプションを検討してください。

## <a name="hosting-recommendations"></a>ホスティングの推奨事項

**クエリ集中型ワークロードの場合は、Linux または Windows 32 ビットのホスト処理ではなく、Windows 64 ビットのホスト処理を使用する**

パフォーマンス向上のため、Windows 64 ビットのホスト処理をお勧めします。 SQL SDK には、ローカル環境でクエリを解析して最適化するためのネイティブ ServiceInterop.dll が含まれています。 ServiceInterop.dll は、Windows x64 プラットフォームでのみサポートされています。 

Linux および ServiceInterop.dll を使用できない他のサポート対象外プラットフォームでは、最適化されたクエリを取得するために、ゲートウェイに対して追加のネットワーク呼び出しが行われます。 

ここに示す 4 種類のアプリケーションでは、既定で 32 ビットのホスト処理が使用されます。 アプリケーションの種類のホスト処理を 64 ビット処理に変更するには、次の手順に従います。

- **実行可能アプリケーションの場合**: **[プロジェクトのプロパティ]** ウィンドウの **[ビルド]** ペインで、[[プラットフォーム ターゲット]](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) を **x64** に設定します。

- **Vstest ベースのテスト プロジェクトの場合**:Visual Studio の **[テスト]** メニューで、 **[テスト]**  >  **[テストの設定]** の順に選択し、 **[既定のプロセッサ アーキテクチャ]** を **X64** に設定します。

- **ローカルにデプロイされた ASP.NET Web アプリケーションの場合**: **[ツール]**  >  **[オプション]**  >  **[プロジェクトとソリューション]**  >  **[Web プロジェクト]** の順に選択し、 **[Web サイトおよびプロジェクト用 IIS Express の 64 ビット バージョンを使用する]** を選択します。

- **Azure にデプロイされた ASP.NET Web アプリケーションの場合**:Azure portal の **[アプリケーション設定]** で、**64 ビット** のプラットフォームを選択します。

> [!NOTE] 
> 既定では、新しい Visual Studio プロジェクトは、 **[任意の CPU]** に設定されます。 **x86** に切り替わらないように、プロジェクトを **x64** に設定することをお勧めします。 **[任意の CPU]** に設定されたプロジェクトは、x86 のみの依存関係が追加されると、簡単に **x86** に切り替わる可能性があります。<br/>
> ServiceInterop.dll ファイルは、SDK DLL が実行されるフォルダーに配置する必要があります。 これは、手動で DLL をコピーする場合や、カスタム ビルドまたはデプロイ システムを使用する場合にのみ、問題になります。
    
**サーバー側のガベージ コレクションを有効にする**

ガベージ コレクションの頻度を減らした方がよい場合もあります。 .NET では、[gcServer](/dotnet/core/run-time-config/garbage-collector#flavors-of-garbage-collection) を `true` に設定します。

**クライアント ワークロードをスケールアウトする**

高スループット レベルでテストしたり、1 秒あたりの要求ユニット数 (RU/秒) が 50,000 を超えるレートでテストしたりすると、クライアント アプリケーションがワークロードのボトルネックになる可能性があります。 この原因として、マシンの CPU またはネットワークの使用率に上限が設定されている可能性があります。 この状態に達しても、クライアント アプリケーションを複数のサーバーにスケールアウトすることで引き続き同じ Azure Cosmos DB アカウントで対応できます。

> [!NOTE] 
> CPU 使用率が高いと、待ち時間が長くなり、要求タイムアウトの例外が発生する可能性があります。

## <a name="networking"></a>ネットワーク
<a id="direct-connection"></a>

**接続ポリシー:直接接続モードを使用する**

.NET V3 SDK の既定の接続モードは直接です。 `CosmosClientOptions` で `CosmosClient` インスタンスを作成するときに、接続モードを構成します。  さまざまな接続オプションについては、[接続モード](sql-sdk-connection-modes.md)に関する記事を参照してください。

```csharp
string connectionString = "<your-account-connection-string>";
CosmosClient client = new CosmosClient(connectionString,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

**一時的なポートの不足**

インスタンスで接続量が多いか、ポートの使用率が高い場合は、まず、クライアント インスタンスがシングルトンであることを確認します。 言い換えると、クライアント インスタンスは、アプリケーションの有効期間にわたって一意である必要があります。

クライアントが TCP プロトコル上で実行されている場合は、有効期間が長い接続を使用して待ち時間が最適化されます。 これは、非アクティブ状態が 2 分間続くと接続を終了する HTTPS プロトコルとは対照的です。

アクセス頻度が低く、ゲートウェイ モード アクセスと比べて接続数が多い場合は、次のことが可能です。

* [CosmosClientOptions.PortReuseMode](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode) プロパティを `PrivatePortPool` に構成します (フレームワーク バージョン 4.6.1 以降および .NET Core バージョン 2.0 以降で有効)。 このプロパティにより、SDK はさまざまな Azure Cosmos DB の宛先エンドポイントに対して一時的なポートの小さなプールを使用できます。
* [CosmosClientOptions.IdleConnectionTimeout](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout) プロパティを 10 分以上に構成します。 推奨値は 20 分～ 24 時間です。

<a id="same-region"></a>

**パフォーマンスを確保するために同じ Azure リージョン内にクライアントを併置する**

可能であれば、Azure Cosmos DB を呼び出すアプリケーションを Azure Cosmos DB データベースと同じリージョンに配置します。 大ざっぱな比較ですが、Azure Cosmos DB の呼び出しは、同じリージョン内であれば 1 から 2 ミリ秒 (ms) 以内で終了するのに対し、米国西部と米国東部との間では待ち時間が 50 ミリ秒を超えます。 要求がクライアントから Azure データセンターの境界まで流れるときに使用されるルートに応じて、この待機時間が要求ごとに異なる可能性があります。 

最短の待ち時間は、プロビジョニングされた Azure Cosmos DB エンドポイントと同じ Azure リージョン内に呼び出し元アプリケーションを配置することによって実現できます。 利用可能なリージョンの一覧については、「[Azure リージョン](https://azure.microsoft.com/regions/#services)」をご覧ください。

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="クライアントを同じリージョンに併置する" border="false":::

   <a id="increase-threads"></a>

**スレッドまたはタスクの数を増やす**

Azure Cosmos DB の呼び出しはネットワーク経由で行われるため、クライアント アプリケーションで要求間の待ち時間を最短にするために、要求のコンカレンシーの次数を変えることが必要な場合があります。 たとえば、.NET の [タスク並列ライブラリ](/dotnet/standard/parallel-programming/task-parallel-library-tpl)を使用する場合、Azure Cosmos DB に対する読み取りタスクまたは書き込みタスクを 100 件単位で作成してください。

**高速ネットワークの有効化**
 
待ち時間と CPU ジッターを減らすために、クライアントの仮想マシンでは高速ネットワークを有効にすることをお勧めします。 詳細については、「[高速ネットワークを使った Linux 仮想マシンの作成](../virtual-network/create-vm-accelerated-networking-powershell.md)」または「[高速ネットワークを使った Windows 仮想マシンの作成](../virtual-network/create-vm-accelerated-networking-cli.md)」を参照してください。

## <a name="sdk-usage"></a>SDK の使用

**最新の SDK をインストールする**

Azure Cosmos DB SDK は、最適なパフォーマンスを提供するために頻繁に改善されています。 最新の SDK を確認し、改善点をレビューするには、[Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) を参照してください。

**ストリーム API を使用する**

[.NET SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) には、シリアル化せずにデータを受信して返すことができるストリーム API シリーズが含まれています。 

SDK からの応答を直接使用せずに他のアプリケーション層にリレーする中間層アプリケーションでは、ストリーム API を利用するメリットがあります。 ストリーム処理の例については、[項目管理](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement)のサンプルを参照してください。

**アプリケーションの有効期間中はシングルトン Azure Cosmos DB クライアントを使用する**

各 `CosmosClient` インスタンスはスレッドセーフであり、直接モードで動作しているときには効率的な接続管理とアドレスのキャッシュが実行されます。 効率的な接続管理と SDK クライアントのパフォーマンス向上を実現するために、アプリケーションの有効期間中は、`AppDomain` ごとに単一のインスタンスを使用することをお勧めします。

Azure Functions で作業する場合、インスタンスは既存の[ガイドライン](../azure-functions/manage-connections.md#static-clients)にも従って、1 つのインスタンスを維持する必要があります。

<a id="max-connection"></a>

**書き込み操作に対するコンテンツ応答を無効にする**

高負荷の作成ペイロードがあるワークロードでは、`EnableContentResponseOnWrite` 要求オプションを `false` に設定します。 サービスは、作成または更新されたリソースを SDK に返さなくなります。 通常、アプリケーションには作成済みのオブジェクトがあるため、サービスから返される必要はありません。 ヘッダー値には、まだ要求の料金と同様にアクセスできます。 応答コンテンツを無効にすると、SDK がメモリを割り当てたり応答の本文をシリアル化したりする必要がなくなるため、パフォーマンスを向上させることができます。 また、ネットワーク帯域幅の使用量も削減され、パフォーマンスがさらに向上します。  

```csharp
ItemRequestOptions requestOptions = new ItemRequestOptions() { EnableContentResponseOnWrite = false };
ItemResponse<Book> itemResponse = await this.container.CreateItemAsync<Book>(book, new PartitionKey(book.pk), requestOptions);
// Resource will be null
itemResponse.Resource
```

**待ち時間ではなくスループットを最適化するために一括処理を有効にする**

ワークロードが大量のスループットを必要とし、待ち時間はそれほど重要ではないシナリオでは、*一括処理* を有効にします。 一括処理機能を有効にする方法と、その機能を使用する必要があるシナリオの詳細については、[一括処理の概要](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk)に関するページを参照してください。

**ゲートウェイ モードを使用するときにホストあたりの System.Net MaxConnections を増やす**

ゲートウェイ モードを使用すると、Azure Cosmos DB の要求は HTTPS/REST を介して行われます。 それらは、ホスト名または IP アドレスごとの既定の接続数の上限に従います。 場合によっては、Azure Cosmos DB に対する複数の同時接続をクライアント ライブラリで使用できるよう、`MaxConnections` を高い値 (100 ～ 1,000) に増やす必要があります。 .NET SDK 1.8.0 以降では、[ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) の既定値は 50 です。 値を変更するには、[`Documents.Client.ConnectionPolicy.MaxConnectionLimit`](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) を高い値に設定します。

**パーティション分割コレクションに対する並列クエリを調整する**

SQL .NET SDK では、並列クエリがサポートされています。この機能を使用すると、パーティション分割コンテナーにクエリを並列的に実行できます。 詳細については、SDK の操作に関連した[コード サンプル](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs)を参照してください。 並列クエリは、シリアル クエリよりもクエリの待機時間とスループットを向上させるように設計されています。 

並列クエリには、要件に合わせてチューニングできる 2 つのパラメーターがあります。 

- **MaxConcurrency**: 同時にクエリを実行できるパーティションの最大数を制御します。

   並列クエリは、複数のパーティションに並列にクエリを実行することによって機能します。 ただし、個々のパーティションからのデータは、クエリごとに順番に取得されます。 そのため、[SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) の `MaxConcurrency` をパーティションの数に設定すると、その他のすべてのシステムの条件が同じであれば、クエリのパフォーマンスを最善にできる可能性が最大になります。 パーティションの数がわからない場合は、並列処理の次数を高い数値に設定できます。 システムにより、並列処理の次数として最小値 (パーティションの数、ユーザー指定の入力) が選択されます。

    並列クエリが最も有効に機能するのは、クエリに対するデータがすべてのパーティションに均等に分散している場合であることに注意する必要があります。 パーティション分割されたコレクションが、クエリによって返されるすべてまたは大部分のデータがわずかな数のパーティション (最悪の場合は 1 つのパーティション) に集中するように分割されている場合、それらのパーティションがクエリのパフォーマンスのボトルネックになります。
   
- **MaxBufferedItemCount**:プリフェッチされる結果の数を制御します。

   並列クエリは、結果の現在のバッチがクライアントによって処理されている間に結果をプリフェッチするように設計されています。 このプリフェッチは、クエリの全体的な遅延の削減に役立ちます。 `MaxBufferedItemCount` パラメーターは、プリフェッチされる結果の数を制限します。 `MaxBufferedItemCount` を、返される結果の予期される数 (またはそれ以上の数) に設定すると、クエリに対するプリフェッチの効果が最大になります。

   プリフェッチは、並列処理の次数とは無関係に同じように動作し、すべてのパーティションからのデータに対して単一のバッファーが存在します。  

**RetryAfter 間隔でバックオフを実装する**

パフォーマンス テストでは、調整される要求の割合がわずかになるまで負荷を上げる必要があります。 要求がスロットル状態になった場合、クライアント アプリケーション側でバックオフ値を適用し、サーバー側によって指定された再試行間隔のスロットル時間を後退させるようにしてください。 バックオフにより、再試行までの待ち時間を最小限に抑えることができます。 

詳細については、[RetryAfter](/dotnet/api/microsoft.azure.cosmos.cosmosexception.retryafter#Microsoft_Azure_Cosmos_CosmosException_RetryAfter) に関するページを参照してください。
    
次の例のように、追加の診断情報をログに記録し、待ち時間に関する問題をトラブルシューティングするメカニズムがあります。 読み取り待ち時間の長い要求についての診断文字列をログに記録できます。 取得した診断文字列は、特定の要求に対して *429* エラーを受け取った回数を把握するのに役立ちます。

```csharp
ItemResponse<Book> readItemResponse = await this.cosmosContainer.ReadItemAsync<Book>("ItemId", new PartitionKey("PartitionKeyValue"));
readItemResponse.Diagnostics.ToString(); 
```

**スレッドまたはタスクの数を増やす**

この記事の「ネットワーク」セクションの「[スレッドまたはタスクの数を増やす](#increase-threads)」を参照してください。

## <a name="indexing-policy"></a>インデックス作成ポリシー
 
**インデックス作成から未使用のパスを除外して書き込みを高速化する**

Azure Cosmos DB のインデックス作成ポリシーでは、インデックス作成パス (IndexingPolicy.IncludedPaths および IndexingPolicy.ExcludedPaths) を使用して、インデックス作成に含めたり除外したりするドキュメント パスも指定できます。 

必要なパスだけでインデックスを作成すると、クエリのパターンが事前にわかっている場合に、書き込みパフォーマンスの向上、書き込み操作に対する RU 料金の削減、およびインデックス ストレージの削減が可能になります。 これは、インデックス作成コストは、インデックスが作成される一意のパスの数に直接関係するためです。 たとえば、次のコードは、ワイルドカード "*" を使用して、ドキュメントのセクション全体 (サブツリー) をインデックス作成から除外する方法を示しています。

```csharp
var containerProperties = new ContainerProperties(id: "excludedPathCollection", partitionKeyPath: "/pk" );
containerProperties.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
containerProperties.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
Container container = await this.cosmosDatabase.CreateContainerAsync(containerProperties);
```

詳細については、[Azure Cosmos DB インデックス作成ポリシー](index-policy.md)に関するページをご覧ください。

## <a name="throughput"></a>スループット
<a id="measure-rus"></a>

**低 RU/秒の使用状況を測定して調整する**

Azure Cosmos DB には、データベース操作の豊富なセットが用意されています。 ユニバーサル ディスク フォーマット (UDF)、ストアド プロシージャ、トリガーを使用したリレーショナル クエリや階層クエリなどの操作があります。これらの操作はすべて、データベース コレクション内のドキュメントに対して実行できます。 

これらの操作のそれぞれに関連付けられたコストは、操作を完了するために必要な CPU、IO、およびメモリに応じて異なります。 ハードウェア リソースの検討や管理をする代わりに、要求ユニットをさまざまなデータベース操作の実行やアプリケーション要求の処理に必要なリソースの 1 つのメジャーとして考えることができます。

コンテナーごとに設定された[要求ユニット](request-units.md)の数に基づいて、スループットをプロビジョニングします。 要求単位の消費は 1 秒あたりのレートとして評価されます。 コンテナーのプロビジョニング済み要求ユニット レートを超過したアプリケーションは、レートがそのコンテナーにプロビジョニングされているレベルを下回るまで制限されます。 アプリケーションでより高いスループットが必要になった場合は、追加の要求ユニットをプロビジョニングしてスループットを増やすことができます。

クエリの複雑さは、操作で消費される要求ユニット数に影響します。 述語の数、述語の特性、UDF ファイルの数、ソース データ セットのサイズのすべてがクエリ操作のコストに影響します。

操作 (作成、更新、または削除) のオーバーヘッドを測定するには、[x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) ヘッダー (あるいは、.NET SDK の `ResourceResponse\<T>` または `FeedResponse\<T>` の同等の `RequestCharge` プロパティ) を調べて、これらの操作で使用される要求ユニット数を測定します。

```csharp
// Measure the performance (Request Units) of writes
ItemResponse<Book> response = await container.CreateItemAsync<Book>(myBook, new PartitionKey(myBook.PkValue));
Console.WriteLine("Insert of item consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
FeedIterator<Book> queryable = container.GetItemQueryIterator<ToDoActivity>(queryString);
while (queryable.HasMoreResults)
    {
        FeedResponse<Book> queryResponse = await queryable.ExecuteNextAsync<Book>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

このヘッダーで返される要求の使用量は、プロビジョニングしたスループット (2,000 RU/秒) の一部です。 たとえば、上記のクエリが 1 KB のドキュメントを 1000 個返した場合、この操作のコストは 1000 になります。 そのため、後続の要求をレート制限する前に、サーバーは 1 秒以内にこのような要求を 2 つだけ受け付けます。 詳細については、[要求ユニット](request-units.md)に関する記事および[要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)のページを参照してください。
<a id="429"></a>

**レート制限と大きすぎる要求レートに対処する**

クライアントがアカウントの予約済みスループットを超えようとしても、サーバーでパフォーマンスの低下が発生することはなく、予約済みのレベルを超えてスループット容量が使用されることもありません。 サーバーは、RequestRateTooLarge (HTTP 状態コード 429) を使用して要求をプリエンプティブに終了します。 それからは、要求を再試行する前にユーザーが待機する必要がある時間 (ミリ秒単位) を示す、[x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) ヘッダーが返されます。

```xml
    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100
```

SDK はすべてこの応答を暗黙的にキャッチし、サーバーが指定した retry-after ヘッダーを優先して要求を再試行します。 アカウントに複数のクライアントが同時アクセスしている状況でなければ、次回の再試行は成功します。

累積的に動作する複数のクライアントがあり、要求レートを常に超えている場合は、現在クライアントによって内部的に 9 に設定される既定の再試行回数では、十分ではない可能性があります。 このような場合、クライアントではアプリケーションに対して状態コード 429 の CosmosException がスローされます。 

`CosmosClientOptions` インスタンスで `RetryOptions` を設定することにより、既定の再試行回数を変更できます。 既定では、要求レートを超えて要求が続行されている場合に、30 秒の累積待ち時間を過ぎると、状態コードが 429 の CosmosException が返されます。 このエラーは、現在の値が既定値の 9 かユーザー定義の値かにかかわらず、現在の再試行回数が最大再試行回数より少ない場合でも返されます。

自動再試行動作は、ほとんどのアプリケーションで回復性と使いやすさを向上させるのに役立ちます。 ただし、パフォーマンス ベンチマークを実行しているときは (特に待機時間を測定するとき)、最適な動作ではない可能性があります。 実験でサーバー スロットルが発生し、クライアント SDK によって警告なしに再試行が行われると、クライアントが監視する待機時間が急増します。 パフォーマンスの実験中に待ち時間が急増するのを回避するには、各操作で返される使用量を測定し、予約済みの要求レートを下回った状態で要求が行われていることを確認します。 

詳細については、[要求ユニット](request-units.md)に関する記事を参照してください。

**スループットを向上させるためにサイズの小さいドキュメントに合わせて設計する**

特定の操作の要求の使用量 (要求処理コスト) は、ドキュメントのサイズに直接関係します。 サイズの大きいドキュメントの操作は、サイズの小さいドキュメントの操作よりもコストがかかります。

## <a name="next-steps"></a>次のステップ
少数のクライアント コンピューターでの高パフォーマンス シナリオで Azure Cosmos DB の評価に使用されるサンプル アプリケーションについては、「[Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)」を参照してください。

スケーリングと高パフォーマンスのためのアプリケーションの設計について詳しくは、「[Azure Cosmos DB でのパーティション分割とスケーリング](partitioning-overview.md)」をご覧ください。