---
title: クイック スタート:Node.js を使用して Text Analytics API を呼び出す
titleSuffix: Azure Cognitive Services
description: Text Analytics API をすぐに使い始めるのに役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: shthowse
ms.openlocfilehash: 7e43d53c0916cf7fdc684c9e044e632015662c3b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080933"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>クイック スタート:Node.js を使用して Text Analytics Cognitive Service を呼び出す
<a name="HOLTop"></a>

このクイック スタートを使用して、Node.js 用 Text Analytics SDK を使用した言語の分析を開始します。 [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API は、ほとんどのプログラミング言語に対応しますが、この SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js) にあります。

API の技術ドキュメントについては、[API の定義](//go.microsoft.com/fwlink/?LinkID=759346)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* [Node.JS](https://nodejs.org/)
* SDK のインストールに使用できる [Node.js 用](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) Text Analytics SDK。

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

また、サインアップ中に生成された[エンドポイントとアクセス キー](../How-tos/text-analytics-how-to-access-key.md)も必要です。

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Node.js アプリケーションの作成と SDK のインストール

Node.js をインストールしたら、ノード プロジェクトを作成します。 アプリの新しいディレクトリを作成し、そのディレクトリに移動します。

```mkdir myapp && cd myapp```

```npm init``` を実行し、package.json ファイルを使用してノード アプリケーションを作成します。 `ms-rest-azure` および `azure-cognitiveservices-textanalytics` NPM パッケージをインストールします。

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

アプリの package.json ファイルは依存関係を含めて更新されます。

## <a name="authenticate-your-credentials"></a>資格情報の認証

プロジェクト ルートに新しいファイル `index.js` を作成し、インストールされているライブラリをインポートします

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Text Analytics サブスクリプション キーの変数を作成します。

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> 運用システムでシークレットを安全にデプロイするために、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net) の使用をお勧めします。
>

## <a name="create-a-text-analytics-client"></a>Text Analytics クライアントの作成

パラメーターとして `credentials` を使用して新しい `TextAnalyticsClient` オブジェクトを作成します。 自分の Text Analytics サブスクリプションに適した Azure リージョンを使用してください。

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>センチメント分析

分析するドキュメントを含むオブジェクトの一覧を作成します。 API に対するペイロードは、`id`、`language`、および `text` 属性を含む `documents` の一覧で構成されます。 `text` 属性には分析対象のテキストが格納され、`language` はドキュメントの言語であり、`id` には任意の値を指定できます。 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

`client.sentiment` を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID とセンチメント スコアを印刷します。 0 に近いスコアは否定的センチメント、1 に近いスコアは肯定的センチメントを示します。

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>言語検出

ドキュメントを含むオブジェクトの一覧を作成します。 API に対するペイロードは、`id` および `text` 属性を含む `documents` の一覧で構成されます。 `text` 属性には、分析対象のテキストが格納され、`id` には任意の値を指定できます。

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
    ]
    };
```

`client.detectLanguage()` を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID と最初に返された言語を印刷します。

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>エンティティの認識

ドキュメントを含むオブジェクトの一覧を作成します。 API に対するペイロードは、`id`、`language`、および `text` 属性を含む `documents` の一覧で構成されます。 `text` 属性には分析対象のテキストが格納され、`language` はドキュメントの言語であり、`id` には任意の値を指定できます。

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

`client.entities()` を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID を印刷します。 検出されたエンティティごとに、その wikipedia 名、型とサブタイプ (存在する場合)、および元のテキスト内の位置を印刷します。

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

ドキュメントを含むオブジェクトの一覧を作成します。 API に対するペイロードは、`id`、`language`、および `text` 属性を含む `documents` の一覧で構成されます。 `text` 属性には分析対象のテキストが格納され、`language` はドキュメントの言語であり、`id` には任意の値を指定できます。

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
    ]
    };
```

`client.keyPhrases()` を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID と検出されたすべてのキー フレーズを印刷します。

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

コンソール ウィンドウで `node index.js` を使用してコードを実行します。

### <a name="output"></a>Output

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>関連項目

 [Text Analytics の概要](../overview.md) [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)
