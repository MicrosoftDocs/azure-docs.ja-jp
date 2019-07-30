---
title: 事前構築済みの意図とエンティティを追加する
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、事前構築済みの意図とエンティティをアプリに追加して、意図の予測の取得とデータの抽出を迅速化します。 発話に事前構築済みエンティティのラベルを付ける必要はありません。 このエンティティは自動的に検出されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2018
ms.author: diberry
ms.openlocfilehash: 189514c51451dedfaab7b07f28d79f674b32f83a
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277546"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>チュートリアル:一般的な意図とエンティティを識別する

このチュートリアルでは、事前構築済みの意図とエンティティを Human Resources チュートリアル アプリに追加して、意図の予測の取得とデータの抽出を迅速化します。 エンティティは自動的に検出されるため、事前構築済みエンティティがある発話にはマークを付ける必要がありません。

事前構築済みモデル (ドメイン、意図、およびエンティティ) を使用すると、モデルをすばやく構築できます。

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * 新しいアプリを作成する
> * 事前構築済み意図を追加する 
> * 事前構築済みエンティティを追加する 
> * トレーニング 
> * 発行 
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>新しいアプリの作成

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>ユーザーの一般的な意図を使用する際に役立つ事前構築済み意図を追加する

LUIS には、ユーザーの一般的な意図を使用する際に役立つ事前構築済み意図が用意されています。  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **[Add prebuilt domain intent]\(ドメインの事前構築済み意図の追加\)** を選択します。 

1. `Utilities` を検索します。 

    [![検索ボックスに「Utilities」と入力された事前構築済み意図ダイアログのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. 次の意図を選択し、 **[完了]** をクリックします。 

   * Utilities.Cancel
   * Utilities.Confirm
   * Utilities.Help
   * Utilities.StartOver
   * Utilities.Stop

     これらの意図は、ユーザーがいる場所やユーザーがしてほしいことを会話の中で判定するのに役立ちます。 これらの意図には、エンティティの使用状況が含まれています。 number、ordinal、および Utilities.DirectionalReference のエンティティは、LUIS アプリに自動的に追加されます。 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>一般的な種類のデータを抽出するのに役立つ事前構築済みエンティティを追加する

LUIS には、一般的なデータ抽出のための事前構築済みエンティティが用意されています。 

1. 左側のナビゲーション メニューの **[Entities]\(エンティティ\)** を選択します。

1. **[Add prebuilt entity]\(作成済みエンティティの追加\)** ボタンを選択します。

1. 事前構築済みエンティティの一覧から 次のエンティティを選択し、 **[完了]** を選択します。

   * **[PersonName](luis-reference-prebuilt-person.md)** 
   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     ![[number] が選択されている事前構築済みエンティティ ダイアログのスクリーンショット](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

     これらのエンティティを使用すると、クライアント アプリケーションに名前および場所の認識を追加できます。

## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>意図への変更をテストできるようにアプリをトレーニングする 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>トレーニング済みのモデルがエンドポイントからクエリ可能になるようにアプリを発行する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. ブラウザーのアドレス バーで URL の末尾に移動して「`I want to cancel my trip to Seattle to see Bob Smith`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話の**クエリ**です。 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    結果として、Utilities.Cancel 意図が 80% の信頼度で予測され、市区町村とユーザー名のデータが抽出されました。 


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>関連情報

事前構築済みモデルの詳細については、次をご覧ください。

* 「[Prebuilt domains (事前構築済みドメイン)](luis-reference-prebuilt-domains.md)」: これらは、LUIS アプリの作成を全体的に削減する一般的なドメインです。
* 「Prebuilt intents (事前構築済み意図)」: これは、一般的なドメインの個々の意図です。 ドメイン全体を追加するのではなく、個々に意図を追加できます。
* 「[事前構築済みエンティティ (Prebuilt entities)](luis-prebuilt-entities.md)」: これらは、大部分の LUIS アプリで役立つ一般的な種類のデータです。

LUIS アプリ の使用法の詳細については、以下をご覧ください。

* 「[How to train (トレーニング方法)](luis-how-to-train.md)」
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)

## <a name="next-steps"></a>次の手順

事前構築済みの意図とエンティティを追加することにより、クライアント アプリケーションで一般的なユーザーの意図を判断したり、一般的なデータ型を抽出したりできます。  

> [!div class="nextstepaction"]
> [正規表現エンティティを追加する](luis-quickstart-intents-regex-entity.md)

