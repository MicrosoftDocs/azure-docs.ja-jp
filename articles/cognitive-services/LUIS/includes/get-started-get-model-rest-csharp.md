---
title: C# で REST 呼び出しを使用してモデルを取得する
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: aaf7658796c50bb30305b5fda0527141cb820289
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534611"
---
[リファレンス ドキュメント](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/csharp-model-with-rest/Program.cs)

## <a name="prerequisites"></a>前提条件

* [.NET Core 3.1](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>発話の例の JSON ファイル

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-pizza-app"></a>Pizza アプリを作成する

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]

## <a name="change-model-programmatically"></a>プログラムを使用してモデルを変更する

1. `csharp-model-with-rest` のプロジェクト名とフォルダー名を使用して、C# 言語をターゲットとした新しいコンソール アプリケーションを作成します。

    ```console
    dotnet new console -lang C# -n csharp-model-with-rest
    ```

1. 作成した `csharp-model-with-rest` ディレクトリに移動し、次のコマンドを使用して必要な依存関係をインストールします。

    ```console
    cd csharp-model-with-rest
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```

1. Program.cs を次のコードで上書きします。

    [!code-csharp[Code snippet](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/csharp-model-with-rest/Program.cs)]

1. `YOUR-` で始まる値を実際の値に置き換えます。

    |Information|目的|
    |--|--|
    |`YOUR-APP-ID`| LUIS アプリ ID。 |
    |`YOUR-AUTHORING-KEY`|32 文字の実際の作成キー。|
    |`YOUR-AUTHORING-ENDPOINT`| 作成 URL エンドポイント。 たとえば、「 `https://replace-with-your-resource-name.api.cognitive.microsoft.com/` 」のように入力します。 リソース名は、リソースの作成時に設定します。|

    割り当てられたキーとリソースは、LUIS ポータルの [Manage]\(管理\) セクションの **[Azure リソース]** ページで確認できます。 アプリ ID は、同じ [Manage]\(管理\) セクションの **[アプリケーションの設定]** ページで入手できます。

1. コンソール アプリケーションをビルドします。

    ```console
    dotnet build
    ```

1. コンソール アプリケーションを実行します。

    ```console
    dotnet run
    ```

1. 作成の応答を確認します。

    ```console
    Added utterances.
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
    Sent training request.
    {
        "statusId": 9,
        "status": "Queued"
    }
    Requested training status.
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
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートを完了したら、ファイル システムからプロジェクト フォルダーを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリのベスト プラクティス](../luis-concept-best-practices.md)
