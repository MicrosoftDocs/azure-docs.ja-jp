---
title: クイック スタート:REST API を使用してモデルを取得する - LUIS
titleSuffix: Azure Cognitive Services
description: この REST API のクイック スタートでは、cURL を使用して発話を追加し、モデルをトレーニングします。
services: cognitive-services
author: nitinme
manager: diberry
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 06/19/2020
ms.author: nitinme
ms.openlocfilehash: e5cf3160e6592a48e3a81480480ad8f01cc6af3a
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206257"
---
# <a name="quickstart-use-curl-and-rest-to-change-model"></a>クイック スタート:cURL と REST を使用してモデルを変更する

このクイック スタートでは、Pizza アプリに発話の例を追加してアプリをトレーニングする方法を説明します。 発話の例は、意図にマッピングされる会話形式のユーザー テキストです。 意図に対する発話の例を与えるには、ユーザーによって入力されるどのようなテキストが、どの意図に属しているかを LUIS に学習させます。

このクイック スタートでは、3 つの REST 呼び出しを順番に実行します。

- まず、[REST のラベル バッチ追加](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)呼び出しを使用して、Pizza アプリ モデルのトレーニングに使用される発話の例のバッチをアップロードします。
- 次に、[REST のアプリケーション バージョン トレーニング](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)呼び出しを使用して、Pizza アプリのトレーニング セッションを開始します。
- 最後に、[REST のバージョン トレーニング状態取得](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46)呼び出しを使用して、Pizza アプリのトレーニング セッションの状態を取得します。

[リファレンス ドキュメント](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>前提条件

* 無料の [LUIS](https://www.luis.ai) アカウント。
* [Visual Studio Code](https://code.visualstudio.com/) などのテキスト エディター。
* コマンド ライン プログラム cURL。 cURL プログラムは、macOS、ほとんどの Linux ディストリビューション、Windows 10 ビルド 1803 以降に既にインストールされています。

    cURL をインストールする必要がある場合は、[cURL ダウンロード ページ](https://curl.haxx.se/download.html)から cURL をダウンロードできます。

## <a name="create-pizza-app"></a>Pizza アプリを作成する

[!INCLUDE [Create pizza app](includes/get-started-get-model-create-pizza-app.md)]

## <a name="create-a-json-file-to-train-the-pizza-app"></a>Pizza アプリをトレーニングするための JSON ファイルを作成する

発話の 3 つの例を含む JSON ファイルを作成するには、次の JSON データを `ExampleUtterances.JSON` という名前のファイルに保存します。

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

発話の例の JSON は、特定の形式に従っています。

`text` フィールドには、サンプルの発話テキストが含まれています。 `intentName` フィールドは、LUIS アプリの既存の意図の名前と対応している必要があります。 `entityLabels` フィールドは必須です。 どのエンティティもラベル付けしない場合は、空の配列を指定します。

entityLabels 配列が空でない場合、`entityName` フィールドで示されるエンティティを `startCharIndex` および `endCharIndex` で指定する必要があります。 インデックスは 0 から始まります。 テキスト内のスペースでラベルを開始または終了すると、発話を追加する API 呼び出しは失敗します。

## <a name="add-example-utterances"></a>発話の例を追加する

1. 発話の例のバッチをアップロードするには、次のコマンドをテキスト エディターにコピーします。

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***" \
          --data "@ExampleUtterances.JSON"
    ```

    ***

1. `***YOUR-` で始まる値を実際の値に置き換えます。

    |Information|目的|
    |--|--|
    |`***YOUR-AUTHORING-ENDPOINT***`| 作成 URL エンドポイント。 (例: "https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/")。 リソース名は、リソースの作成時に設定します。|
    |`***YOUR-APP-ID***`| LUIS アプリ ID。 |
    |`***YOUR-APP-VERSION***`| LUIS アプリのバージョン。 Pizza アプリの場合、バージョン番号は "0.1" です (引用符は除く)。|
    |`***YOUR-AUTHORING-KEY***`|32 文字の実際の作成キー。|

    割り当てられたキーとリソースは、LUIS ポータルの [Manage]\(管理\) セクションの **[Azure リソース]** ページで確認できます。 アプリ ID は、同じ [Manage]\(管理\) セクションの **[アプリケーションの設定]** ページで入手できます。

1. コマンド プロンプト (Windows) またはターミナル (macOS と Linux) を起動し、`ExampleUtterances.JSON` ファイルを保存したディレクトリに移動します。

1. エディターから cURL コマンドをコピーし、コマンド プロンプト (Windows) またはターミナル (macOS と Linux) に貼り付けます。 Enter キーを押してコマンドを実行します。

    次のような応答が表示されます。

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    読みやすいように書式設定された出力を次に示します。

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>Pizza アプリ モデルをトレーニングする

1. Pizza アプリのトレーニング セッションを開始するには、次のコマンドをテキスト エディターにコピーします。

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. 前の手順と同様に、`***YOUR-` で始まる値を実際の値に置き換えます。

1. エディターから cURL コマンドをコピーし、コマンド プロンプト (Windows) またはターミナル (macOS と Linux) に貼り付けます。 Enter キーを押してコマンドを実行します。

    次のような応答が表示されます。

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    読みやすいように書式設定された出力を次に示します。

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>トレーニングの状態を取得する

1. トレーニング セッションのトレーニング状態を取得するには、次のコマンドをテキスト エディターにコピーします。

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT***/luis/authoring/v3.0-preview/apps/***YOUR-APP-ID***/versions/***YOUR-APP-VERSION***/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: ***YOUR-AUTHORING-KEY***"
    ```

    ***

1. 前の手順と同様に、`***YOUR-` で始まる値を実際の値に置き換えます。

1. エディターから cURL コマンドをコピーし、コマンド プロンプト (Windows) またはターミナル (macOS と Linux) に貼り付けます。 Enter キーを押してコマンドを実行します。

    次のような応答が表示されます。

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    読みやすいように書式設定された出力を次に示します。

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイック スタートを完了したら、ファイル システムから `ExampleUtterances.JSON` ファイルを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリのベスト プラクティス](luis-concept-best-practices.md)
