---
title: クイック スタート:C# SDK による予測エンドポイントに対するクエリの実行
titleSuffix: Azure Cognitive Services
description: C# SDK を使用して、LUIS にユーザーの発話を送信して予測を受け取ります。
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: af20d555a83e8d229ed5d83d3b1d3f242de1e4a8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275804"
---
# <a name="quickstart-query-prediction-endpoint-with-c-net-sdk"></a>クイック スタート:C# .NET SDK を使用した予測エンドポイントに対するクエリの実行

Language Understanding (LUIS) にユーザーの発話を送信してユーザーの意図の予測を受け取るには、[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) で見つかる .NET SDK を使用します。 

このクイック スタートでは、Language Understanding パブリック アプリケーションにユーザーの発話 (`turn on the bedroom light` など) を送信した後、予測を受け取り、トップスコアの意図 `HomeAutomation.TurnOn` と、発話内で検出されたエンティティ `HomeAutomation.Room` を表示します。 

## <a name="prerequisites"></a>前提条件

* [Visual Studio Community 2017 エディション](https://visualstudio.microsoft.com/vs/community/)
* C# プログラミング言語 (VS Community 2017 に含まれています)
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2

> [!Note]
> 完全なソリューションは、[cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime) GitHub リポジトリから入手できます。

その他のドキュメントをお探しですか?

 * [SDK のリファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Cognitive Services キーまたは Language Understanding キーを取得する

ホーム オートメーションにパブリック アプリを使用するには、エンドポイントの予測に有効なキーが必要です。 Cognitive Services キー (以下で Azure CLI を使用して作成されます) または `Language Understanding` キーを使用できます。Cognitive Services キーは、多くの Cognitive Services に対して有効です。 

次に示す [Cognitive Service キーを作成する Azure CLI コマンド](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create)を使用します。

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>.NET Core プロジェクトを作成する

Visual Studio Community 2017 で .NET Core コンソール プロジェクトを作成します。

1. Visual Studio Community 2017 を開きます。
1. 新しいプロジェクトを作成し、 **[Visual C#]** セクションで **[コンソール アプリ (.NET Core)]** を選択します。
1. プロジェクト名「`QueryPrediction`」を入力し、他の既定値はそのままにして、 **[OK]** を選択します。
    これで、**Program.cs** という名前のプライマリ コード ファイルを含む簡単なプロジェクトが作成されます。

## <a name="add-sdk-with-nuget"></a>NuGet を使用して SDK を追加する

1. **ソリューション エクスプローラー**のツリー ビューで、**QueryPrediction** という名前のプロジェクトを選択して右クリックします。 メニューから **[NuGet パッケージの管理]** を選択します。
1. **[参照]** を選択し、「`Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`」と入力します。 パッケージ情報が表示されたら、 **[インストール]** を選択してそのパッケージをプロジェクトにインストールします。 
1. **Program.cs** の先頭に次の _using_ ステートメントを追加します。 `System` の既存の _using_ ステートメントは削除しないでください。 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>予測用の新しいメソッドを作成する

新しいメソッド `GetPrediction` を作成し、クエリ予測エンドポイントにクエリを送信します。 このメソッドにより、必要なすべてのオブジェクトが作成、構成された後、[`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet) の予測結果を含む `Task` が返されます。 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>資格情報オブジェクトを作成する

`GetPrediction` メソッドに次のコードを追加し、自分の Cognitive Services キーを使用してクライアント資格情報を作成します。

`<REPLACE-WITH-YOUR-KEY>` を実際の Cognitive Services キーのリージョンに置き換えます。 このキーは、[Azure portal](https://portal.azure.com) の該当リソースの [キー] ページにあります。

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Language Understanding クライアントを作成する

`GetPrediction` メソッド内で、上記のコードの後に次のコードを追加し、[`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___) クライアント オブジェクトを作成して、新しい資格情報を使用します。 

`<REPLACE-WITH-YOUR-KEY-REGION>` を実際のキーのリージョン (`westus` など) に置き換えます。 このキーのリージョンは、[Azure portal](https://portal.azure.com) の該当リソースの [概要] ページにあります。

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>クエリ パラメーターを設定する

`GetPrediction` メソッド内で、上記のコードの後に次のコードを追加して、クエリ パラメーターを設定します。

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>予測エンドポイントに対するクエリの実行

`GetPrediction` メソッド内で、上記のコードの後に次のコードを追加して、クエリ パラメーターを設定します。

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>予測結果を表示する

新しい `GetPrediction` メソッドを呼び出すように **Main** メソッドを変更し、予測結果を返します。

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>プロジェクトを実行する

Studio でプロジェクトをビルドし、プロジェクトを実行してクエリの出力を表示します。

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>次の手順

[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) と [.NET リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)の詳細を確認してください。 

> [!div class="nextstepaction"] 
> [チュートリアル:ユーザーの意図を特定する LUIS アプリを構築する](luis-quickstart-intents-only.md) 