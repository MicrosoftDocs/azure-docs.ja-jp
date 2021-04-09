---
title: Bulk Executor .NET ライブラリを使用して Azure Cosmos DB で一括インポートおよび更新操作を実行する
description: Bulk Executor .NET ライブラリを使用して Azure Cosmos DB ドキュメントを一括インポートおよび更新します。
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 34aef5bd880e3ef080676fb9e90e62796d499e7b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102429818"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Bulk Executor .NET ライブラリを使用して Azure Cosmos DB で一括操作を実行する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> この記事で説明されているこのバルク エグゼキューター ライブラリは、.NET SDK 2.x バージョンを使用するアプリケーションに対して維持されます。 新しいアプリケーションの場合は、[.NET SDK バージョン 3.x](tutorial-sql-api-dotnet-bulk-import.md) で直接使用できる **バルク サポート** を使用できるため、外部ライブラリを必要としません。 

> 現在、バルク エグゼキューター ライブラリを使用していて、新しい SDK でのバルク サポートへの移行を計画している場合は、[移行ガイド](how-to-migrate-from-bulk-executor-library.md)の手順を使用して、アプリケーションを移行してください。

このチュートリアルでは、Bulk Executor .NET ライブラリを使用して、ドキュメントを Azure Cosmos コンテナーにインポートし、更新する方法について説明します。 Bulk Executor ライブラリについてと、それを大規模なスループットおよびストレージの活用に役立てる方法については、[Bulk Executor ライブラリの概要](bulk-executor-overview.md)に関する記事を参照してください。 このチュートリアルでは、ランダムに生成されたドキュメントを Azure Cosmos コンテナーに一括インポートするサンプル .NET アプリケーションを示します。 インポート後、特定のドキュメント フィールドに対して実行する操作としてパッチを指定することによって、インポートされたデータを一括更新する方法について説明します。

