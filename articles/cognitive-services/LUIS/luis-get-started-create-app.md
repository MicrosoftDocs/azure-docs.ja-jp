---
title: 'クイックスタート: アプリの作成 - LUIS'
titleSuffix: Azure Cognitive Services
description: 照明やアプライアンスの電源をオンにしたりオフにしたりする用途を想定し、事前構築済みのドメイン `HomeAutomation` を使用した LUIS アプリを作成します。 この事前構築済みのドメインによって、意図、エンティティ、発話例が得られます。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 1704b62cae6375d376fc43fb7a2940cd9c717072
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382516"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>クイック スタート: 事前構築済みの Home Automation アプリを使用する

このクイック スタートでは、照明やアプライアンスの電源をオンにしたりオフにしたりする用途を想定し、事前構築済みのドメイン `HomeAutomation` を使用した LUIS アプリを作成します。 この事前構築済みのドメインによって、意図、エンティティ、発話例が得られます。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。

## <a name="prerequisites"></a>前提条件

この記事には、LUIS ポータル ([https://www.luis.ai](https://www.luis.ai)) で作成された無料の LUIS アカウントが必要です。 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>新しいアプリの作成
アプリケーションは、 **[マイ アプリ]** で作成および管理できます。 

2. **[Create new app]\(新しいアプリの作成\)** を選択します。

    [![アプリの一覧のスクリーン ショット](media/luis-quickstart-new-app/app-list.png "アプリの一覧のスクリーン ショット")](media/luis-quickstart-new-app/app-list.png)

3. ダイアログ ボックスで、アプリケーションに "Home Automation" という名前を付けます。

    [![新しいアプリの作成 ポップアップ ダイアログのスクリーンショット](media/luis-quickstart-new-app/create-new-app-dialog.png "新しいアプリの作成 ポップアップ ダイアログのスクリーンショット")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. アプリケーションのカルチャを選択します。 この Home Automation アプリでは、英語を選択します。 **[完了]** を選択します。 LUIS により Home Automation アプリが作成されます。 

    >[!NOTE]
    >カルチャは、アプリケーションを作成した後に変更できません。 

## <a name="add-prebuilt-domain"></a>事前構築済みのドメインの追加

左側のナビゲーション ウィンドウで **[Prebuilt domains]\(事前構築済みドメイン\)** を選択します。 次に、"Home" を検索します。 **[Add domain]\(ドメインの追加\)** を選択します。

[![事前構築済みドメイン メニューで呼び出された Home Automation ドメインのスクリーンショット](media/luis-quickstart-new-app/home-automation.png "事前構築済のドメイン メニューで呼び出された Home Automation ドメインのスクリーンショット")](media/luis-quickstart-new-app/home-automation.png)

ドメインが正常に追加されると、事前構築済みドメインのボックスに、 **[Remove domain]\(ドメインの削除\)** ボタンが表示されます。

[![削除 ボタンがある Home Automation ドメインのスクリーンショット](media/luis-quickstart-new-app/remove-domain.png "削除 ボタンがある Home Automation ドメインのスクリーンショット")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>意図とエンティティ

左側のナビゲーション ウィンドウで **[Intents]\(意図\)** を選択し、HomeAutomation ドメインの意図を確認します。 それぞれの意図には、サンプル発話が存在します。

![HomeAutomation の意図の一覧のスクリーンショット](media/luis-quickstart-new-app/home-automation-intents.png "HomeAutomation の意図の一覧のスクリーンショット")]

> [!NOTE]
> **[None]\(なし\)** は、すべての LUIS アプリに用意されている意図です。 これは自分のアプリの機能に対応しない発話を処理する目的で使用されます。 

**[HomeAutomation.TurnOff]** 意図を選択します。 意図には、エンティティでラベル付けされている発話の一覧が含まれていることがわかります。

[![意図のスクリーンショット](media/luis-quickstart-new-app/home-automation-turnoff.png "Screenshot of HomeAutomation.TurnOff intent")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>アプリをテストする
アプリのトレーニング後、そのテストを行うことができます。 上部のナビゲーションの **[Test]\(テスト\)** を選択します。 対話型のテスト ウィンドウにテスト発話 (「Turn off the lights」など) を入力し、Enter キーを押します。 

```
Turn off the lights
```

それぞれのテスト発話について、最もスコアの高い意図が、想定された意図と対応していることを確認します。

この例では、**HomeAutomation.TurnOff** に対する最もスコアの高い意図として `Turn off the lights` が正しく識別されています。

[![発話が強調表示されている テスト ウィンドウのスクリーンショット](media/luis-quickstart-new-app/test.png "発話が強調表示されている テスト ウィンドウのスクリーンショット")](media/luis-quickstart-new-app/test.png)


**[検査]** を選択して、その予測の詳細を確認します。

![発話が強調表示されたテスト パネルのスクリーンショット](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

もう一度 **[テスト]** を選択して、テスト ウィンドウを折りたたみます。 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>V2 API 予測エンドポイントに対してクエリを実行する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. アドレスの URL の末尾に移動し、「`turn off the living room light`」と入力して Enter キーを押します。 HTTP エンドポイントの、**V2 API** バージョンの JSON 応答がブラウザーに表示されます。

    ```json
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.9753089
      },
      "intents": [
        {
          "intent": "HomeAutomation.TurnOff",
          "score": 0.9753089
        },
        {
          "intent": "HomeAutomation.QueryState",
          "score": 0.01027893
        },
        {
          "intent": "HomeAutomation.TurnUp",
          "score": 0.006881481
        },
        {
          "intent": "HomeAutomation.SetDevice",
          "score": 0.006786365
        },
        {
          "intent": "HomeAutomation.TurnDown",
          "score": 0.005145787
        },
        {
          "intent": "HomeAutomation.TurnOn",
          "score": 0.004114749
        },
        {
          "intent": "None",
          "score": 0.000598924
        }
      ],
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Location",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.94558233
        },
        {
          "entity": "living room light",
          "type": "HomeAutomation.DeviceName",
          "startIndex": 13,
          "endIndex": 29,
          "resolution": {
            "values": [
              "living room light"
            ]
          }
        },
        {
          "entity": "light",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 25,
          "endIndex": 29,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
## <a name="query-the-v3-api-prediction-endpoint"></a>V3 API 予測エンドポイントに対してクエリを実行する

[V3 API クエリ](luis-migration-api-v3.md)の場合は、ブラウザーで GET メソッドの HTTPS 要求を変更します。山かっこ内の値は、実際の値に置き換えてください。 

**V2 URL と GET メソッド**:

https://\<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/\<appID>?verbose=true&subscription-key=\<YOUR_KEY>&**q=\<user-utterance-text>**

**V3 URL と GET メソッド**:

https://\<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/\<appID>/**slots**/**production**/**predict**?verbose=true&subscription-key=\<YOUR_KEY>&**query=\<user-utterance-text>**

HTTP エンドポイントの、**V3 API** バージョンの JSON 応答がブラウザーに表示されます。

```json
{
    "query": "turn off the lights",
    "prediction": {
        "normalizedQuery": "turn off the lights",
        "topIntent": "HomeAutomation.TurnOff",
        "intents": {
            "HomeAutomation.TurnOff": {
                "score": 0.99649024
            }
        },
        "entities": {
            "HomeAutomation.DeviceType": [
                [
                    "light"
                ]
            ],
            "$instance": {
                "HomeAutomation.DeviceType": [
                    {
                        "type": "HomeAutomation.DeviceType",
                        "text": "lights",
                        "startIndex": 13,
                        "length": 6,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

エンドポイントはコードから呼び出すことができます。

> [!div class="nextstepaction"]
> [コードを使って LUIS エンドポイントを呼び出す](luis-get-started-cs-get-intent.md)
