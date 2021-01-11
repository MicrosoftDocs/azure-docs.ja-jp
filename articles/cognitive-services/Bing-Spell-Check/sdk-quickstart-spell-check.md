---
title: クイック スタート:Bing Spell Check SDK for C# を使用してスペルをチェックする
titleSuffix: Azure Cognitive Services
description: Bing Spell Check REST API を使用してスペルと文法をチェックしてみましょう。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: e1e05ca9b63bda3373afb8a090118953d89ad8f9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934227"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>クイック スタート:Bing Spell Check SDK for C# を使用してスペルをチェックする

このクイック スタートを使用して、Bing Spell Check SDK for C# を使ったスペル チェックを始めましょう。 Bing Spell Check には、ほとんどのプログラミング言語に対応した REST API がありますが、SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck) にあります。

## <a name="application-dependencies"></a>アプリケーションの依存関係

* [Visual Studio 2017 またはそれ以降](https://visualstudio.microsoft.com/downloads/)の任意のエディション。
* Bing Spell Check [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

Bing Spell Check SDK をプロジェクトに追加するには、Visual Studio の**ソリューション エクスプローラー**から **[NuGet パッケージの管理]** をクリックします。 `Microsoft.Azure.CognitiveServices.Language.SpellCheck` パッケージを追加します。 このパッケージをインストールすると、次の依存関係もインストールされます。

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. Visual Studio で、新しい C# コンソール ソリューションを作成します。 次の `using` ステートメントを追加します。
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. 新しいクラスを作成します。 次に、サブスクリプション キーを受け取ってスペル チェック要求を送信する `SpellCheckCorrection()` という非同期関数を作成します。

3. 新しい `ApiKeyServiceClientCredentials` オブジェクトを作成してクライアントをインスタンス化します。 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>要求を送信して応答を読み取ります。

1. 先ほど作成した関数で、次の手順を実行します。 クライアントを使用してスペル チェック要求を送信します。 チェック対象のテキストを `text` パラメーターに追加し、モードを `proof` に設定します。  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. スペル チェック結果が存在する場合、1 つ目の結果を取得します。 スペル ミスとして返された最初の単語 (トークン)、トークンの種類、修正候補の数を出力します。

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. 1 つ目の修正候補が存在する場合はそれを取得します。 修正候補のスコアと単語を出力します。 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>アプリケーションの実行

プロジェクトをビルドして実行します。 Visual Studio を使用している場合は、**F5** キーを押してファイルをデバッグします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シングル ページ Web アプリを作成する](tutorials/spellcheck.md)

- [Bing Spell Check API とは](overview.md)
- [Bing Spell Check C# SDK リファレンス ガイド](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
