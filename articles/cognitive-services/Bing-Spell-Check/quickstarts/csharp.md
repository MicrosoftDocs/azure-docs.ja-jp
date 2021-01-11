---
title: クイック スタート:REST API と C# を使用してスペルをチェックする - Bing Spell Check
titleSuffix: Azure Cognitive Services
description: Bing Spell Check REST API を使用してスペルと文法をチェックしてみましょう。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: df77ea73338f37925c0b59a8838c31617ff22ad8
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919405"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>クイック スタート:Bing Spell Check REST API と C# を使用してスペルをチェックする

このクイック スタートを使用して、Bing Spell Check の REST API を呼び出してみましょう。 このシンプルな C# アプリケーションは、API に要求を送信して、一連の修正候補を返します。 

このアプリケーションは C# で記述されていますが、この API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このアプリケーションのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs) で入手できます。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2017 またはそれ以降](https://www.visualstudio.com/downloads/)の任意のエディション。
* Newtonsoft.Json NuGet パッケージ。 
     
   Visual Studio にこのパッケージをインストールするには

     1. **ソリューション エクスプローラー**で、ソリューション ファイルを右クリックします。
     1. **[ソリューションの NuGet パッケージの管理]** を選択します。
     1. *Newtonsoft.json* を探してパッケージをインストールします。

* Linux または MacOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

1. Visual Studio で、SpellCheckSample という新しいコンソール ソリューションを作成します。 次に、メイン コード ファイルに次の名前空間を追加します。
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. API エンドポイント、サブスクリプション キー、スペル チェックの対象テキストのための変数を作成します。 次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. 検索パラメーターの文字列を作成します。 

   1. `=` 演算子を使用して、`mkt` パラメーターに市場コードを割り当てます。 市場コードは、要求の送信元となる国/地域のコードです。 

   1. `&` 演算子を使用して `mode` パラメーターを追加し、スペルチェック モードを割り当てます。 モードは `proof` (スペルまたは文法のほとんどのエラーが検出されます)、または `spell` (スペル ミスはほとんど検出されますが、文法エラーの検出数は相対的に少なくなります) のいずれかにすることができます。
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>スペル チェック要求を作成して送信する

1. API に要求を送信するための `SpellCheck()` という非同期関数を作成します。 `HttpClient` を作成し、`Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加します。 この関数内で、次の手順に従います。

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. ホスト、パス、およびパラメーターを追加して、要求の URI を作成します。
    
    ```csharp
    string uri = host + path + params_;
    ```

3. 対象テキストを含んだ `KeyValuePair` オブジェクトのリストを作成し、それを使用して `FormUrlEncodedContent` オブジェクトを作成します。 ヘッダー情報を設定し、`PostAsync()` を使用して要求を送信します。

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>API の応答を取得して出力する

### <a name="get-the-client-id-header"></a>クラアント ID ヘッダーを取得する

応答に `X-MSEdge-ClientID` ヘッダーが含まれている場合は、その値を取得して出力します。

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>応答を取得する

API から応答を取得します。 JSON オブジェクトを逆シリアル化し、それをコンソールに出力します。

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>スペル チェック関数を呼び出す

プロジェクトの `Main()` 関数で `SpellCheck()` を呼び出します。

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>アプリケーションの実行

プロジェクトをビルドして実行します。 Visual Studio を使用している場合は、**F5** キーを押してファイルをデバッグします。

## <a name="example-json-response"></a>JSON の応答例

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../tutorials/spellcheck.md)

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
