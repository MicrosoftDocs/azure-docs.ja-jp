---
title: クイック スタート:Node.js 用 Text Analytics v3 クライアント ライブラリ | Microsoft Docs
description: Node.js 用 Text Analytics v3 クライアント ライブラリの概要を紹介します。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: adfe6282d4a7d2cc35b4f5c71ecb67e6cda641a8
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090739"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

[v3 リファレンス ドキュメント](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview) | [v3 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 パッケージ (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Version 3.0](#tab/version-3)

[v3 リファレンス ドキュメント](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest) | [v3 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 パッケージ (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 サンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

[v2 リファレンス ドキュメント](/javascript/api/@azure/cognitiveservices-textanalytics) | [v2 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2 パッケージ (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2 サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Node.js](https://nodejs.org/)。
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics リソースを作成"  target="_blank">Text Analytics リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * アプリケーションを Text Analytics API に接続するには、作成するリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 分析機能を使用するには、Standard (S) 価格レベルの Text Analytics リソースが必要です。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp 

cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。 

```console
npm init
```
### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

`@azure/ai-text-analytics` NPM パッケージをインストールします。

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.3
```

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js) にあり、このクイックスタートのコード例が含まれています。 


# <a name="version-30"></a>[Version 3.0](#tab/version-3)

`@azure/ai-text-analytics` NPM パッケージをインストールします。

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js) にあり、このクイックスタートのコード例が含まれています。 

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

`@azure/cognitiveservices-textanalytics` NPM パッケージをインストールします。

```console
npm install --save @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js) にあり、このクイックスタートのコード例が含まれています。 

---

アプリの `package.json` ファイルが依存関係によって更新されます。
`index.js` という名前のファイルを作成し、以下を追加します。

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

```javascript
"use strict";

const os = require("os");
const CognitiveServicesCredentials = require("@azure/ms-rest-js");
const TextAnalyticsAPIClient = require("@azure/cognitiveservices-textanalytics");
```
---

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>オブジェクト モデル

Text Analytics クライアントは、キーを使用して Azure に対して認証を行う `TextAnalyticsClient` オブジェクトです。 このクライアントには、テキストを単一の文字列として、またはバッチとして分析するためのメソッドがいくつか備わっています。

テキストは、`documents` (使用したメソッドに応じて `id`、`text`、`language` の各属性の組み合わせを保持する `dictionary` オブジェクト) のリストとして API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

## <a name="code-examples"></a>コード例

* [クライアント認証](#client-authentication)
* [感情分析](#sentiment-analysis) 
* [オピニオン マイニング](#opinion-mining)
* [言語検出](#language-detection)
* [名前付きエンティティの認識](#named-entity-recognition-ner)
* [エンティティ リンク設定](#entity-linking)
* 個人を特定できる情報
* [キー フレーズ抽出](#key-phrase-extraction)

## <a name="client-authentication"></a>クライアント認証

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

キーとエンドポイントをパラメーターとして使用して、新しい `TextAnalyticsClient` オブジェクトを作成します。

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

キーとエンドポイントをパラメーターとして使用して、新しい `TextAnalyticsClient` オブジェクトを作成します。

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

`credentials` および `endpoint` をパラメーターとして新しい [TextAnalyticsClient](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) オブジェクトを作成します。

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>センチメント分析

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `analyzeSentiment()` メソッドを呼び出し、返される `SentimentBatchResult` オブジェクトを取得します。 結果の一覧を反復処理し、各ドキュメントの ID、ドキュメント レベルのセンチメントと信頼度スコアを出力します。 各ドキュメントの結果には、文レベルのセンチメントと、オフセット、長さ、および信頼度スコアが含まれます。

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>意見マイニング

オピニオン マイニングを使用した感情分析を行うには、分析するドキュメントを含む文字列の配列を作成します。 クライアントの `analyzeSentiment()` メソッドに別途オプション フラグ `includeOpinionMining: true` を渡して呼び出し、返される `SentimentBatchResult` オブジェクトを取得します。 結果の一覧を反復処理し、各ドキュメントの ID、ドキュメント レベルのセンチメントと信頼度スコアを出力します。 各ドキュメントの結果には、前述した文レベルのセンチメントだけでなく、アスペクトおよびオピニオン レベルのセンチメントも含まれます。

```javascript
async function sentimentAnalysisWithOpinionMining(client){

    const sentimentInput = [
        {
            text: "The food and service were unacceptable, but the concierge were nice",
            id: "0",
            language: "en"
        }
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
            console.log("\tMined opinions");
            for (const { aspect, opinions } of sentence.minedOpinions) {
                console.log(`\t\tAspect text: ${aspect.text}`);
                console.log(`\t\tAspect sentiment: ${aspect.sentiment}`);
                console.log(`\t\tAspect Positive: ${aspect.confidenceScores.positive.toFixed(2)} \tNegative: ${aspect.confidenceScores.negative.toFixed(2)}`);
                console.log("\t\tAspect opinions:");
                for (const { text, sentiment, confidenceScores } of opinions) {
                    console.log(`\t\tOpinion text: ${text}`);
                    console.log(`\t\tOpinion sentiment: ${sentiment}`);
                    console.log(`\t\tOpinion Positive: ${confidenceScores.positive.toFixed(2)} \tNegative: ${confidenceScores.negative.toFixed(2)}`);
                }
            }
        });
    });
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Sentences Sentiment(1):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 0.84  Negative: 0.16  Neutral: 0.00
        Mined opinions
                Aspect text: food
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: service
                Aspect sentiment: negative
                Aspect Positive: 0.01   Negative: 0.99
                Aspect opinions:
                Opinion text: unacceptable
                Opinion sentiment: negative
                Opinion Positive: 0.01  Negative: 0.99
                Aspect text: concierge
                Aspect sentiment: positive
                Aspect Positive: 1.00   Negative: 0.00
                Aspect opinions:
                Opinion text: nice
                Opinion sentiment: positive
                Opinion Positive: 1.00  Negative: 0.00
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `analyzeSentiment()` メソッドを呼び出し、返される `SentimentBatchResult` オブジェクトを取得します。 結果の一覧を反復処理し、各ドキュメントの ID、ドキュメント レベルのセンチメントと信頼度スコアを出力します。 各ドキュメントの結果には、文レベルのセンチメントと、オフセット、長さ、および信頼度スコアが含まれます。

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

分析するドキュメントを含んだディクショナリ オブジェクトの一覧を作成します。 クライアントの [sentiment()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) メソッドを呼び出し、返された [SentimentBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult) を取得します。 結果の一覧を反復処理し、各ドキュメントの ID とセンチメント スコアを印刷します。 0 に近いスコアは否定的センチメント、1 に近いスコアは肯定的センチメントを示します。

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>言語検出

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `detectLanguage()` メソッドを呼び出し、返される `DetectLanguageResultCollection` を取得します。 次に、結果を反復処理して、各ドキュメントの ID とそれぞれの第一言語を出力します。

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `detectLanguage()` メソッドを呼び出し、返される `DetectLanguageResultCollection` を取得します。 次に、結果を反復処理して、各ドキュメントの ID とそれぞれの第一言語を出力します。

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
ID: 0
        Primary Language French
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

ドキュメントを含むディクショナリ オブジェクトの一覧を作成します。 クライアントの [detectLanguage()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) メソッドを呼び出し、返された [LanguageBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult) を取得します。 結果を反復処理し、各ドキュメントの ID と言語を出力します。

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>名前付きエンティティの認識 (NER)

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

> [!NOTE]
> バージョン `3.1`:
> * エンティティ リンク設定は、NER から切り離された要求です。

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `recognizeEntities()` メソッドを呼び出し、`RecognizeEntitiesResult` オブジェクトを取得します。 結果の一覧を反復処理し、エンティティ名、タイプ、サブタイプ、オフセット、長さ、およびスコアを出力します。

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entity Linking

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `recognizeLinkedEntities()` メソッドを呼び出し、`RecognizeLinkedEntitiesResult` オブジェクトを取得します。 結果の一覧を反復処理し、エンティティ名、ID、データ ソース、URL、および一致を出力します。 `matches` 配列内の各オブジェクトには、その一致のオフセット、長さ、およびスコアが含まれています。

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>個人を特定できる情報 (PII) の認識

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `recognizePiiEntities()` メソッドを呼び出し、`RecognizePIIEntitiesResult` オブジェクトを取得します。 結果の一覧を反復処理し、エンティティ名、タイプ、スコアを出力します。

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

> [!NOTE]
> バージョン `3.0`:
> * エンティティ リンク設定は、NER から切り離された要求です。

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `recognizeEntities()` メソッドを呼び出し、`RecognizeEntitiesResult` オブジェクトを取得します。 結果の一覧を反復処理し、エンティティ名、タイプ、サブタイプ、オフセット、長さ、およびスコアを出力します。

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entity Linking

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `recognizeLinkedEntities()` メソッドを呼び出し、`RecognizeLinkedEntitiesResult` オブジェクトを取得します。 結果の一覧を反復処理し、エンティティ名、ID、データ ソース、URL、および一致を出力します。 `matches` 配列内の各オブジェクトには、その一致のオフセット、長さ、およびスコアが含まれています。

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

> [!NOTE]
> バージョン 2.1 では、エンティティ リンク設定が NER の応答に含まれています。

ドキュメントを含むオブジェクトの一覧を作成します。 クライアントの [entities()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) メソッドを呼び出し、[EntitiesBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) オブジェクトを取得します。 結果の一覧を反復処理し、各ドキュメントの ID を印刷します。 検出されたエンティティごとに、その wikipedia 名、型とサブタイプ (存在する場合)、および元のテキスト内の位置を印刷します。

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `extractKeyPhrases()` メソッドを呼び出し、返される `ExtractKeyPhrasesResult` オブジェクトを取得します。 結果を反復処理し、各ドキュメントの ID と検出されたすべてのキー フレーズを出力します。

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

分析するドキュメントを含む文字列の配列を作成します。 クライアントの `extractKeyPhrases()` メソッドを呼び出し、返される `ExtractKeyPhrasesResult` オブジェクトを取得します。 結果を反復処理し、各ドキュメントの ID と検出されたすべてのキー フレーズを出力します。

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

ドキュメントを含むオブジェクトの一覧を作成します。 クライアントの [keyPhrases()](/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) メソッドを呼び出し、返された [KeyPhraseBatchResult](/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) オブジェクトを取得します。 結果を反復処理し、各ドキュメントの ID と検出されたすべてのキー フレーズを出力します。

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>出力

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>分析操作で API を非同期的に使用する

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

> [!CAUTION]
> 分析操作を使用するには、Standard (S) 価格レベルの Text Analytics リソースを使用する必要があります。  

`beginAnalyze()` 関数を呼び出す `analyze_example()` という名前の新しい関数を作成します。 この操作の実行には時間がかかり、結果に対してポーリングされます。

```javascript
const documents = [
  "Microsoft was founded by Bill Gates and Paul Allen.",
];

async function analyze_example(client) {
  console.log("== Analyze Sample ==");

  const tasks = {
    entityRecognitionTasks: [{ modelVersion: "latest" }]
  };
  const poller = await client.beginAnalyze(documents, tasks);
  const resultPages = await poller.pollUntilDone();

  for await (const page of resultPages) {
    const entitiesResults = page.entitiesRecognitionResults![0];
    for (const doc of entitiesResults) {
      console.log(`- Document ${doc.id}`);
      if (!doc.error) {
        console.log("\tEntities:");
        for (const entity of doc.entities) {
          console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
        }
      } else {
        console.error("  Error:", doc.error);
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>出力

```console
== Analyze Sample ==
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

また、分析操作を使用して、PII とキー フレーズ抽出を検出することもできます。 GitHub で「[JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/javascript/beginAnalyze.js) と [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/textanalytics/ai-text-analytics/samples/typescript/src/beginAnalyze.ts) のサンプルを分析する」を参照してください。

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

この機能はバージョン 3.0 では使用できません。

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

この機能はバージョン 2.1 では使用できません。

---

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```
