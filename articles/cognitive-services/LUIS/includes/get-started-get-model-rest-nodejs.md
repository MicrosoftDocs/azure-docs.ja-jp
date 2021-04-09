---
title: Node.js で REST 呼び出しを使用してモデルを取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.custom: devx-track-js
ms.openlocfilehash: fd25ef6ff44f1d62d553db518f7389da0e5d5019
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534633"
---
[リファレンス ドキュメント](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-model-with-rest/model.js)

## <a name="prerequisites"></a>前提条件

* [Node.js](https://nodejs.org/) プログラミング言語
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>発話の例の JSON ファイル

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-the-nodejs-project"></a>Node.js プロジェクトの作成

1. `node-model-with-rest` など、Node.js プロジェクトを保持する新しいフォルダーを作成します。

1. 新しいコマンド プロンプトを開き、作成したフォルダーに移動して、次のコマンドを実行します。

    ```console
    npm init
    ```

    各プロンプトで Enter キーを押して、既定の設定をそのまま使用します。

1. 次のコマンドを入力して request-promise モジュールをインストールします。

    ```console
    npm install --save request
    npm install --save request-promise
    npm install --save querystring
    ```

## <a name="change-model-programmatically"></a>プログラムを使用してモデルを変更する

1. `model.js` という名前で新しいファイルを作成します。 次のコードを追加します。

    [!code-javascript[Code snippet](~/cognitive-services-quickstart-code/javascript/LUIS/node-model-with-rest/model.js)]

1. `YOUR-` で始まる値を実際の値に置き換えます。

    |Information|目的|
    |--|--|
    |`YOUR-APP-ID`| LUIS アプリ ID。 |
    |`YOUR-AUTHORING-KEY`|32 文字の実際の作成キー。|
    |`YOUR-AUTHORING-ENDPOINT`| 作成 URL エンドポイント。 たとえば、「 `https://replace-with-your-resource-name.api.cognitive.microsoft.com/` 」のように入力します。 リソース名は、リソースの作成時に設定します。|

    割り当てられたキーとリソースは、LUIS ポータルの [Manage]\(管理\) セクションの **[Azure リソース]** ページで確認できます。 アプリ ID は、同じ [Manage]\(管理\) セクションの **[アプリケーションの設定]** ページで入手できます。

1. コマンド プロンプトに次のコマンドを入力してプロジェクトを実行します。

    ```console
    node model.js
    ```

1. 作成の応答を確認します。

    ```json
    addUtterance:
    [
      {
        "value": {
          "ExampleId": 1137150691,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150692,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1137150693,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    train POST:
    {
      "statusId": 9,
      "status": "Queued"
    }
    train GET:
    [
      {
        "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      },
      {
        "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
        "details": {
          "statusId": 9,
          "status": "Queued",
          "exampleCount": 0
        }
      }
    ]
    done
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートを完了したら、ファイル システムからプロジェクト フォルダーを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリのベスト プラクティス](../luis-concept-best-practices.md)