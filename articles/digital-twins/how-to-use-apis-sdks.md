---
title: Azure Digital Twins の API および SDK を使用する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins の API の操作方法 (SDK 経由を含む) を参照してください。
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 39dd9604cf0e58eda94acf6528ab31eca26355d0
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936777"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Azure Digital Twins の API および SDK を使用する

Azure Digital Twins には、インスタンスとその要素を管理するための**コントロール プレーン API** と**データ プレーン API** の両方が用意されています。 この記事では、使用可能な API の概要と、API を操作するためのメソッドについて説明します。 REST API は、関連付けられている Swagger を使って直接使用することも、SDK を通して使用することもできます。

## <a name="overview-control-plane-apis"></a>概要: コントロール プレーン API

コントロール プレーン API は、Azure Digital Twins のインスタンス全体を管理するために使用されます。このため、インスタンス全体の作成や削除などの操作を行うことができます。 また、エンドポイントの作成と削除にも使用します。

パブリック プレビューの最新のコントロール プレーン API バージョンは、**2020-03-01-preview** です。__

コントロール プレーン API を使用するには
* 最新の [Swagger フォルダー](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins)を参照することで、API を直接呼び出すことができます。 このリポジトリには、使用法を示す例が保存されているフォルダーもあります。
* 現在、コントロール API の SDK には、次の言語でアクセスできます。
  - [.NET (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/1.0.0-preview.1) ([ソース](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)) ([参照 [自動生成]](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet-preview))
  - [Java](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_03_01_preview/azure-mgmt-digitaltwins) ([ソース](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)) ([参照 [自動生成]](https://docs.microsoft.com/java/api/overview/azure/digitaltwins/management?view=azure-java-preview))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([ソース](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([ソース](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go - ソース](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/digitaltwins/mgmt/2020-03-01-preview/digitaltwins)

また、コントロール プレーン API の演習を行うには、[Azure portal](https://portal.azure.com) および [CLI](how-to-use-cli.md) を使用して Azure Digital Twins を操作します。

## <a name="overview-data-plane-apis"></a>概要: データ プレーン API

データ プレーン API は、Azure Digital Twins のインスタンス内の要素を管理するために使用されます。 この中には、ルートの作成、モデルのアップロード、リレーションシップの作成、Twins の管理などの操作が含まれます。 これらは、主に次のカテゴリに分けることができます。
* **DigitalTwinsModels** - DigitalTwinsModels カテゴリには、Azure Digital Twins のインスタンスの[モデル](concepts-models.md)を管理するための API が含まれています。 管理アクティビティには、DTDL で作成されたモデルのアップロード、検証、取得、および削除が含まれます。
* **DigitalTwins** - DigitalTwins カテゴリには、開発者が Azure Digital Twins のインスタンスで [Digital Twins](concepts-twins-graph.md) とその関係を作成、変更、および削除するための API が含まれています。
* **Query** - 開発者は、Query カテゴリを使用して、複数のリレーションシップにわたる[ツイングラフで一連の Digital Twins を検索できます](how-to-query-graph.md)。
* **EventRoutes** - EventRoutes カテゴリには、システムを通してダウンストリーミング サービスに[データをルーティングする](concepts-route-events.md) API が含まれています。

パブリック プレビューの最新のデータ プレーン API バージョンは、**2020-05-31-preview** です。__ データ プレーン操作に対する _2020-03-01-preview_ API バージョンは非推奨になりました。

データ プレーン API を使用するには
* API を直接呼び出すには、次の操作を実行します。
   - 最新の [Swagger フォルダー](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)を参照する。 このリポジトリには、使用法を示す例が保存されているフォルダーもあります。 
   - [API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/azure-digitaltwins/)を参照する。
* .NET (C#) SDK を使用できます。 現在、これらの API を操作するために公開されている SDK は、これだけです。 .NET SDK は次の方法で使用します。
   - NuGet でパッケージ [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core) を表示する。 
   - GitHub で、サンプルのフォルダーを含む SDK ソース [Azure IoT Digital Twins client library for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) を探す。 
   - [SDK のリファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins?view=azure-dotnet-preview)を確認する。
   - 詳細な情報と使用例については、この記事の「[.NET (C#) SDK (データ プレーン)](#net-c-sdk-data-plane)」セクションを参照してください。
* AutoRest を使用して、別の言語用の SDK を生成することができます。 ["*AutoRest を使用して Azure Digital Twins 用のカスタム SDK を作成する方法*"](how-to-create-custom-sdks.md) の手順に従ってください。

また、データ プレーン API の演習を行うには、[CLI](how-to-use-cli.md) を使用して Azure Digital Twins を操作します。

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (データ プレーン)

Azure Digital Twins .NET (C#) SDK は、Azure SDK for .Net に含まれています。 これはオープン ソースであり、Azure Digital Twins データ プレーン API に基づいています。

> [!NOTE]
> SDK の設計の詳細については、[Azure SDK の一般的な設計原則](https://azure.github.io/azure-sdk/general_introduction.html)と特定の [.NET 設計ガイドライン](https://azure.github.io/azure-sdk/dotnet_introduction.html)を参照してください。

SDK を使用するには、NuGet パッケージ **Azure.DigitalTwins.Core** をプロジェクトに含めます。 また、**Azure.Identity** パッケージの最新バージョンも必要です。

* Visual Studio では、NuGet パッケージ マネージャーを使用してパッケージを追加できます。そのためには、[ツール] > [NuGet パッケージ マネージャー] > [ソリューションの NuGet パッケージの管理] の順に選択します。** 
* .NET コマンド ライン ツールを使用して、次のコマンドを実行できます。

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

実際の API の使用方法の詳細については、["*クライアント アプリのコーディングに関するチュートリアル*"](tutorial-code.md) を参照してください。 

### <a name="net-sdk-usage-examples"></a>.NET SDK の使用例

ここでは、.NET SDK の使用方法を示すコード サンプルをいくつか紹介します。

サービスに対する認証を実行する:

```csharp
// Authenticate against the service and create a client
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
```

モデルをアップロードし、モデルを一覧表示する:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Twins を作成して照会する:

```csharp
// Initialize twin metadata
BasicDigitalTwin twinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", "Hello World!");
try {
    await client.CreateDigitalTwinAsync("firstTwin", JsonSerializer.Serialize(twinData));
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

このサンプル コード アプリの詳細については、["*クライアント アプリのコーディングに関するチュートリアル*"](tutorial-code.md) を参照してください。 

その他のサンプルについては、[.NET (C#) SDK 用 GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)を参照してください。

#### <a name="serialization-helpers"></a>シリアル化のヘルパー

シリアル化のヘルパーは、基本情報にアクセスするためのツイン データを、迅速に作成または逆シリアル化するために SDK 内で使用できるヘルパー関数です。 コア SDK メソッドは、既定でツイン データを JSON として返すため、これらのヘルパー クラスを使用して、ツイン データをさらに分割するのに役立ちます。

使用できるヘルパー クラスは次のとおりです。
* `BasicDigitalTwin`:デジタル ツインのコアデータを表します。
* `BasicRelationship`:リレーションシップのコア データを表します。
* `UpdateOperationUtility`:Update 呼び出しで使用される JSON パッチ情報を表します。
* `WriteableProperty`:プロパティのメタデータを表します。

##### <a name="deserialize-a-digital-twin"></a>デジタル ツインを逆シリアル化する

`System.Test.Json` や `Newtonsoft.Json` といった任意の JSON ライブラリを使用して、ツイン データをいつでも逆シリアル化できます。 ツインにアクセスする際には、ヘルパー クラスを使用すると便利です。

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

また、`BasicDigitalTwin` ヘルパー クラスを使用すると、ツインで定義されたプロパティに `Dictionary<string, object>` を介してアクセスできます。 ツインのプロパティを一覧表示するには、次のコードを使用します。

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>デジタル ツインを作成する

`BasicDigitalTwin` クラスを使用すると、ツイン インスタンスを作成するためのデータを準備できます。

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

前述のコードは、次の "手動" バリアントと同じです。

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

##### <a name="deserialize-a-relationship"></a>リレーションシップを逆シリアル化する

`System.Test.Json` や `Newtonsoft.Json` といった任意の JSON ライブラリを使用して、リレーションシップ データをいつでも逆シリアル化できます。 リレーションシップにアクセスする際には、ヘルパー クラスを使用すると便利です。

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

また、`BasicRelationship` ヘルパー クラスを使用すると、リレーションシップで定義されたプロパティに `Dictionary<string, object>` を介してアクセスできます。 プロパティを一覧表示するには、次のコードを使用します。

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>リレーションシップを作成する

`BasicRelationship` クラスを使用すると、ツイン インスタンスでリレーションシップを作成するためのデータを準備できます。

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.CustomProperties = props;
client.CreateRelationship("mySourceTwin", "rel001", JsonSerializer.Serialize<BasicRelationship>(rel));
```

##### <a name="create-a-patch-for-twin-update"></a>ツインを更新するための修正プログラムを作成する

ツインとリレーションシップの Update 呼び出しでは、[JSON Patch](http://jsonpatch.com/) 構造を使用します。 JSON Patch 操作のリストを作成するには、次に示すように `UpdateOperationsUtility` クラスを使用します。

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
uou.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
uou.AppendRemoveOp("/Humidity");
client.UpdateDigitalTwin("myTwin", uou.Serialize());
```

## <a name="general-apisdk-usage-notes"></a>一般的な API/SDK の使用上の注意

> [!NOTE]
> プレビュー期間中、Azure Digital Twins では**クロスオリジン リソース共有 (CORS)** がサポートされないことに注意してください。 このため、ブラウザー アプリ、[API Management (APIM)](../api-management/api-management-key-concepts.md) インターフェイス、または [Power Apps](https://docs.microsoft.com/powerapps/powerapps-overview) コネクタから REST API を呼び出すと、ポリシー エラーが表示される場合があります。
> このエラーを解決するには、次のいずれかの操作を行います。
> * メッセージから CORS ヘッダー `Access-Control-Allow-Origin` を削除します。 このヘッダーは、応答を共有できるかどうかを示します。 
> * または、CORS プロキシを作成し、それを通じて Azure Digital Twins REST API 要求を行います。 

次のリストに、API と SDK の使用に関する追加詳細情報および一般的なガイドラインを示します。

* SDK を使用するには、`DigitalTwinsClient` クラスをインスタンス化します。 コンストラクターには、`Azure.Identity` パッケージ内のさまざまな認証方法で取得できる資格情報が必要です。 `Azure.Identity` の詳細については、[名前空間に関するドキュメント](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet)を参照してください。 
* 作業を開始するときには `InteractiveBrowserCredential` が役立ちますが、[マネージド ID](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) の資格情報など、他にもいくつかのオプションがあります。これは、Azure Digital Twins に対して、[MSI を使用した Azure Functions のセットアップ](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)を認証する際に使用できます。 `InteractiveBrowserCredential` の詳細については、[クラスのドキュメント](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)を参照してください。
* すべてのサービス API 呼び出しは、`DigitalTwinsClient` クラスのメンバー関数として公開されます。
* すべてのサービス関数には、同期バージョンと非同期バージョンが存在します。
* すべてのサービス関数は、400 以上のリターン状態に対して例外をスローします。 `try` セクションに呼び出しをラップし、少なくとも `RequestFailedExceptions` をキャッチします。 この種類の例外の詳細については、[こちら](https://docs.microsoft.com/dotnet/api/azure.requestfailedexception?view=azure-dotnet)を参照してください。
* ほとんどのサービス メソッドは `Response<T>` (または非同期呼び出しの場合は `Task<Response<T>>`) を返します。 `T` は、サービス呼び出しで返されるオブジェクトのクラスです。 [`Response`](https://docs.microsoft.com/dotnet/api/azure.response-1?view=azure-dotnet) クラスは、サービスの戻り値をカプセル化し、その `Value` フィールドに戻り値を表示します。  
* ページングされた結果を含むサービス メソッドは、結果として `Pageable<T>` または `AsyncPageable<T>` を返します。 `Pageable<T>` クラスの詳細については、[こちら](https://docs.microsoft.com/dotnet/api/azure.pageable-1?view=azure-dotnet-preview)を参照してください。`AsyncPageable<T>` の詳細については、[こちら](https://docs.microsoft.com/dotnet/api/azure.asyncpageable-1?view=azure-dotnet-preview)を参照してください。
* `await foreach` ループを使用して、ページングした結果を反復処理できます。 この処理の詳細については、[こちら](https://docs.microsoft.com/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)を参照してください。
* 基になる SDK は `Azure.Core` です。 SDK のインフラストラクチャと種類については、[Azure 名前空間のドキュメント](https://docs.microsoft.com/dotnet/api/azure?view=azure-dotnet-preview)を参照してください。

サービス メソッドは、可能な限り、厳密に型指定されたオブジェクトを返します。 ただし、Azure Digital Twins は、実行時にユーザーによって構成されたカスタム モデル (サービスにアップロードされた DTDL モデルを使用) に基づいているため、多くのサービス API はツイン データを JSON 形式で取得して返します。

## <a name="monitor-api-metrics"></a>API メトリックの監視

要求、待機時間、失敗率などの API メトリックは、[Azure portal](https://portal.azure.com/) で見ることができます。 

portal のホームページで、Azure Digital Twins インスタンスを検索して詳細を取得します。 Azure Digital Twins インスタンスのメニューから **[メトリック]** オプションを選択して、 *[メトリック]* ページを表示します。

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins の [メトリック] ページが示されているスクリーンショット":::

ここから、インスタンスのメトリックを表示したり、カスタム ビューを作成したりできます。

## <a name="next-steps"></a>次のステップ

API を使用して Azure Digital Twins インスタンスと認証を設定する方法を参照してください。
* [*方法: インスタンスと認証を設定する*"](how-to-set-up-instance-scripted.md)

または、次のチュートリアルで使用されているようなクライアント アプリの作成手順を実行します。
* [*チュートリアル:クライアント アプリをコーディングする*](tutorial-code.md)