現在、Bulk Executor ライブラリは、Azure Cosmos DB SQL API および Gremlin API アカウントによってのみサポートされています。 この記事では、SQL API アカウントで Bulk Executor .NET ライブラリを使用する方法について説明します。 Gremlin API アカウントでの Bulk Executor .NET ライブラリの使用の詳細については、[Azure Cosmos DB Gremlin API での一括操作の実行](bulk-executor-graph-dotnet.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* まだ Visual Studio 2019 をインストールしていない場合は、[Visual Studio 2019 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず "Azure の開発" を有効にしてください。

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

* Azure サブスクリプションを必要とせず、課金や契約もなしに [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)ことができます。 または、`https://localhost:8081` エンドポイントで [Azure Cosmos DB Emulator](./local-emulator.md) を使用できます。 主キーは、[要求の認証](local-emulator.md#authenticate-requests)で与えられます。

* .NET クイック スタート記事の「[データベース アカウントの作成](create-sql-api-dotnet.md#create-account)」セクションで説明されている手順に従って、Azure Cosmos DB SQL API アカウントを作成します。

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

それでは、GitHub からサンプル .NET アプリケーションをダウンロードして、コードの作業を行います。 このアプリケーションでは、Azure Cosmos アカウントに格納されているデータに対して一括操作を実行します。 アプリケーションをクローンするために、コマンド プロンプトを開き、コピー先のディレクトリに移動し、次のコマンドを実行します。

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

クローンされたリポジトリには、2 つのサンプル "BulkImportSample" と "BulkUpdateSample" が含まれています。 どちらかのサンプル アプリケーションを開き、App.config ファイルの接続文字列を Azure Cosmos DB アカウントの接続文字列に更新し、ソリューションをビルドして実行します。

"BulkImportSample" アプリケーションでは、ランダムなドキュメントを生成し、Azure Cosmos アカウントに一括インポートします。 "BulkUpdateSample" アプリケーションでは、特定のドキュメント フィールドに対して実行する操作としてパッチを指定することによって、インポートされたドキュメントを一括更新します。 次のセクションでは、これらのサンプル アプリケーションのコードを確認します。

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Azure Cosmos アカウントへのデータの一括インポート

1. "BulkImportSample" フォルダーに移動し、"BulkImportSample.sln" ファイルを開きます。  

2. 次のコードに示すように、Azure Cosmos DB の接続文字列は App.config ファイルから取得されます。  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   一括インポーターでは、App.config ファイルで指定されているデータベース名、コンテナー名、およびスループット値を使用して、新しいデータベースとコンテナーが作成されます。

3. 次に、DocumentClient オブジェクトが Direct TCP 接続モードで初期化されます。  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. BulkExecutor オブジェクトが初期化され、待機時間とスロットルされた要求の再試行回数が大きい値に設定されます。 その後、有効期間にわたって BulkExecutor に輻輳制御を渡すために、0 に設定されます。  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. このアプリケーションでは、BulkImportAsync API を呼び出します。 .NET ライブラリは、一括インポート API の 2 つのオーバーロードを提供します。1 つはシリアル化された JSON ドキュメントの一覧を受け取り、もう 1 つは逆シリアル化された POCO ドキュメントの一覧を受け取ります。 これらのオーバーロードされた各メソッドの定義について詳しくは、[API ドキュメント](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync)を参照してください。

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync メソッドは、次のパラメーターを受け取ります。**
   
   |**パラメーター**  |**説明** |
   |---------|---------|
   |enableUpsert    |   ドキュメントに対するアップサート操作を有効にするフラグ。 指定された ID のドキュメントが既に存在する場合は、更新されます。 既定では false に設定されています。      |
   |disableAutomaticIdGeneration    |    ID の自動生成を無効にするフラグ。 既定値は true です。     |
   |maxConcurrencyPerPartitionKeyRange    | パーティション キー範囲あたりのコンカレンシーの最大数。null に設定すると、ライブラリは既定値の 20 を使用します。 |
   |maxInMemorySortingBatchSize     |  各ステージで API 呼び出しに渡されたドキュメント列挙子から取得するドキュメントの最大数。 一括インポート前に行われるインメモリの並べ替えフェーズについては、このパラメーターを null に設定すると、ライブラリで既定の最小値 (documents.count, 1000000) が使用されます。       |
   |cancellationToken    |    一括インポート操作を正常に終了させるためのキャンセル トークン。     |

   **一括インポート応答オブジェクト定義** 一括インポート API 呼び出しの結果には、次の属性が含まれています。

   |**パラメーター**  |**説明**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  一括インポート API 呼び出しに渡された全ドキュメントのうち正常にインポートされたドキュメントの総数。       |
   |TotalRequestUnitsConsumed (double)   |   一括インポート API 呼び出しによって使用された要求ユニット (RU) の合計数。      |
   |TotalTimeTaken (TimeSpan)    |   一括インポート API 呼び出しが実行を完了するまでに要した合計時間。      |
   |BadInputDocuments (List\<object>)   |     一括インポート API 呼び出しで正常にインポートされなかった、形式が正しくないドキュメントの一覧。 返されたドキュメントを修正し、インポートを再試行してください。 形式が正しくないドキュメントには、ID 値が文字列ではないドキュメントがあります (null やその他のデータ型は無効と見なされます)。    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Azure Cosmos アカウントでのデータの一括更新

BulkUpdateAsync API を使用すると、既存のドキュメントを更新できます。 この例では、`Name` フィールドを新しい値に設定し、既存のドキュメントから `Description` フィールドを削除します。 サポートされているすべての更新操作については、[API ドキュメント](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate)を参照してください。

1. "BulkUpdateSample" フォルダーに移動し、"BulkUpdateSample.sln" ファイルを開きます。  

2. 更新項目と、対応するフィールド更新操作を定義します。 この例では、`SetUpdateOperation` を使用して `Name` フィールドを更新し、`UnsetUpdateOperation` を使用してすべてのドキュメントから `Description` フィールドを削除します。 ドキュメント フィールドの値に特定の値を加算したり、特定の値を配列フィールドにプッシュしたり、配列フィールドから特定の値を削除したりするなど、他の操作を実行することもできます。 一括更新 API が提供するさまざまなメソッドについては、[API ドキュメント](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate)を参照してください。

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. このアプリケーションでは、BulkUpdateAsync API を呼び出します。 BulkUpdateAsync メソッドの定義については、[API ドキュメント](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync)を参照してください。  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync メソッドは次のパラメーターを受け取ります。**

   |**パラメーター**  |**説明** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   パーティション キー範囲あたりのコンカレンシーの最大数。このパラメーターを null に設定すると、ライブラリでは既定値 (20) が使用されます。   |
   |maxInMemorySortingBatchSize    |    各ステージで API 呼び出しに渡された更新項目列挙子から取得する更新項目の最大数。 一括更新前に行われるインメモリの並べ替えフェーズについては、このパラメーターを null に設定すると、ライブラリで既定の最小値 (updateItems.count, 1000000) が使用されます。     |
   | cancellationToken|一括更新操作を正常に終了させるためのキャンセル トークン。 |

   **一括更新応答オブジェクト定義** 一括更新 API 呼び出しの結果には、次の属性が含まれています。

   |**パラメーター**  |**説明** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   一括更新 API 呼び出しに渡された全ドキュメントのうち正常に更新されたドキュメントの数。      |
   |TotalRequestUnitsConsumed (double)   |    一括更新 API 呼び出しによって使用された要求ユニット (RU) の合計数。    |
   |TotalTimeTaken (TimeSpan)   | 一括更新 API 呼び出しが実行を完了するまでに要した合計時間。 |
    
## <a name="performance-tips"></a>パフォーマンスに関するヒント 

Bulk Executor ライブラリを使用する場合は、パフォーマンスを向上させるために、次の点を考慮してください。

* パフォーマンスを最大限に高めるには、Azure Cosmos アカウントの書き込みリージョンと同じリージョンにある Azure 仮想マシンからアプリケーションを実行します。  

* 特定の Azure Cosmos コンテナーに対応する単一の仮想マシン内でアプリケーション全体に対して 1 つの `BulkExecutor` オブジェクトをインスタンス化することをお勧めします。  

* 1 つの一括操作 API 実行でクライアント マシンの CPU とネットワーク IO が大量に消費されます (これは、内部的に複数のタスクを生成することによって発生します)。 一括操作 API 呼び出しを実行するアプリケーション プロセス内で、複数の同時実行タスクを実行しないようにしてください。 単一の仮想マシンで実行される 1 つの一括操作 API 呼び出しでコンテナー全体のスループットを消費できない場合 (コンテナーのスループットが 100 万 RU/秒を超える場合)、別個の仮想マシンを作成して、一括操作 API 呼び出しを同時に実行することをお勧めします。  

* BulkExecutor オブジェクトをインスタンス化して、ターゲット Cosmos コンテナーのパーティション マップを取得した後、`InitializeAsync()` メソッドが確実に呼び出されるようにします。  

* パフォーマンス向上のために、アプリケーションの App.Config で **gcServer** を有効にします。
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* このライブラリが出力するトレースは、ログ ファイルまたはコンソールで収集できます。 両方を有効にするには、アプリケーションの App.Config ファイルに以下のコードを追加します。

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>次のステップ

* NuGet パッケージの詳細とリリース ノートについては、[Bulk Executor SDK の詳細](sql-api-sdk-bulk-executor-dot-net.md)に関するページを参照してください。
