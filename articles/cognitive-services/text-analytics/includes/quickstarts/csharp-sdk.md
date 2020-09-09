---
title: クイック スタート:C# 用 Text Analytics クライアント ライブラリ | Microsoft Docs
description: C# 用 Text Analytics クライアント ライブラリの概要を紹介します
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/27/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: aa182f27fcd3ab2984bbd9f960b2b828880c36f5
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87375527"
---
<a name="HOLTop"></a>

#### <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

[v3 リファレンス ドキュメント](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3 パッケージ (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

[v2 リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics リソースを作成"  target="_blank">Text Analytics リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。  デプロイされたら、 **[リソースに移動]** をクリックします。
    * アプリケーションを Text Analytics API に接続するには、作成するリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-net-core-application"></a>新しい .NET Core アプリを作成する

Visual Studio IDE を使用して新しい .NET Core コンソール アプリを作成します。 1 つの C# ソース ファイル (*program.cs*) を含んだ "Hello World" プロジェクトが作成されます。

#### <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

**ソリューション エクスプローラー**でソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールします。 パッケージ マネージャーが開いたら、 **[参照]** を選択して `Azure.AI.TextAnalytics` を検索します。 バージョン `5.0.0` を選択し、 **[インストール]** を選択します。 [パッケージ マネージャー コンソール](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)を使用してもかまいません。


> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs) にあり、このクイックスタートのコード例が含まれています。 

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

**ソリューション エクスプローラー**でソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールします。 パッケージ マネージャーが開いたら、 **[参照]** を選択して `Microsoft.Azure.CognitiveServices.Language.TextAnalytics` を検索します。 それをクリックして **[インストール]** をクリックします。 [パッケージ マネージャー コンソール](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)を使用してもかまいません。

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs) にあり、このクイックスタートのコード例が含まれています。 

---

#### <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

*program.cs* ファイルを開き、次の `using` ディレクティブを追加します。

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

アプリケーションの `Program` クラスに、リソースのキーとエンドポイントの変数を作成します。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

アプリケーションの `Main` メソッドを置き換えます。 ここで呼び出されるメソッドは、後で定義します。

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

*program.cs* ファイルを開き、次の `using` ディレクティブを追加します。

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

アプリケーションの `Program` クラスに、リソースのキーとエンドポイントの変数を作成します。 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

アプリケーションの `Main` メソッドを置き換えます。 ここで呼び出されるメソッドは、後で定義します。

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>オブジェクト モデル

Text Analytics クライアントは、キーを使用して Azure に対して認証を行う `TextAnalyticsClient` オブジェクトであり、テキストを単一の文字列またはバッチとして受け取る機能を提供します。 テキストは、同期的または非同期的に API に送信できます。 応答オブジェクトには、送信する各ドキュメントの分析情報が格納されます。 

このサービスのバージョン `3.0` を使用している場合は、オプションの `TextAnalyticsClientOptions` インスタンスを使用すると、クライアントをさまざまな既定の設定 (既定の言語や国/地域ヒントなど) で初期化できます。 Azure Active Directory トークンを使用して認証することもできます。 

## <a name="code-examples"></a>コード例

* [感情分析](#sentiment-analysis)
* [言語検出](#language-detection)
* [名前付きエンティティの認識](#named-entity-recognition-ner)
* [エンティティ リンク設定](#entity-linking)
* [キー フレーズ抽出](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>クライアントを認証する

#### <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

先ほどの main メソッドが、エンドポイントと資格情報を使用して、新しいクライアント オブジェクトを作成していることを確認してください。

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

資格情報を格納し、それらをクライアントの要求に追加するための新しい `ApiKeyServiceClientCredentials` クラスを作成します。 その中で、`Ocp-Apim-Subscription-Key` ヘッダーにキーを追加する `ProcessHttpRequestAsync()` のオーバーライドを作成します。

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

エンドポイントと、キーを含む `ApiKeyServiceClientCredentials` オブジェクトを使用して、[TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) オブジェクトをインスタンス化します。

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>センチメント分析

#### <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

前に作成したクライアントを受け取る `SentimentAnalysisExample()` という新しい関数を作成し、その `AnalyzeSentiment()` 関数を呼び出します。 返される `Response<DocumentSentiment>` オブジェクトには、入力ドキュメント全体のセンチメント ラベルとスコアが含まれます。また、成功した場合は各文の感情分析が含まれます。 エラーが発生した場合は、`RequestFailedException` がスローされます。

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>出力

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

前に作成したクライアントを取得する `SentimentAnalysisExample()` という新しい関数を作成し、その [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) 関数を呼び出します。 返される [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) オブジェクトには、成功した場合はセンチメント `Score` が含まれ、失敗した場合は `errorMessage` が含まれます。 

0 に近いスコアが否定的な感情を示すのに対して、1 に近いスコアは肯定的な感情を示します。

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>言語検出

#### <a name="version-30"></a>[バージョン 3.0](#tab/version-3)


前に作成したクライアントを受け取る `LanguageDetectionExample()` という新しい関数を作成し、その `DetectLanguage()` 関数を呼び出します。 返された `Response<DetectedLanguage>` オブジェクトには、検出された言語と共に、その名前と ISO-6391 コードが含まれます。 エラーが発生した場合は、`RequestFailedException` がスローされます。

> [!Tip]
> 場合によっては、入力に基づいて言語を明確に区別するのが困難なことがあります。 `countryHint` パラメーターを使用して、2 文字の国/地域コードを指定できます。 API の既定では、既定の countryHint として "US" が使用されます。この動作を削除するには、この値を空の文字列に設定して (`countryHint = ""`)、このパラメーターをリセットします。 別の既定値を設定するには、`TextAnalyticsClientOptions.DefaultCountryHint` プロパティを設定し、クライアントの初期化中に渡します。

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>出力

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

前に作成したクライアントを取得する `languageDetectionExample()` という新しい関数を作成し、その [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 関数を呼び出します。 返される [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) オブジェクトには、成功した場合は `DetectedLanguages` で検出された言語のリストが含まれ、失敗した場合は `errorMessage` が含まれます。 最初に返された言語を出力します。

> [!Tip]
> 場合によっては、入力に基づいて言語を明確に区別するのが困難なことがあります。 `countryHint` パラメーターを使用して、2 文字の国/地域コードを指定できます。 API の既定では、既定の countryHint として "US" が使用されます。この動作を削除するには、この値を空の文字列 `countryHint = ""` に設定して、このパラメーターをリセットします。

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>出力

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>名前付きエンティティの認識 (NER)

#### <a name="version-30"></a>[バージョン 3.0](#tab/version-3)


> [!NOTE]
> バージョン `3.0` の新機能:
> * エンティティ リンク設定がエンティティの認識から切り離されました。


前に作成したクライアントを受け取る `EntityRecognitionExample()` という新しい関数を作成し、その `RecognizeEntities()` 関数を呼び出して、結果を反復処理します。 返される `Response<IReadOnlyCollection<CategorizedEntity>>` オブジェクトには、検出されたエンティティの一覧が含まれます。 エラーが発生した場合は、`RequestFailedException` がスローされます。

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>出力

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

## <a name="entity-linking"></a>エンティティ リンク設定

前に作成したクライアントを受け取る `EntityLinkingExample()` という新しい関数を作成し、その `RecognizeLinkedEntities()` 関数を呼び出して、結果を反復処理します。 返される `Response<IReadOnlyCollection<LinkedEntity>>` は、検出されたエンティティの一覧を表します。 エラーが発生した場合は、`RequestFailedException` がスローされます。 リンクされたエンティティは一意に識別されるため、同じエンティティの出現は、`LinkedEntityMatch` オブジェクトの一覧として `LinkedEntity` オブジェクトの下にグループ化されます。

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>出力

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

> [!NOTE]
> バージョン 2.1 では、エンティティ リンク設定が NER の応答に含まれています。

前に作成したクライアントを取得する `RecognizeEntitiesExample()` という新しい関数を作成し、その [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 関数を呼び出します。 結果を反復処理します。 返される [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) オブジェクトには、成功した場合は `Entities` で検出されたエンティティのリストが含まれ、失敗した場合は `errorMessage` が含まれます。 検出されたエンティティごとに、その型、サブタイプ、Wikipedia 名 (存在する場合)、および元のテキスト内の場所を出力します。

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>キー フレーズの抽出

#### <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

前に作成したクライアントを受け取る `KeyPhraseExtractionExample()` という新しい関数を作成し、その `ExtractKeyPhrases()` 関数を呼び出します。 返される `<Response<IReadOnlyCollection<string>>` オブジェクトには、検出されたキー フレーズの一覧が含まれます。 エラーが発生した場合は、`RequestFailedException` がスローされます。

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>出力

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

前に作成したクライアントを取得する `KeyPhraseExtractionExample()` という新しい関数を作成し、その [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 関数を呼び出します。 成功した場合は `KeyPhrases` 内で検出されたキー フレーズのリストが結果に含まれ、失敗した場合は `errorMessage` が含まれます。 検出されたキー フレーズを出力します。

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>出力

```console
Key phrases:
    cat
    veterinarian
```

---
