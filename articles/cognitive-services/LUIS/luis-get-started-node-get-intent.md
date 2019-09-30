---
title: クイック スタート:意図の取得、Node.js - LUIS
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、提供されているパブリック LUIS アプリを使って、会話形式のテキストからユーザーの意図を判断します。 Node.js を使用して、パブリック アプリの HTTP 予測エンドポイントにユーザーの意図をテキストとして送信します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 78df1eb546d285b6b477a6dd53efa0d3dd9d7ce8
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375471"
---
# <a name="quickstart-get-intent-using-nodejs"></a>クイック スタート: Node.js を使用した意図の取得

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>前提条件

* [Node.js](https://nodejs.org/) プログラミング言語 
* [Visual Studio Code](https://code.visualstudio.com/)
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> 完成した Node.js ソリューションは、[**Azure-Samples** の GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node)から入手できます。

## <a name="get-luis-key"></a>LUIS キーを取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>ブラウザーで意図を取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>プログラムで意図を取得する

Node.js を使用して、前の手順でブラウザー ウィンドウに表示されたものと同じ結果にアクセスできます。

1. 次のコード スニペットをコピーします。

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. 次のテキストを含んだ `.env` ファイルを作成するか、システム環境内でこれらの変数を設定します。

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. `LUIS_ENDPOINT_KEY` 環境変数を実際のキーに設定します。

4. コマンド ラインから `npm install` コマンドを実行して依存関係をインストールします。

5. `npm start` でコードを実行します。 前の手順でブラウザー ウィンドウに表示されたものと同じ値が表示されます。

## <a name="luis-keys"></a>LUIS キー

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Node.js ファイルを削除します。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [発話の追加](luis-get-started-node-add-utterance.md)
