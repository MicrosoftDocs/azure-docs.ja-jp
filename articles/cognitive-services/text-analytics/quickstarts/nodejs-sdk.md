---
title: クイック スタート:Node.js 用 Text Analytics クライアント ライブラリ | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Text Analytics API をすぐに使い始めるのに役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 8590acbbd6001c1f214589298e454c1e75f93d67
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883529"
---
# <a name="quickstart-text-analytics-client-library-for-nodejs"></a>クイック スタート:Node.js 用 Text Analytics クライアント ライブラリ
<a name="HOLTop"></a>

Node.js 用 Text Analytics クライアント ライブラリの概要を紹介します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 

Node.js 用 Text Analytics クライアント ライブラリを使って次のことを実行します。

* センチメント分析
* 言語検出
* エンティティの認識
* キー フレーズの抽出

[リファレンスのドキュメント](https://docs.microsoft.com/javascript/api/overview/azure/cognitiveservices/textanalytics?view=azure-node-latest) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [パッケージ (NPM)](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) | [サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](../../cognitive-services-apis-create-account.md) または [Azure CLI](../../cognitive-services-apis-create-account-cli.md) を使用して、ローカル コンピューター上に Text Analytics のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 これは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。  
* お使いのリソースを [Azure portal](https://portal.azure.com/) で表示する

試用版のサブスクリプションまたはリソースからキーを取得した後、`TEXTANALYTICS_SUBSCRIPTION_KEY` という名前のキーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用してノード アプリケーションを作成します。 

```console
npm init
```

`index.js` という名前のファイルを作成して次のライブラリをインポートします。

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```javascript
// replace this endpoint with the correct one for your Azure resource. 
let endpoint = "https://westus.api.cognitive.microsoft.com/";
// This sample assumes you have created an environment variable for your key
let key = var apiKey = process.env.TEXTANALYTICS_SUBSCRIPTION_KEY;
let credentials = new CognitiveServicesCredentials(
    key
);
```

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

`ms-rest-azure` および `azure-cognitiveservices-textanalytics` NPM パッケージをインストールします。

```console
npm install azure-cognitiveservices-textanalytics ms-rest-azure
```

アプリの `package.json` ファイルが依存関係によって更新されます。

## <a name="object-model"></a>オブジェクト モデル

Text Analytics クライアントは、ご利用のキーを使用して Azure に対して認証を行う [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) オブジェクトです。 このクライアントには、テキストを単一の文字列として、またはバッチとして分析するためのメソッドがいくつか備わっています。

テキストは、`documents` (使用したメソッドに応じて `id`、`text`、`language` の各属性の組み合わせを保持する `dictionary` オブジェクト) のリストとして API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

## <a name="code-examples"></a>コード例

* [クライアントを認証する](#authenticate-the-client)
* [感情分析](#sentiment-analysis)
* [言語検出](#language-detection)
* [エンティティの認識](#entity-recognition)
* [キー フレーズ抽出](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>クライアントを認証する

`credentials` および `endpoint` をパラメーターとして新しい [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-textanalytics/textanalyticsclient?view=azure-node-latest) オブジェクトを作成します。

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    endpoint
);
```

## <a name="sentiment-analysis"></a>センチメント分析

分析するドキュメントを含むオブジェクトの一覧を作成します。

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."}
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
[ { id: '1', score: 0.8723785877227783 } ]
```

## <a name="language-detection"></a>言語検出

ドキュメントを含むオブジェクトの一覧を作成します。

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." }
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
```

## <a name="entity-recognition"></a>エンティティの認識

ドキュメントを含むオブジェクトの一覧を作成します。

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"}
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
```

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

ドキュメントを含むオブジェクトの一覧を作成します。

```javascript
    let inputLanguage = {
    documents: [
        {language:"en", id:"1", text:"My cat might need to see a veterinarian."}
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
    { id: '1', keyPhrases: [ 'cat', 'veterinarian' ] }
]
```

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics の概要](../overview.md)
* [感情分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [エンティティの認識](../how-tos/text-analytics-how-to-entity-linking.md)
* [言語を検出する](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [言語の認識](../how-tos/text-analytics-how-to-language-detection.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js) にあります。
