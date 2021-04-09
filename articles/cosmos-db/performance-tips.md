---
title: .NET SDK v2 用の Azure Cosmos DB のパフォーマンスに関するヒント
description: Azure Cosmos DB .NET v2 SDK のパフォーマンスを向上させるためのクライアント構成オプションについて説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: sngun
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 57b3d5853f83fc7ee75538d7966f5e20b1a64cd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428951"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Azure Cosmos DB と .NET SDK v2 のパフォーマンスに関するヒント
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB は、高速で柔軟性に優れた分散データベースです。待機時間とスループットが保証されており、シームレスにスケーリングできます。 Azure Cosmos DB でデータベースをスケーリングするために、アーキテクチャを大きく変更したり、複雑なコードを記述したりする必要はありません。 スケールアップとスケールダウンは、API 呼び出しを 1 回行うだけの簡単なものです。 詳細については、[コンテナーのスループットをプロビジョニングする方法](how-to-provision-container-throughput.md)または[データベースのスループットをプロビジョニングする方法](how-to-provision-database-throughput.md)に関するページを参照してください。 ただし、Azure Cosmos DB にはネットワーク呼び出しによってアクセスするため、[SQL .NET SDK](sql-api-sdk-dotnet-standard.md) を使うと、最高のパフォーマンスを実現するためにクライアント側の最適化を行うことができます。

そのため、データベースのパフォーマンスを向上させる場合は、次のオプションを検討してください。

## <a name="upgrade-to-the-net-v3-sdk"></a>.NET V3 SDK へのアップグレード

[.NET v3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) がリリースされました。 .NET v3 SDK を使用する場合、次の情報については、[.NET v3 パフォーマンス ガイド](performance-tips-dotnet-sdk-v3-sql.md)を参照してください。

- 直接 TCP モードの規定値
- Stream API のサポート
- System.Text.JSON の使用を許可するカスタム シリアライザーのサポート
- バッチと一括の統合サポート

## <a name="hosting-recommendations"></a>ホスティングの推奨事項

**クエリ集中型ワークロードの場合は、Linux または Windows 32 ビットのホスト処理ではなく、Windows 64 ビットのホスト処理を使用する**

パフォーマンス向上のため、Windows 64 ビットのホスト処理をお勧めします。 SQL SDK には、ローカル環境でクエリを解析して最適化するためのネイティブ ServiceInterop.dll が含まれています。 ServiceInterop.dll は、Windows x64 プラットフォームでのみサポートされています。 Linux および ServiceInterop.dll を使用できない他のサポート対象外プラットフォームでは、最適化されたクエリを取得するために、ゲートウェイに対して追加のネットワーク呼び出しが行われます。 次の種類のアプリケーションでは、既定で 32 ビットのホスト処理が使用されます。 ホスト処理を 64 ビット処理に変更するには、アプリケーションの種類に基づいて次の手順のようにします。

- 実行可能なアプリケーションの場合は、 **[プロジェクトのプロパティ]** ウィンドウの **[ビルド]** タブで [[プラットフォーム ターゲット]](/visualstudio/ide/how-to-configure-projects-to-target-platforms?preserve-view=true&view=vs-2019) を **[x64]** に設定することで、ホスト処理を変更できます。

- VSTest ベースのテスト プロジェクトの場合は、Visual Studio の **[テスト]** メニューで **[テスト]**  >  **[テストの設定]**  >  **[既定のプロセッサ アーキテクチャ] > [X64]** の順に選択することで、ホスト処理を変更できます。

- ローカルでデプロイされた ASP.NET Web アプリケーションの場合は、 **[ツール]**  >  **[オプション]**  >  **[プロジェクトおよびソリューション]**  >  **[Web プロジェクト]** の順に選択して、 **[Web サイトおよびプロジェクト用 IIS Express の 64 ビット バージョンを使用する]** をオンにすることで、ホスト処理を変更できます。

