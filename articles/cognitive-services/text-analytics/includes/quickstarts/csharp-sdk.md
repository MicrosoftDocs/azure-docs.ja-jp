---
title: クイック スタート:C# 用 Text Analytics クライアント ライブラリ | Microsoft Docs
description: C# 用 Text Analytics クライアント ライブラリの概要を紹介します
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 6e71d9b4006d0353b094306424ba0fe99c581279
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090730"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

[v3.1 リファレンス ドキュメント](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview) | [v3.1 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [v3.1 パッケージ (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.3) | [v3.1 サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

[v3 リファレンス ドキュメント](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet) | [v3 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics) | [v3 パッケージ (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 サンプル](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

[v2 リファレンス ドキュメント](/dotnet/api/overview/azure/cognitiveservices/client) | [v2 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics リソースを作成"  target="_blank">Text Analytics リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。  デプロイされたら、 **[リソースに移動]** をクリックします。
    * アプリケーションを Text Analytics API に接続するには、作成するリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 分析機能を使用するには、Standard (S) 価格レベルの Text Analytics リソースが必要です。

## <a name="setting-up"></a>設定

### <a name="create-a-new-net-core-application"></a>新しい .NET Core アプリを作成する

Visual Studio IDE を使用して新しい .NET Core コンソール アプリを作成します。 1 つの C# ソース ファイル (*program.cs*) を含んだ "Hello World" プロジェクトが作成されます。

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

**ソリューション エクスプローラー** でソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールします。 パッケージ マネージャーが開いたら、 **[参照]** を選択して `Azure.AI.TextAnalytics` を検索します。 **[プレリリースを含める]** チェック ボックスをオンにし、バージョン `5.1.0-beta.3` を選択して、 **[インストール]** を選択します。 [パッケージ マネージャー コンソール](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)を使用してもかまいません。

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

**ソリューション エクスプローラー** でソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールします。 パッケージ マネージャーが開いたら、 **[参照]** を選択して `Azure.AI.TextAnalytics` を検索します。 バージョン `5.0.0` を選択し、 **[インストール]** を選択します。 [パッケージ マネージャー コンソール](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)を使用してもかまいません。


> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs) にあり、このクイックスタートのコード例が含まれています。 

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

**ソリューション エクスプローラー** でソリューションを右クリックし、 **[NuGet パッケージの管理]** を選択して、クライアント ライブラリをインストールします。 パッケージ マネージャーが開いたら、 **[参照]** を選択して `Microsoft.Azure.CognitiveServices.Language.TextAnalytics` を検索します。 それをクリックして **[インストール]** をクリックします。 [パッケージ マネージャー コンソール](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)を使用してもかまいません。

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs) にあり、このクイックスタートのコード例が含まれています。 

---

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

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
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

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

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

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

このサービスのバージョン `3.x` を使用している場合は、オプションの `TextAnalyticsClientOptions` インスタンスを使用すると、クライアントをさまざまな既定の設定 (既定の言語や国/地域ヒントなど) で初期化できます。 Azure Active Directory トークンを使用して認証することもできます。 

## <a name="code-examples"></a>コード例

* [感情分析](#sentiment-analysis)
* [意見マイニング](#opinion-mining)
* [言語検出](#language-detection)
* [名前付きエンティティの認識](#named-entity-recognition-ner)
* [エンティティ リンク設定](#entity-linking)
* [キー フレーズ抽出](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>クライアントを認証する

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

先ほどの main メソッドが、エンドポイントと資格情報を使用して、新しいクライアント オブジェクトを作成していることを確認してください。

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

先ほどの main メソッドが、エンドポイントと資格情報を使用して、新しいクライアント オブジェクトを作成していることを確認してください。

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

資格情報を格納し、それらをクライアントの要求に追加するための新しい `ApiKeyServiceClientCredentials` クラスを作成します。 その中で、`Ocp-Apim-Subscription-Key` ヘッダーにキーを追加する `ProcessHttpRequestAsync()` のオーバーライドを作成します。

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

エンドポイントと、キーを含む `ApiKeyServiceClientCredentials` オブジェクトを使用して、[TextAnalyticsClient](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient) オブジェクトをインスタンス化します。

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>センチメント分析

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

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

### <a name="opinion-mining"></a>意見マイニング

前に作成したクライアントを受け取る `SentimentAnalysisWithOpinionMiningExample()` という新しい関数を作成し、その `AnalyzeSentimentBatch()` 関数を `AdditionalSentimentAnalyses.OpinionMining` オプションで呼び出します。 返される `AnalyzeSentimentResultCollection` オブジェクトには、`Response<DocumentSentiment>` を表す `AnalyzeSentimentResult` のコレクションが含まれます。 `SentimentAnalysis()` と `SentimentAnalysisWithOpinionMiningExample()` の違いは、後者には、個々の文の `MinedOpinion`、つまり分析されたアスペクトおよび関連する意見を示す情報が含まれることです。 エラーが発生した場合は、`RequestFailedException` がスローされます。

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (MinedOpinion minedOpinion in sentence.MinedOpinions)
            {
                Console.WriteLine($"\tAspect: {minedOpinion.Aspect.Text}, Value: {minedOpinion.Aspect.Sentiment}");
                Console.WriteLine($"\tAspect positive score: {minedOpinion.Aspect.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tAspect negative score: {minedOpinion.Aspect.ConfidenceScores.Negative:0.00}");
                foreach (OpinionSentiment opinion in minedOpinion.Opinions)
                {
                    Console.WriteLine($"\t\tRelated Opinion: {opinion.Text}, Value: {opinion.Sentiment}");
                    Console.WriteLine($"\t\tRelated Opinion positive score: {opinion.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Opinion negative score: {opinion.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>出力

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Aspect: food, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: service, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: concierge, Value: Positive
        Aspect positive score: 1.00
        Aspect negative score: 0.00
                Related Opinion: nice, Value: Positive
                Related Opinion positive score: 1.00
                Related Opinion negative score: 0.00


Press any key to exit.
```

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

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

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

前に作成したクライアントを取得する `SentimentAnalysisExample()` という新しい関数を作成し、その [Sentiment()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment) 関数を呼び出します。 返される [SentimentResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult) オブジェクトには、成功した場合はセンチメント `Score` が含まれ、失敗した場合は `errorMessage` が含まれます。 

0 に近いスコアが否定的な感情を示すのに対して、1 に近いスコアは肯定的な感情を示します。

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>言語検出

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)


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

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)


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

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

前に作成したクライアントを取得する `languageDetectionExample()` という新しい関数を作成し、その [DetectLanguage()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 関数を呼び出します。 返される [LanguageResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult) オブジェクトには、成功した場合は `DetectedLanguages` で検出された言語のリストが含まれ、失敗した場合は `errorMessage` が含まれます。 最初に返された言語を出力します。

> [!Tip]
> 場合によっては、入力に基づいて言語を明確に区別するのが困難なことがあります。 `countryHint` パラメーターを使用して、2 文字の国/地域コードを指定できます。 API の既定では、既定の countryHint として "US" が使用されます。この動作を削除するには、この値を空の文字列 `countryHint = ""` に設定して、このパラメーターをリセットします。

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>出力

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>名前付きエンティティの認識 (NER)

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)


前に作成したクライアントを受け取る `EntityRecognitionExample()` という新しい関数を作成し、その `RecognizeEntities()` 関数を呼び出して、結果を反復処理します。 返される `Response<CategorizedEntityCollection>` オブジェクトには、検出されたエンティティ `CategorizedEntity` のコレクションが含まれます。 エラーが発生した場合は、`RequestFailedException` がスローされます。

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>出力

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>エンティティ リンク設定

前に作成したクライアントを受け取る `EntityLinkingExample()` という新しい関数を作成し、その `RecognizeLinkedEntities()` 関数を呼び出して、結果を反復処理します。 返される `Response<LinkedEntityCollection>` オブジェクトには、検出されたエンティティ `LinkedEntity` のコレクションが含まれます。 エラーが発生した場合は、`RequestFailedException` がスローされます。 リンクされたエンティティは一意に識別されるため、同じエンティティの出現は、`LinkedEntityMatch` オブジェクトの一覧として `LinkedEntity` オブジェクトの下にグループ化されます。

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
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>出力

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>個人を特定できる情報の認識

前に作成したクライアントを受け取る `RecognizePIIExample()` という新しい関数を作成し、その `RecognizePiiEntities()` 関数を呼び出して、結果を反復処理します。 返される `PiiEntityCollection` は、検出された PII エンティティの一覧を表します。 エラーが発生した場合は、`RequestFailedException` がスローされます。

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>出力

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)


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

### <a name="entity-linking"></a>エンティティ リンク設定

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

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

> [!NOTE]
> バージョン 2.1 では、エンティティ リンク設定が NER の応答に含まれています。

前に作成したクライアントを取得する `RecognizeEntitiesExample()` という新しい関数を作成し、その [Entities()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 関数を呼び出します。 結果を反復処理します。 返される [EntitiesResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult) オブジェクトには、成功した場合は `Entities` で検出されたエンティティのリストが含まれ、失敗した場合は `errorMessage` が含まれます。 検出されたエンティティごとに、その型、サブタイプ、Wikipedia 名 (存在する場合)、および元のテキスト内の場所を出力します。

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


### <a name="key-phrase-extraction"></a>キー フレーズの抽出

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

前に作成したクライアントを受け取る `KeyPhraseExtractionExample()` という新しい関数を作成し、その `ExtractKeyPhrases()` 関数を呼び出します。 返される `<Response<KeyPhraseCollection>` オブジェクトには、検出されたキー フレーズの一覧が含まれます。 エラーが発生した場合は、`RequestFailedException` がスローされます。

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

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

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

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

前に作成したクライアントを取得する `KeyPhraseExtractionExample()` という新しい関数を作成し、その [KeyPhrases()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 関数を呼び出します。 成功した場合は `KeyPhrases` 内で検出されたキー フレーズのリストが結果に含まれ、失敗した場合は `errorMessage` が含まれます。 検出されたキー フレーズを出力します。

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>出力

```console
Key phrases:
    cat
    veterinarian
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>分析操作で API を非同期的に使用する

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

前に作成したクライアントを受け取る `AnalyzeOperationExample()` という新しい関数を作成し、その `StartAnalyzeOperationBatch()` 関数を呼び出します。 返された `AnalyzeOperation` オブジェクトには、`AnalyzeOperationResult` の `Operation` インターフェイス オブジェクトが格納されます。 実行時間の長い操作であるため、更新する値の `operation.WaitForCompletionAsync()` に `await` します。 `WaitForCompletionAsync()` が完了したら、`operation.Value` でコレクションを更新する必要があります。 エラーが発生した場合は、`RequestFailedException` がスローされます。


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };

    AnalyzeOperationOptions operationOptions = new AnalyzeOperationOptions()
    {
        EntitiesTaskParameters = new EntitiesTaskParameters(),
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeOperation operation = client.StartAnalyzeOperationBatch(batchDocuments, operationOptions, "en");

    await operation.WaitForCompletionAsync();

    AnalyzeOperationResult resultCollection = operation.Value;

    RecognizeEntitiesResultCollection entitiesResult = resultCollection.Tasks.EntityRecognitionTasks[0].Results;

    Console.WriteLine("Analyze Operation Request Details");
    Console.WriteLine($"    Status: {resultCollection.Status}");
    Console.WriteLine($"    DisplayName: {resultCollection.DisplayName}");
    Console.WriteLine("");

    Console.WriteLine("Recognized Entities");

    foreach (RecognizeEntitiesResult result in entitiesResult)
    {
        Console.WriteLine($"    Recognized the following {result.Entities.Count} entities:");

        foreach (CategorizedEntity entity in result.Entities)
        {
            Console.WriteLine($"    Entity: {entity.Text}");
            Console.WriteLine($"    Category: {entity.Category}");
            Console.WriteLine($"    Offset: {entity.Offset}");
            Console.WriteLine($"    ConfidenceScore: {entity.ConfidenceScore}");
            Console.WriteLine($"    SubCategory: {entity.SubCategory}");
        }
        Console.WriteLine("");
    }
}
```

この例をアプリケーションに追加した後、`await` を使用して `main()` メソッドでを呼び出します。

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>出力

```console
Analyze Operation Request Details
    Status: succeeded
    DisplayName: Analyze Operation Quick Start Example

Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

また、分析操作を使用して、PII とキー フレーズ抽出を検出することもできます。 GitHub の[分析のサンプル](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeOperation.md)を参照してください。

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

この機能はバージョン 3.0 では使用できません。

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

この機能はバージョン 2.1 では使用できません。

---
