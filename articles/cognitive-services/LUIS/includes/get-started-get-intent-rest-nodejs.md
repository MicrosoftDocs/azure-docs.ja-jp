---
title: Node.js で REST 呼び出しを使用して意図を取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: a6dfe21cd92c5bf5580d7b121f33f68fb4e135fa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838541"
---
## <a name="prerequisites"></a>前提条件

* [Node.js](https://nodejs.org/) プログラミング言語 
* [Visual Studio Code](https://code.visualstudio.com/)
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> 完成した Node.js ソリューションは、[**Azure-Samples** の GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/analyze-text/node)から入手できます。

## <a name="get-luis-key"></a>LUIS キーを取得する

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

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

[!INCLUDE [Use authoring key for endpoint](../../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートを完了したら、Visual Studio プロジェクトを閉じ、ファイル システムからプロジェクトのディレクトリを削除します。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Node.js を使った発話の追加とトレーニング](../luis-get-started-node-add-utterance.md)