- Azure にデプロイされた ASP.NET Web アプリケーションの場合は、Azure portal の **[アプリケーションの設定]** で **64 ビット** プラットフォームを選択することで、ホスト処理を変更できます。

> [!NOTE] 
> 既定では、新しい Visual Studio プロジェクトは、 **[任意の CPU]** に設定されます。 **x86** に切り替わらないように、プロジェクトを **x64** に設定することをお勧めします。 **[任意の CPU]** に設定されたプロジェクトは、x86 のみの依存関係が追加されると、簡単に **x86** に切り替わる可能性があります。<br/>
> ServiceInterop.dll は、SDK DLL が実行されるフォルダーに配置する必要があります。 これは、手動で DLL をコピーする場合、またはカスタム ビルドおよびデプロイ システムを使用する場合にのみ、問題になります。
    
**サーバー側のガベージ コレクション (GC) を有効にする**

ガベージ コレクションの頻度を減らした方がよい場合もあります。 .NET では、[gcServer](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element) を `true` に設定します。

**クライアント ワークロードをスケールアウトする**

高スループット レベル (1 秒あたりの要求ユニット数が 50,000 超) でテストを行っている場合、コンピューターが CPU 使用率またはネットワーク使用率の上限に達したことでクライアント アプリケーションがボトルネックになることがあります。 この状態に達しても、クライアント アプリケーションを複数のサーバーにスケールアウトすることで引き続き同じ Azure Cosmos DB アカウントで対応できます。

> [!NOTE] 
> CPU 使用率が高いと、待ち時間が長くなり、要求タイムアウトの例外が発生する可能性があります。

## <a name="networking"></a><a id="networking"></a>ネットワーク

**接続ポリシー:直接接続モードを使用する**

.NET V2 SDK の既定の接続モードはゲートウェイです。 `ConnectionPolicy` パラメーターを使用して `DocumentClient` インスタンスを構築するときに接続モードを構成します。 直接モードを使用する場合、`ConnectionPolicy` パラメーターを使用して `Protocol` を設定する必要もあります。 さまざまな接続オプションについては、[接続モード](sql-sdk-connection-modes.md)に関する記事を参照してください。

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

**一時的なポートの不足**

インスタンスで接続量が多いか、ポートの使用率が高い場合は、まず、クライアント インスタンスがシングルトンであることを確認します。 言い換えると、クライアント インスタンスは、アプリケーションの有効期間にわたって一意である必要があります。

TCP プロトコルで実行されている場合、クライアントでは、非アクティブな状態が 2 分続くと接続を終了する HTTPS プロトコルとは異なり、有効期間の長い接続を使用して待機時間が最適化されます。

アクセス頻度が低く、ゲートウェイ モード アクセスと比べて接続数が多い場合は、次のことが可能です。

* [ConnectionPolicy.PortReuseMode](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) プロパティを `PrivatePortPool` に構成します (フレームワーク バージョン >= 4.6.1 および .NET core バージョン >= 2.0 で有効):このプロパティにより、SDK は異なる Azure Cosmos DB の宛先エンドポイントに対して一時的なポートの小さなプールを使用できます。
* [ConnectionPolicy.IdleConnectionTimeout](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) プロパティを構成します。これは 10 分以上である必要があります。 推奨値は 20 分から 24 時間です。

**OpenAsync を呼び出して最初の要求での開始時の待機時間を回避する**

既定では、最初の要求でアドレス ルーティング テーブルを取得する必要があるため、最初の要求の待機時間が長くなります。 [SDK V2](sql-api-sdk-dotnet.md) を使用しているとき、最初の要求でこの開始時の待機時間を回避するには、初期化中に `OpenAsync()` を 1 回呼び出します。 呼び出しは次のようになります: `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` によって、アカウント内のすべてのコンテナーのアドレス ルーティング テーブルを取得する要求が生成されます。 アカウントに多数のコンテナーがあるにも関わらず、アプリケーションではそのサブセットにしかアクセスしないようなケースでは、`OpenAsync` によって必要以上のトラフィックが生成され、初期化に時間がかかることがあります。 このシナリオではアプリケーションの起動速度が低下するため、`OpenAsync` を使用しても効果がない可能性があります。

