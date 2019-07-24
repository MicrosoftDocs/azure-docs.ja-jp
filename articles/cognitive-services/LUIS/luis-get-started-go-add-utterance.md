---
title: 変更、アプリのトレーニング、Go
description: この Go 言語クイック スタートでは、Home Automation アプリに発話の例を追加してアプリをトレーニングします。
titleSuffix: Language Understanding - Microsoft Cognitive Services
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 60594137306bfe58024877947959f2865fffe019
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961807"
---
# <a name="quickstart-change-model-using-go"></a>クイック スタート: Go を使ってモデルを変更する

このクイック スタートでは、LUIS エンドポイントに発話を渡して、意図とエンティティを取得します。

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Go](https://golang.org/) プログラミング言語がインストールされていること。
* [VSCode](https://code.visualstudio.com) 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>発話の例の JSON ファイル

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>クイック スタート コードの作成 

1. VSCode で `add-utterances.go` を作成します。 

2. 依存関係を追加します。 

   [!code-go[Add dependencies.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=2-10 "Add dependencies.")]

3. 一般的な HTTP 要求の関数を追加します。この関数には、オーサリング キーをヘッダーで渡す処理が含まれています。 

   [!code-go[Add HTTP request function which includes passing authoring key in header.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=12-36 "Add HTTP request function, which includes passing authoring key in header. ")]

4. JSON ファイルから発話の例を追加します。

   [!code-go[Add example utterances from JSON file.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=62-76 "Add example utterances from JSON file.")]

5. トレーニングを要求します。 トレーニングの状態と同じルートの動詞をヘルパー関数を使用して設定します。 

   [!code-go[Request training.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=77-86 "Request training. ")]

6. トレーニングの状態を要求します。 トレーニングの要求と同じルートの動詞をヘルパー関数を使用して設定します。 

   [!code-go[Request training status.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=87-90 "Request training status. ")]

7. コマンド ラインの解析を処理するメイン関数を追加します。

   [!code-go[Add main function to handle command line parsing.](~/samples-luis/documentation-samples/quickstarts/change-model/go/add-utterances.go?range=38-60 "Add main function to handle command-line parsing.")]

## <a name="add-an-utterance-from-the-command-line-train-and-get-status"></a>コマンド ラインから発話を追加し、トレーニングして、状態を取得する

1. Go ファイルを作成したディレクトリからコマンド プロンプトで「`go build add-utterances.go`」と入力し、Go ファイルをコンパイルします。 このコマンド プロンプトから、ビルドの成功に関する情報は一切返されません。

2. コマンド プロンプトに次のテキストを入力して、コマンド ラインから Go アプリケーションを実行します。 

    ```console
    add-utterances -appID <your-app-id> -authoringKey <add-your-authoring-key> -version <your-version-id> -region westus -utteranceFile utterances.json

    ```

    `<add-your-authoring-key>` は、実際のオーサリング キー (スターター キー) の値に置き換えます。 `<your-app-id>` は、実際のアプリ ID の値に置き換えます。 `<your-version-id>` は、実際のバージョン の値に置き換えます。 既定のバージョンは `0.1` です。

    このコマンド プロンプトを実行すると、次の結果が表示されます。

    ```console
    add example utterances requested
    [
        {
            "text": "go lang 1",
            "intentName": "None",
            "entityLabels": []
        }
    ,
        {
            "text": "go lang 2",
            "intentName": "None",
            "entityLabels": []
        }
    ]
    201
    [
        {
            "value": {
                "ExampleId": 77783998,
                "UtteranceText": "go lang 1"
            },
            "hasError": false
        },
        {
            "value": {
                "ExampleId": 77783999,
                "UtteranceText": "go lang 2"
            },
            "hasError": false
        }
    ]
    training selected
    202
    {"statusId":9,"status":"Queued"}
    training status selected
    200
    [
        {
            "modelId": "c52d6509-9261-459e-90bc-b3c872ee4a4b",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "5119cbe8-97a1-4c1f-85e6-6449f3a38d77",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "01e6b6bc-9872-47f9-8a52-da510cddfafe",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "33b409b2-32b0-4b0c-9e91-31c6cfaf93fb",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "1fb210be-2a19-496d-bb72-e0c2dd35cbc1",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "3d098beb-a1aa-423f-a0ae-ce08ced216d6",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "cce854f8-8f8f-4ed9-a7df-44dfea562f62",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        },
        {
            "modelId": "4d97bf0d-5213-4502-9712-2d6e77c96045",
            "details": {
                "statusId": 3,
                "status": "InProgress",
                "exampleCount": 24
            }
        }
    ]
    ```

    この応答には、3 つの HTTP 呼び出しについて、それぞれの HTTP 状態コードが含まれているほか、応答の本文で返された JSON 応答が含まれています。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイック スタートで作成したファイルは、完了後、すべて削除してください。 

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [カスタム ドメインを使用してアプリをビルドする](luis-quickstart-intents-only.md) 
