---
title: Azure Digital Twins の API および SDK を使用する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins の API の操作方法 (SDK 経由を含む) を参照してください。
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f4f3fc8c928cd284088cc51120f1a7b485b4fac0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595347"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Azure Digital Twins の API および SDK を使用する

Azure Digital Twins には、インスタンスとその要素を管理するための **コントロール プレーン API** と **データ プレーン API** の両方が用意されています。 
* コントロール プレーン API は、[Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) API であり、インスタンスの作成や削除などのリソース管理操作に対応しています。 
* データ プレーン API は、Azure Digital Twins API であり、モデル、ツイン、グラフの管理などのデータ管理操作に使用されます。

この記事では、使用可能な API の概要と、API を操作するためのメソッドについて説明します。 REST API は、関連付けられている Swagger を使って直接使用することも ([Postman](how-to-use-postman.md) などのツールを使用)、SDK を通して使用することもできます。

## <a name="overview-control-plane-apis"></a>概要: コントロール プレーン API

コントロール プレーン API は、[ARM](../azure-resource-manager/management/overview.md) API であり、Azure Digital Twins インスタンス全体を管理するために使用されます。そのため、インスタンス全体の作成や削除などの操作に対応しています。 また、エンドポイントの作成と削除にも使用します。

最新のコントロール プレーン API のバージョンは、_**2020-12-01**_ です。