**パフォーマンスを確保するために、同じ Azure リージョン内にクライアントを併置する**

可能であれば、Azure Cosmos DB を呼び出すアプリケーションを Azure Cosmos DB データベースと同じリージョンに配置します。 大ざっぱな比較ですが、Azure Cosmos DB の呼び出しは、同じリージョン内であれば 1 から 2 ミリ秒以内で完了するのに対し、米国西部と米国東部との間では待ち時間が 50 ミリ秒を超えます。 要求がクライアントから Azure データセンターの境界まで流れるときに使用されるルートに応じて、この待機時間が要求ごとに異なる可能性があります。 最短の待機時間は、プロビジョニングされた Azure Cosmos DB エンドポイントと同じ Azure リージョン内に呼び出し元アプリケーションを配置することによって実現できます。 利用可能なリージョンの一覧については、「[Azure リージョン](https://azure.microsoft.com/regions/#services)」をご覧ください。

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB の接続ポリシー" border="false":::

**スレッドまたはタスクの数を増やす**
<a id="increase-threads"></a>

Azure Cosmos DB の呼び出しはネットワーク経由で行われるため、クライアント アプリケーションで要求間の待機時間を最短にするために、要求の並列処理の次数を変えることが必要な場合があります。 たとえば、.NET の [タスク並列ライブラリ](/dotnet/standard/parallel-programming/task-parallel-library-tpl)を使用する場合、Azure Cosmos DB に対する読み取りタスクまたは書き込みタスクを 100 件単位で作成してください。

**高速ネットワークの有効化**
 
待機時間と CPU ジッターを減らすために、クライアントの仮想マシンでは高速ネットワークを有効にすることをお勧めします。 「[高速ネットワークを使った Windows 仮想マシンの作成](../virtual-network/create-vm-accelerated-networking-powershell.md)」または「[高速ネットワークを使った Linux 仮想マシンの作成](../virtual-network/create-vm-accelerated-networking-cli.md)」を参照してください。

## <a name="sdk-usage"></a>SDK の使用

**最新の SDK をインストールする**

Azure Cosmos DB SDK は、最適なパフォーマンスを提供するために頻繁に改善されています。 [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) のページを参照して、最新の SDK を確認し、改善点を確認してください。

**アプリケーションの有効期間中はシングルトン Azure Cosmos DB クライアントを使用する**

各 `DocumentClient` インスタンスはスレッドセーフであり、直接モードで動作しているときには効率的な接続管理とアドレスのキャッシュが実行されます。 効率的な接続管理と SDK クライアントのパフォーマンス向上を実現するために、アプリケーションの有効期間中は、`AppDomain` ごとに単一のインスタンスを使用することをお勧めします。

**ゲートウェイ モードを使用するときはホストあたりの System.Net MaxConnections を増やす**

ゲートウェイ モードを使用すると、Azure Cosmos DB の要求は HTTPS/REST を介して行われます。 それらは、ホスト名または IP アドレスごとの既定の接続数の上限に従います。 場合によっては、Azure Cosmos DB に対する複数の同時接続をクライアント ライブラリで使用できるよう、`MaxConnections` を高い値 (100 から 1,000) に増やす必要があります。 .NET SDK 1.8.0 以降では、[ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) の既定値は 50 です。 その値を変更するには、[Documents.Client.ConnectionPolicy.MaxConnectionLimit](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit) をより高い値に設定できます。

**パーティション分割コレクションに対する並列クエリを調整する**

SQL .NET SDK 1.9.0 以降では、並列クエリがサポートされています。この機能を使用すると、パーティション分割コレクションにクエリを並列的に実行できます。 詳細については、SDK の操作に関連した[コード サンプル](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs)を参照してください。 並列クエリは、シリアル クエリよりもクエリの待機時間とスループットを向上させるように設計されています。 並列クエリには、要件に合わせてチューニングできる 2 つのパラメーターがあります。 
- `MaxDegreeOfParallelism` では、同時にクエリを実行できるパーティションの最大数を制御します。 
- `MaxBufferedItemCount` では、プリフェッチされる結果の数を制御します。

***並列処理の次数を調整する***

並列クエリは、複数のパーティションに並列にクエリを実行することによって機能します。 ただし、個々のパーティションからのデータは、クエリごとに順番に取得されます。 そのため、[SDK V2](sql-api-sdk-dotnet.md) の `MaxDegreeOfParallelism` をパーティションの数に設定すると、その他のすべてのシステムの条件が変わらなければ、クエリのパフォーマンスを最善にできる可能性が最大になります。 パーティションの数がわからない場合は、並列処理の次数を高い数値に設定できます。 システムにより、並列処理の次数として最小値 (パーティションの数、ユーザー指定の入力) が選択されます。

並列クエリが最も有効に機能するのは、クエリに対するデータがすべてのパーティションに均等に分散している場合であることに注意する必要があります。 パーティション分割されたコレクションが、クエリによって返されるすべてまたは大部分のデータがわずかな数のパーティション (最悪の場合は 1 つのパーティション) に集中するように分割されている場合、それらのパーティションがクエリのパフォーマンスのボトルネックになります。

***MaxBufferedItemCount を調整する***
    
並列クエリは、結果の現在のバッチがクライアントによって処理されている間に結果をプリフェッチするように設計されています。 このプリフェッチは、クエリの全体的な遅延の削減に役立ちます。 `MaxBufferedItemCount` パラメーターは、プリフェッチされる結果の数を制限します。 `MaxBufferedItemCount` を、返される結果の予期される数 (またはそれ以上の数) に設定すると、クエリに対するプリフェッチの効果が最大になります。

プリフェッチは、並列処理の次数とは無関係に同じように動作し、すべてのパーティションからのデータに対して単一のバッファーが存在します。  

**RetryAfter 間隔でバックオフを実装する**

パフォーマンス テストでは、調整される要求の割合がわずかになるまで負荷を上げる必要があります。 要求がスロットル状態になった場合、クライアント アプリケーション側でバックオフ値を適用し、サーバー側によって指定された再試行間隔のスロットル時間を後退させるようにしてください。 バックオフにより、再試行までの待ち時間を最小限に抑えることができます。 

再試行ポリシーは、次の SDK でサポートされています。
- [.NET SDK for SQL](sql-api-sdk-dotnet.md) および [Java SDK for SQL](sql-api-sdk-java.md) のバージョン 1.8.0 以降
- [Node.js SDK for SQL](sql-api-sdk-node.md) および [Python SDK for SQL](sql-api-sdk-python.md) のバージョン 1.9.0 以降
- [.NET Core](sql-api-sdk-dotnet-core.md) SDK のサポートされているすべてのバージョン 

詳細については、[RetryAfter](/dotnet/api/microsoft.azure.documents.documentclientexception.retryafter) に関するページを参照してください。
    
.NET SDK のバージョン 1.19 以降では、次の例のように、追加の診断情報をログに記録し、待ち時間に関する問題をトラブルシューティングするメカニズムがあります。 読み取り待ち時間の長い要求についての診断文字列をログに記録できます。 取得した診断文字列は、特定の要求に対して 429 エラーを受け取った回数を把握するのに役立ちます。

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**ドキュメント URI をキャッシュして読み取り待機時間を減らす**

最適な読み取りパフォーマンスを実現するために、できる限りドキュメント URI をキャッシュします。 リソースを作成するときに、リソース ID をキャッシュするロジックを定義する必要があります。 リソース ID に基づく参照は名前ベースの参照よりも高速であるため、これらの値をキャッシュすると、パフォーマンスが向上します。

**パフォーマンスを向上させるために、クエリ/読み取りフィードのページ サイズを調整する**

読み取りフィード機能 (`ReadDocumentFeedAsync` など) を使ってドキュメントの一括読み取りを行うときや、SQL クエリを発行するときに、結果セットが大きすぎる場合、セグメント化された形式で結果が返されます。 既定では、100 項目または 1 MB (先に達した方) のチャンク単位で結果が返されます。

該当するすべての結果を取得するために必要なネットワーク ラウンド トリップの回数を減らすために、要求に対して [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) を使用することで、ページ サイズを最大 1,000 ヘッダーまで増やすことができます。 ごく少数の結果のみを表示する必要がある場合は (ユーザー インターフェイスやアプリケーション API が一度に 10 件しか結果を返さない場合など)、読み取りとクエリに使用されるスループットを減らすために、ページ サイズを 10 に減らすこともできます。

> [!NOTE] 
> `maxItemCount` プロパティは、改ページ位置の自動修正のみに使用しないでください。 主な用途は、1 ページに返される項目の最大数を減らすことで、クエリのパフォーマンスを向上させることです。  

また、使用可能な Azure Cosmos DB SDK を使用してページ サイズを設定することもできます。 `FeedOptions` の [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount) プロパティでは、列挙操作で返される項目の最大数を設定できます。 `maxItemCount` が-1 に設定されている場合、ドキュメント サイズに応じて最適な値が SDK によって自動的に見つけられます。 次に例を示します。
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
クエリが実行されると、結果のデータは TCP パケット内で送信されます。 `maxItemCount` に指定した値が小さすぎると、TCP パケット内でデータを送信するために必要なトリップ数が多くなり、パフォーマンスに影響します。 そのため、`maxItemCount` プロパティに設定する値がわからない場合は、-1 に設定して SDK で自動的に既定値を選択することをお勧めします。

**スレッドまたはタスクの数を増やす**

この記事の「ネットワーク」セクションの「[スレッドまたはタスクの数を増やす](#increase-threads)」を参照してください。

## <a name="indexing-policy"></a>インデックス作成ポリシー
 
**インデックス作成から未使用のパスを除外して書き込みを高速化する**

Azure Cosmos DB のインデックス作成ポリシーでは、パスのインデックス作成 (IndexingPolicy.IncludedPaths および IndexingPolicy.ExcludedPaths) を使用して、インデックス作成に含めたり除外したりするドキュメント パスを指定できます。 パスのインデックス作成により、クエリのパターンが事前にわかっている場合に、書き込みパフォーマンスの向上とインデックス ストレージの削減が可能になります。 これは、インデックス作成コストは、インデックスが作成される一意のパスの数に直接関係するためです。 たとえば、次のコードは、ワイルドカード "*" を使用して、ドキュメントのセクション全体 (サブツリー) をインデックス作成から除外する方法を示しています。

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

詳細については、[Azure Cosmos DB インデックス作成ポリシー](index-policy.md)に関するページをご覧ください。

## <a name="throughput"></a><a id="measure-rus"></a> スループット

**測定と調整によって 1 秒あたりの要求ユニットの使用量を削減する**

Azure Cosmos DB には、データベース操作の豊富なセットが用意されています。 UDF、ストアド プロシージャ、トリガーを使用したリレーショナル クエリや階層クエリなどの操作があります。これらの操作はすべて、データベース コレクション内のドキュメントに対して実行できます。 これらの操作のそれぞれに関連付けられたコストは、操作を完了するために必要な CPU、IO、およびメモリに応じて異なります。 ハードウェア リソースの管理について考える代わりに、各種のデータベース操作を実行しアプリケーション要求を処理するのに必要なリソースに関する単一の測定単位として要求ユニット (RU) を考えることができます。

コンテナーごとに設定された[要求ユニット](request-units.md)の数に基づいて、スループットをプロビジョニングします。 要求ユニットの消費は、1 秒あたりのレートとして評価されます。 コンテナーのプロビジョニング済み要求ユニット レートを超過したアプリケーションは、レートがそのコンテナーにプロビジョニングされているレベルを下回るまで制限されます。 アプリケーションでより高いスループットが必要になった場合は、追加の要求ユニットをプロビジョニングしてスループットを増やすことができます。

クエリの複雑さは、操作で消費される要求ユニット数に影響します。 述語の数、述語の特性、UDF 数、ソース データ セットのサイズのすべてがクエリ操作のコストに影響します。

操作 (作成、更新、または削除) のオーバーヘッドを測定するには、[x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) ヘッダー (あるいは、.NET SDK の `ResourceResponse\<T>` または `FeedResponse\<T>` の同等の `RequestCharge` プロパティ) を調べて、これらの操作で使用される要求ユニット数を測定します。

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

このヘッダーで返される要求の使用量は、プロビジョニングしたスループット (2000 RU/秒) の一部です。 たとえば、上記のクエリが 1 KB のドキュメントを 1000 個返した場合、この操作のコストは 1000 になります。 そのため、後続の要求をレート制限する前に、サーバーは 1 秒以内にこのような要求を 2 つだけ受け付けます。 詳細については、[要求ユニット](request-units.md)に関する記事および[要求ユニット計算ツール](https://www.documentdb.com/capacityplanner)のページを参照してください。
<a id="429"></a>

**レート制限と大きすぎる要求レートに対処する**

クライアントがアカウントの予約済みスループットを超えようとしても、サーバーでパフォーマンスの低下が発生することはなく、予約済みのレベルを超えてスループット容量が使用されることもありません。 サーバーは、RequestRateTooLarge (HTTP 状態コード 429) を使用して要求をプリエンプティブに終了します。 それからは、要求を再試行する前にユーザーが待機する必要がある時間 (ミリ秒単位) を示す、[x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) ヘッダーが返されます。

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

SDK はすべてこの応答を暗黙的にキャッチし、サーバーが指定した retry-after ヘッダーを優先して要求を再試行します。 アカウントに複数のクライアントが同時アクセスしている状況でなければ、次回の再試行は成功します。

累積的に動作する複数のクライアントがあり、要求レートを常に超えている場合は、現在クライアントによって内部的に 9 に設定される既定の再試行回数では、十分ではない可能性があります。 このような場合、クライアントではアプリケーションに対して状態コード 429 の DocumentClientException がスローされます。 

`ConnectionPolicy` インスタンスで `RetryOptions` を設定することにより、既定の再試行回数を変更できます。 既定では、要求レートを超えて要求が続行されている場合に、30 秒の累積待機時間を過ぎると、状態コード 429 を含む DocumentClientException が返されます。 このエラーは、現在の値が既定値の 9 かユーザー定義の値かにかかわらず、現在の再試行回数が最大再試行回数より少ない場合でも返されます。

自動再試行動作は、ほとんどのアプリケーションで回復性と使いやすさを向上させるのに役立ちます。 ただし、パフォーマンス ベンチマークを実行しているときは (特に待機時間を測定するとき)、最適な動作ではない可能性があります。 実験でサーバー スロットルが発生し、クライアント SDK によって警告なしに再試行が行われると、クライアントが監視する待機時間が急増します。 パフォーマンスの実験中に待機時間が急増するのを回避するには、各操作で返される使用量を測定し、予約済みの要求レートを下回った状態で要求が行われていることを確認します。 詳細については、[要求ユニット](request-units.md)に関する記事を参照してください。

**スループットを向上させるためにサイズの小さいドキュメントに合わせて設計する**

特定の操作の要求の使用量 (要求処理コスト) は、ドキュメントのサイズに直接関係します。 サイズの大きいドキュメントの操作は、サイズの小さいドキュメントの操作よりもコストがかかります。

## <a name="next-steps"></a>次のステップ

少数のクライアント コンピューターでの高パフォーマンス シナリオで Azure Cosmos DB の評価に使用されるサンプル アプリケーションについては、「[Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)」を参照してください。

スケーリングと高パフォーマンスのためのアプリケーションの設計について詳しくは、「[Azure Cosmos DB でのパーティション分割とスケーリング](partitioning-overview.md)」をご覧ください。