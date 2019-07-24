---
title: 順位を使用して検索結果を表示する
titleSuffix: Azure Cognitive Services
description: Bing RankingResponse の回答を使用して優先度順に検索結果を表示する方法について説明します。
services: cognitive-services
author: bradumbaugh
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/08/2017
ms.author: bking
ms.openlocfilehash: 7c074d5d25453e2b2a1ddfc32422790235815f1c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872524"
---
# <a name="build-a-console-app-search-client-in-c"></a>C# でのコンソール アプリ検索クライアントのビルド

このチュートリアルでは、ユーザーが Bing Web Search API にクエリを実行し、優先度を付けた結果を表示できるようにする単純な .NET Core コンソール アプリをビルドする方法を示します。

このチュートリアルでは、次の操作方法について説明します。

- Bing Web Search API への簡単なクエリの実行
- クエリ結果の優先度順の表示

## <a name="prerequisites"></a>前提条件

チュートリアルを進めるには、次のものが必要です。

- 見ることができます。 お持ちでない場合は、[無料版の Visual Studio 2017 Community Edition をダウンロードしインストールしてください](https://www.visualstudio.com/downloads/)。
- Bing Web Search API のサブスクリプション キー。 お持ちでない場合は、[無料試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)にサインアップしてください。

## <a name="create-a-new-console-app-project"></a>新しいコンソール アプリ プロジェクトを作成する

Visual Studio で、`Ctrl` + `Shift` + `N` キーを押してプロジェクトを作成します。

**[新しいプロジェクト]** ダイアログで、**[Visual C#] > [Windows クラシック デスクトップ] > [コンソール アプリ (.NET Framework)]** をクリックします。

アプリケーションに "**MyConsoleSearchApp**" という名前を付けて、**[OK]** をクリックします。

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>JSON.net Nuget パッケージをプロジェクトに追加する

JSON.net では、API によって返される JSON 応答を使用することができます。 NuGet パッケージをプロジェクトに追加します。

- **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理...]** を選びます。
- **[参照]** タブで `Newtonsoft.Json` を検索します。 最新のバージョンを選択し、**[インストール]** をクリックします。
- **[変更のレビュー]** ウィンドウで **[OK]** ボタンをクリックします。
- 次に示す Visual Studio タブを閉じます。**NuGet:MyConsoleSearchApp**。

## <a name="add-a-reference-to-systemweb"></a>System.Web に参照を追加する

このチュートリアルは、`System.Web` アセンブリに基づいています。 このアセンブリへの参照をプロジェクトに追加します。

- **ソリューション エクスプローラー**で **[参照]** を右クリックし、**[参照の追加...]** を選択します
- **[アセンブリ] > [フレームワーク]** を選択してから下にスクロールし、**System.Web** をチェックします
- **[OK]** を選択します。

## <a name="add-some-necessary-using-statements"></a>複数の必要な using ステートメントを追加する

このチュートリアルのコードでは、3 つの追加 using ステートメントが必要です。 **Program.cs** の上部にある既存の `using` ステートメントの下に、これらのステートメントを追加します。

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>ユーザーにクエリを求める

**ソリューション エクスプローラー**で、**Program.cs** を開きます。 `Main()` メソッドを更新します。

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

このメソッドは以下の操作を行います。

- ユーザーにクエリを求める
- `RunQueryAndDisplayResults(userQuery)` を呼び出してクエリを実行し、結果を表示する
- コンソール ウィンドウがすぐに終了しないようにするために、ユーザー入力を待機する。

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Bing Web Search API を使用したクエリ結果の検索

次に、API にクエリを実行し、結果を表示するメソッドを追加します。

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

このメソッドは以下の操作を行います。

- Web Search API にクエリを実行する `HttpClient` を作成する
- 要求の認証に Bing で使用する `Ocp-Apim-Subscription-Key` HTTP ヘッダーを設定する
- 要求を実行し、JSON.net を使用して結果を逆シリアル化する
- `DisplayAllRankedResults(responseObjects)` を呼び出し、優先度順にすべての結果を表示する

必ず、`Ocp-Apim-Subscription-Key` の値をお使いのサブスクリプション キーに設定してください。

## <a name="display-ranked-results"></a>順位付けされた結果を表示する

優先度順に結果を表示する方法を示す前に、Web 検索応答サンプルを見てください。

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

`rankingResponse` JSON オブジェクト ([ドキュメント](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)) には、検索結果の適切な表示順序が記されます。 そこには優先度が付けられた次の 1 つまたは複数のグループが含まれます。

- `pole`:最も見やすい取り扱い (メインラインとサイドバーの上に表示されるなど) を受ける検索結果。
- `mainline`:メインラインに表示される検索結果。
- `sidebar`:サイドバーに表示される検索結果。 サイドバーがない場合、結果はメインラインの下に表示されます。

優先度付け応答 JSON には、1 つまたは複数のグループが含まれる場合があります。

**Program.cs** で、次のメソッドを追加して、正しい優先度順で結果を表示します。

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

このメソッドは以下の操作を行います。

- 応答に含まれる `rankingResponse` グループ応答をループ処理する
- `DisplaySpecificResults(...)` を呼び出して、各グループ内の項目を表示する

**Program.cs** で次の 2 つのメソッドを追加します。

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

これらのメソッドは連携して、検索結果をコンソールに出力します。

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行します。 出力は次のようになります。

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>次の手順

[優先度付けを使用して結果を表示する](rank-results.md)に関するページをご覧ください。
