---
title: クイック スタート:2 か国語辞書で単語を検索する、C# - Translator Text API
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、.NET Core と Translator Text API を使って、特定の用語に対する翻訳の代替候補とその用例を取得する方法について説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 02/21/2019
ms.author: erhopf
ms.openlocfilehash: b5a14791b30ef825a136840a81900940c6def16d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181250"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-c"></a>クイック スタート:C# を使用して 2 か国語辞書で単語を検索する

このクイック スタートでは、.NET Core と Translator Text API を使って、特定の用語に対する翻訳の代替候補とその用例を取得する方法について説明します。

このクイック スタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Translator Text リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/try/cognitive-services/)を使用してサブスクリプション キーを取得できます。

## <a name="prerequisites"></a>前提条件

* [.NET SDK](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Json.NET NuGet パッケージ](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)、または任意のテキスト エディター
* Translator Text の Azure サブスクリプション キー

## <a name="create-a-net-core-project"></a>.NET Core プロジェクトを作成する

新しいコマンド プロンプト (またはターミナル セッション) を開き、次のコマンドを実行します。

```console
dotnet new console -o alternate-sample
cd alternate-sample
```

最初のコマンドでは、2 つのことを行っています。 新しい .NET コンソール アプリケーションを作成し、`alternate-sample` という名前のディレクトリを作成します。 2 つ目のコマンドでは、目的のプロジェクトのディレクトリに変更しています。

次に、Json.Net をインストールする必要があります。 プロジェクトのディレクトリから、次を実行します。

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>必要な名前空間をプロジェクトに追加する

先ほど実行した `dotnet new console` コマンドによって、プロジェクトが作成されています。これには `Program.cs` が含まれています。 このファイルに、アプリケーション コードを記述することになります。 `Program.cs` を開いて、既存の using ステートメントを置き換えます。 これらのステートメントによって、サンプル アプリのビルドと実行に必要なすべての型に確実にアクセスすることができます。

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-get-alternate-translations"></a>翻訳の代替候補を取得するための関数を作成する

`Program` クラス内に、`AltTranslation` という関数を作成します。 このクラスには、Dictionary リソースを呼び出してその結果をコンソールに出力するコードがカプセル化されています。

```csharp
static void AltTranslation()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>サブスクリプション キー、ホスト名、パスを設定する

次の行を `AltTranslation` 関数に追加します。 `route` には、`api-version` と共に別途 2 つのパラメーターが追加されていることがわかります。 翻訳の入力と出力は、これらのパラメーターを使用して設定します。 このサンプルでは、英語 (`en`) とスペイン語 (`es`) です。

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/dictionary/lookup?api-version=3.0&from=en&to=es";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

次に、翻訳対象のテキストを含む JSON オブジェクトを作成してシリアル化する必要があります。 `body` 配列には複数のオブジェクトを渡すことができる点に注目してください。

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Elephants" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>クライアントをインスタンス化して要求を行う

次の行によって `HttpClient` と `HttpRequestMessage` がインスタンス化されます。

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>要求を構成して応答を出力する

`HttpRequestMessage` 内で次のことを行います。

* HTTP メソッドを宣言する
* 要求 URI を構成する
* 要求本文 (シリアル化した JSON オブジェクト) を挿入する
* 必要なヘッダーを追加する
* 非同期要求を実行する
* 応答の出力

次のコードを `HttpRequestMessage` に追加します。

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

`PrettyPrint` を追加して、JSON 応答に書式設定を追加します。
```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

最後の手順は、`Main` 関数での `AltTranslation()` の呼び出しです。 `static void Main(string[] args)` を探して、次の行を追加してください。

```csharp
AltTranslation();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

以上で、サンプル アプリを実行する準備が整いました。 コマンド ライン (またはターミナル セッション) から、プロジェクト ディレクトリに移動して次のコマンドを実行します。

```console
dotnet run
```

## <a name="sample-response"></a>応答のサンプル

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプション キーなどの秘密情報は、サンプル アプリのソース コードからすべて確実に削除してください。

## <a name="next-steps"></a>次の手順

このクイック スタートをはじめとする各種ドキュメントで表記変換と言語認識を含んだサンプル コードを詳しく見てみましょう。GitHub にある Translator Text の各種サンプル プロジェクトもご覧ください。

> [!div class="nextstepaction"]
> [GitHub で C# のコード例を詳しく見てみる](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>関連項目

* [テキストを翻訳する](quickstart-csharp-translate.md)
* [テキストを表記変換する](quickstart-csharp-transliterate.md)
* [入力によって言語を識別する](quickstart-csharp-detect.md)
* [サポートされている言語の一覧を取得する](quickstart-csharp-languages.md)
* [入力から文章の長さを判定する](quickstart-csharp-sentences.md)