コントロール プレーン API を使用するには
* [コントロール プレーン Swagger リポジトリ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable)の最新の Swagger フォルダーを参照することで、API を直接呼び出すことができます。 このフォルダーには、使用法を示す例が保存されているフォルダーも含まれています。
* 現在、コントロール API の SDK には、次の言語でアクセスできます。
  - [ **.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([参照 [自動生成]](/dotnet/api/overview/azure/digitaltwins/management)) ([ソース](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([参照 [自動生成]](/java/api/overview/azure/digitaltwins)) ([ソース](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([ソース](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([ソース](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([ソース](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

また、コントロール プレーン API の演習を行うには、[Azure portal](https://portal.azure.com) および [CLI](how-to-use-cli.md) を使用して Azure Digital Twins を操作します。

## <a name="overview-data-plane-apis"></a>概要: データ プレーン API

データ プレーン API は、Azure Digital Twins API であり、Azure Digital Twins インスタンス内の要素を管理するために使用されます。 これには、ルートの作成、モデルのアップロード、リレーションシップの作成、ツインの管理などの操作が含まれます。 これらは、主に次のカテゴリに分けることができます。
* **DigitalTwinModels** - DigitalTwinModels カテゴリには、Azure Digital Twins のインスタンスの [モデル](concepts-models.md)を管理するための API が含まれています。 管理アクティビティには、DTDL で作成されたモデルのアップロード、検証、取得、および削除が含まれます。
* **DigitalTwins** - DigitalTwins カテゴリには、開発者が Azure Digital Twins のインスタンスで [Digital Twins](concepts-twins-graph.md) とその関係を作成、変更、および削除するための API が含まれています。
* **Query** - 開発者は、Query カテゴリを使用して、複数のリレーションシップにわたる [ツイングラフで一連の Digital Twins を検索できます](how-to-query-graph.md)。
* **Event Routes** - Event Routes カテゴリには、システムを通してダウンストリーミング サービスに [データをルーティングする](concepts-route-events.md) API が含まれています。

最新のデータ プレーン API のバージョンは、_**2020-10-31**_ です。

データ プレーン API を使用するには
* API を直接呼び出すには、次の操作を実行します。
   - [データ プレーン Swagger リポジトリ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)の最新の Swagger フォルダーを参照する。 このフォルダーには、使用法を示す例が保存されているフォルダーも含まれています。 
   - [API リファレンス ドキュメント](/rest/api/azure-digitaltwins/)を参照する。
* **.NET (C#)** SDK を使用できます。 .NET SDK は次の方法で使用します。
   - NuGet からパッケージを表示して追加する。[Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core) を表示する。 
   - [SDK のリファレンス ドキュメント](/dotnet/api/overview/azure/digitaltwins/client)を確認する。
   - GitHub で、サンプルのフォルダーを含む SDK ソース [Azure IoT Digital Twins client library for .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) を探す。 
   - 詳細な情報と使用例については、この記事の「[ *.NET (C#) SDK (データ プレーン)*](#net-c-sdk-data-plane)」セクションを参照してください。
* **Java** SDK を使用できます。 Java SDK を使用するには、次のようにします。
   - Maven からパッケージを表示してインストールする。[`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - [SDK のリファレンス ドキュメント](/java/api/overview/azure/digitaltwins/client)を確認する
   - GitHub で SDK のソースを探す。[Azure IoT Digital Twins client library for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* **JavaScript** SDK を使用できます。 JavaScript SDK を使用するには、次のようにします。
   - npm からパッケージを表示してインストールする。[JavaScript 用 Azure Digital Twins コア クライアント ライブラリ](https://www.npmjs.com/package/@azure/digital-twins-core)。
   - [SDK のリファレンス ドキュメント](/javascript/api/@azure/digital-twins-core/)を確認する。
   - GitHub で SDK のソースを探す。[JavaScript 用 Azure Digital Twins コア クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* **Python** SDK を使用できます。 Python SDK を使用するには、次のようにします。
   - PyPi からパッケージを表示してインストールする。[Azure Azure Digital Twins Core client library for Python](https://pypi.org/project/azure-digitaltwins-core/)。
   - [SDK のリファレンス ドキュメント](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)を確認する。
   - GitHub で SDK のソースを探す。[Azure Digital Twins Core client library for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* AutoRest を使用して、別の言語用の SDK を生成することができます。 ["*AutoRest を使用して Azure Digital Twins 用のカスタム SDK を作成する方法*"](how-to-create-custom-sdks.md) の手順に従ってください。

また、データ プレーン API の演習を行うには、[CLI](how-to-use-cli.md) を使用して Azure Digital Twins を操作します。

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (データ プレーン)

Azure Digital Twins .NET (C#) SDK は、Azure SDK for .Net に含まれています。 これはオープン ソースであり、Azure Digital Twins データ プレーン API に基づいています。

> [!NOTE]
> SDK の設計の詳細については、[Azure SDK の一般的な設計原則](https://azure.github.io/azure-sdk/general_introduction.html)と特定の [.NET 設計ガイドライン](https://azure.github.io/azure-sdk/dotnet_introduction.html)を参照してください。

SDK を使用するには、NuGet パッケージ **Azure.DigitalTwins.Core** をプロジェクトに含めます。 また、**Azure.Identity** パッケージの最新バージョンも必要です。 Visual Studio では、NuGet パッケージ マネージャーを使用してこれらのパッケージを追加できます。そのためには、 *[ツール]、[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]* の順に選択します。 または、.NET コマンド ライン ツールと次の NuGet パッケージ リンクにあるコマンドを使用し、プロジェクトにこれらを追加できます。
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core)。 これは、[.NET 用 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client) のパッケージです。 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity)。 このライブラリでは、Azure に対する認証を支援するツールが提供されます。

実際の API の使用方法の詳細については、["*クライアント アプリのコーディングに関するチュートリアル*"](tutorial-code.md) を参照してください。 

### <a name="net-sdk-usage-examples"></a>.NET SDK の使用例

ここでは、.NET SDK の使用方法を示すコード サンプルをいくつか紹介します。

サービスに対する認証を実行する:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

モデルをアップロードする:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

モデルを一覧表示する:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

ツインを作成する:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

ツインにクエリを実行し、結果をループ処理する:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

このサンプル コード アプリの詳細については、["*クライアント アプリのコーディングに関するチュートリアル*"](tutorial-code.md) を参照してください。 

その他のサンプルについては、[.NET (C#) SDK 用 GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)を参照してください。

#### <a name="serialization-helpers"></a>シリアル化のヘルパー

シリアル化のヘルパーは、基本情報にアクセスするためのツイン データを、迅速に作成または逆シリアル化するために SDK 内で使用できるヘルパー関数です。 コア SDK メソッドは、既定でツイン データを JSON として返すため、これらのヘルパー クラスを使用して、ツイン データをさらに分割するのに役立ちます。

使用できるヘルパー クラスは次のとおりです。
* `BasicDigitalTwin`: 一般的にデジタル ツインのコア データを表します
* `BasicDigitalTwinComponent`: 一般的に `BasicDigitalTwin` の `Contents` プロパティ内のコンポーネントを表します
* `BasicRelationship`: 一般的にリレーションシップのコア データを表します
* `DigitalTwinsJsonPropertyName`: カスタムのデジタル ツイン型での JSON のシリアル化と逆シリアル化で使用される文字列定数が格納されています

##### <a name="deserialize-a-digital-twin"></a>デジタル ツインを逆シリアル化する

`System.Text.Json` や `Newtonsoft.Json` といった任意の JSON ライブラリを使用して、ツイン データをいつでも逆シリアル化できます。 ツインへの基本的なアクセスについては、ヘルパー クラスを使用すると便利です。

また、`BasicDigitalTwin` ヘルパー クラスを使用すると、ツインで定義されたプロパティに `Dictionary<string, object>` を介してアクセスできます。 ツインのプロパティを一覧表示するには、次のコードを使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin` では `System.Text.Json` 属性が使用されます。 `BasicDigitalTwin` を [DigitalTwinsClient](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true) で使用するためには、既定のコンストラクターを使用してクライアントを初期化する必要があります。または、シリアライザー オプションをカスタマイズする場合は、[JsonObjectSerializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true) を使用します。

##### <a name="create-a-digital-twin"></a>デジタル ツインを作成する

`BasicDigitalTwin` クラスを使用すると、ツイン インスタンスを作成するためのデータを準備できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

前述のコードは、次の "手動" バリアントと同じです。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>リレーションシップを逆シリアル化する

リレーションシップ データはいつでも任意の型に逆シリアル化できます。 リレーションシップへの基本的なアクセスには、`BasicRelationship` 型を使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

また、`BasicRelationship` ヘルパー クラスを使用すると、リレーションシップで定義されたプロパティに `IDictionary<string, object>` を介してアクセスできます。 プロパティを一覧表示するには、次のコードを使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>リレーションシップを作成する

`BasicRelationship` クラスを使用すると、ツイン インスタンスでリレーションシップを作成するためのデータを準備できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>ツインを更新するための修正プログラムを作成する

ツインとリレーションシップの Update 呼び出しでは、[JSON Patch](http://jsonpatch.com/) 構造を使用します。 JSON Patch 操作のリストを作成するには、下に示すように `JsonPatchDocument` を使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>一般的な API/SDK の使用上の注意

> [!NOTE]
> 現時点では、Azure Digital Twins では **クロス オリジン リソース共有 (CORS)** がサポートされないことに注意してください。 影響と解決方法の詳細については、[ *「クロス オリジン リソース共有 (CORS)」*](concepts-security.md#cross-origin-resource-sharing-cors)セクション (*概念:Azure Digital Twins ソリューションのセキュリティ* に関するページを参照してください。

次のリストに、API と SDK の使用に関する追加詳細情報および一般的なガイドラインを示します。

* Postman などの HTTP REST テスト ツールを使用して、Azure Digital Twins の API を直接呼び出すことができます。 このプロセスの詳細については、"[*Postman を使用して要求を作成する方法*](how-to-use-postman.md)" に関する記事を参照してください。
* SDK を使用するには、`DigitalTwinsClient` クラスをインスタンス化します。 コンストラクターには、`Azure.Identity` パッケージ内のさまざまな認証方法で取得できる資格情報が必要です。 `Azure.Identity` の詳細については、[名前空間に関するドキュメント](/dotnet/api/azure.identity)を参照してください。 
* 作業を開始するときには `InteractiveBrowserCredential` が役立ちますが、[マネージド ID](/dotnet/api/azure.identity.interactivebrowsercredential) の資格情報など、他にもいくつかのオプションがあります。これは、Azure Digital Twins に対して、[MSI を使用した Azure Functions のセットアップ](../app-service/overview-managed-identity.md?tabs=dotnet)を認証する際に使用できます。 `InteractiveBrowserCredential` の詳細については、[クラスのドキュメント](/dotnet/api/azure.identity.interactivebrowsercredential)を参照してください。
* すべてのサービス API 呼び出しは、`DigitalTwinsClient` クラスのメンバー関数として公開されます。
* すべてのサービス関数には、同期バージョンと非同期バージョンが存在します。
* すべてのサービス関数は、400 以上のリターン状態に対して例外をスローします。 `try` セクションに呼び出しをラップし、少なくとも `RequestFailedExceptions` をキャッチします。 この種類の例外の詳細については、[こちら](/dotnet/api/azure.requestfailedexception)を参照してください。
* ほとんどのサービス メソッドは `Response<T>` (または非同期呼び出しの場合は `Task<Response<T>>`) を返します。 `T` は、サービス呼び出しで返されるオブジェクトのクラスです。 [`Response`](/dotnet/api/azure.response-1) クラスは、サービスの戻り値をカプセル化し、その `Value` フィールドに戻り値を表示します。  
* ページングされた結果を含むサービス メソッドは、結果として `Pageable<T>` または `AsyncPageable<T>` を返します。 `Pageable<T>` クラスの詳細については、[こちら](/dotnet/api/azure.pageable-1)を参照してください。`AsyncPageable<T>` の詳細については、[こちら](/dotnet/api/azure.asyncpageable-1)を参照してください。
* `await foreach` ループを使用して、ページングした結果を反復処理できます。 この処理の詳細については、[こちら](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)を参照してください。
* 基になる SDK は `Azure.Core` です。 SDK のインフラストラクチャと種類については、[Azure 名前空間のドキュメント](/dotnet/api/azure)を参照してください。

サービス メソッドは、可能な限り、厳密に型指定されたオブジェクトを返します。 ただし、Azure Digital Twins は、実行時にユーザーによって構成されたカスタム モデル (サービスにアップロードされた DTDL モデルを使用) に基づいているため、多くのサービス API はツイン データを JSON 形式で取得して返します。

## <a name="monitor-api-metrics"></a>API メトリックの監視

要求、待機時間、失敗率などの API メトリックは、[Azure portal](https://portal.azure.com/) で見ることができます。 

portal のホームページで、Azure Digital Twins インスタンスを検索して詳細を取得します。 Azure Digital Twins インスタンスのメニューから **[メトリック]** オプションを選択して、 *[メトリック]* ページを表示します。

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins の [メトリック] ページが示されているスクリーンショット":::

ここから、インスタンスのメトリックを表示したり、カスタム ビューを作成したりできます。

## <a name="next-steps"></a>次のステップ

Postman を使用して API に直接要求を行う方法を参照してください。
* [*方法: Postman で要求を行う*](how-to-use-postman.md)

または、このチュートリアルを使用してクライアント アプリを作成し、.NET SDK を使用する練習を行います。
* [*チュートリアル:クライアント アプリをコーディングする*](tutorial-code.md)