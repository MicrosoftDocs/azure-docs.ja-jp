---
title: クイック スタート:Node.js 用の Bing Web Search SDK を使用する
titleSuffix: Azure Cognitive Services
description: Bing Web Search SDK を使用すると、Node.js アプリケーションに Bing Web Search を簡単に統合することができます。 このクイック スタートでは、クライアントをインスタンス化し、要求を送信して、応答を出力する方法を学習します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 059862255ca2cf0ce435496bf22d866a37c8be71
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977167"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>クイック スタート:Node.js 用の Bing Web Search SDK を使用する

Bing Web Search SDK を使用すると、Node.js アプリケーションに Bing Web Search を簡単に統合することができます。 このクイック スタートでは、クライアントをインスタンス化し、要求を送信して、応答を出力する方法を学習します。

今すぐコードを確認したい場合は、 GitHub で、[Node.js 用の Bing Web Search SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)を入手できます。

## <a name="prerequisites"></a>前提条件
このクイック スタートを実行するには、以下のものが必要です。

* [Node.js 6](https://nodejs.org/en/download/) 以降
* サブスクリプション キー  

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>開発環境を設定する

まず、Node.js プロジェクトの開発環境を設定します。

1. プロジェクト用の新規ディレクトリを作成します。

    ```console
    mkdir YOUR_PROJECT
    ```

1. 新しいパッケージ ファイルを作成します。

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. ここで、いくつかの Azure モジュールをインストールし、`package.json` に追加します。

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>プロジェクトの作成と必要なモジュールの宣言

`package.json` と同じディレクトリで、普段使用している IDE またはエディターで新しい Node.js プロジェクトを作成します。 (例: `sample.js`)。

次に、このコードをプロジェクトにコピーします。 前のセクションでインストールしたモジュールが読み込まれます。

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>クライアントのインスタンス化

次のコードは、クライアントをインスタンス化し、`azure-cognitiveservices-websearch` モジュールを使用します。 続行する前に、ご自分の Azure アカウントの有効なサブスクリプション キーを入力してください。

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>要求の実行と結果の出力

クライアントを使用して、Bing Web search に検索クエリを送信します。 応答に `properties` 配列のいずれかの項目の結果が含まれている場合、`result.value` がコンソールに出力されます。

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>プログラムの実行

最後の手順で、プログラムを実行します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このプロジェクトの使用を終了するときに、プログラムのコードからサブスクリプション キーを必ず削除してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Cognitive Services Node.js SDK サンプル](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>関連項目

* [Azure Node SDK リファレンス](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-websearch/)
