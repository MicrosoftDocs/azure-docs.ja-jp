---
title: センチメント分析
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、ポジティブ、ネガティブ、およびニュートラルな感情を発話から取得する方法を示すアプリを作成します。 感情は発話全体から決定されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 0e61f6a914c33842f4f42b2e1e4206b370a11dd4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099037"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>チュートリアル:発話の感情を取得する

このチュートリアルでは、ポジティブ、ネガティブ、およびニュートラルな感情を発話から判定する方法を示すアプリを作成します。 感情は発話全体から決定されます。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * 新しいアプリの作成
> * 感情分析を発行設定として追加する
> * アプリのトレーニング
> * アプリの発行
> * 発話の感情をエンドポイントから取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>感情分析は発行設定

以下の発話は、感情の例を示しています。

|センチメント|Score|発話|
|:--|:--|:--|
|ポジティブ|0.91 |John W. Smith はパリのプレゼンテーションで大活躍だった。|
|ポジティブ|0.84 |シアトルのエンジニアは Parker への売り込みですばらしい働きをした。|

感情分析は、どの発話にも適用される発行設定です。 発話の中に感情を示す単語を見つけてマークする必要はありません。 

それは発行設定であるため、意図またはエンティティのページでは確認できません。 [対話型テスト](luis-interactive-test.md#view-sentiment-results)のウィンドウで、またはエンドポイント URL のテスト時に確認できます。 


## <a name="create-a-new-app"></a>新しいアプリの作成

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>事前構築済みエンティティ PersonName を追加する 


1. 左側のナビゲーション メニューの **[Entities]\(エンティティ\)** を選択します。

1. **[Add prebuilt entity]\(作成済みエンティティの追加\)** ボタンを選択します。

1. 事前構築済みエンティティの一覧から 次のエンティティを選択し、**[完了]** を選択します。

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![[number] が選択されている事前構築済みエンティティ ダイアログのスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>意図を作成して従業員のフィードバックを判定する

新しい意図を追加して、会社のメンバーから従業員のフィードバックをキャプチャします。 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **[Create new intent]\(意図の新規作成\)** を選択します。

3. 新しい意図の名前として「`EmployeeFeedback`」と入力します。

    ![名前として EmployeeFeedback が指定された [Create new intent]\(意図の新規作成\) ダイアログ ボックス](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. 良い仕事をした従業員や改善が必要な領域を示す発話をいくつか追加します。

    |発話|
    |--|
    |John Smith は産休から復帰した同僚がスムーズに仕事に復帰できるよう協力しました。|
    |Jill Jones は、悲しみにくれている同僚を上手く慰めました。|
    |Bob Barnes は、事務処理に必要な請求書の一部を用意できていませんでした。|
    |Todd Thomas は、必要なフォームの提出が 1 か月遅れたうえ、そのフォームには署名がありませんでした。|
    |Katherine Kelly は、重要なマーケティング オフサイト ミーティングに参加しませんでした。|
    |Denise Dillard は、6 月レビュー会議に出席しませんでした。|
    |Mark Mathews はハーバードでの売り込みを上手くこなしました。|
    |Walter Williams はスタンフォードのプレゼンテーションで大活躍でした。|

    [![EmployeeFeedback 意図の発話の例が示された LUIS アプリのスクリーンショット](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>意図への変更をテストできるようにアプリをトレーニングする 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>感情分析を含むアプリを構成する

1. 右上のナビゲーションで **[管理]** を選択し、左側のメニューから **[Publish settings]\(発行設定\)** を選択します。

1. **[感情分析]** を選択して、この設定を有効にします。 

    ![感情分析を発行設定として有効にする](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>トレーニング済みのモデルがエンドポイントからクエリ可能になるようにアプリを発行する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>エンドポイントから発話の感情を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. アドレスの URL の末尾に移動し、「`Jill Jones work with the media team on the public portal was amazing`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、感情分析が抽出された `EmployeeFeedback` 意図を返す必要があります。
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    sentimentAnalysis はポジティブで、スコアは 86% です。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>関連情報

* 感情分析は、Cognitive Service の [Text Analytics](../Text-Analytics/index.yml) によって提供されます。 この機能は、Text Analytics の[サポートされる言語](luis-language-support.md##languages-supported)に制限されています。
* 「[How to train (トレーニング方法)](luis-how-to-train.md)」
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)


## <a name="next-steps"></a>次の手順
このチュートリアルでは、感情分析を発行設定として追加し、発話全体から感情値を抽出します。

> [!div class="nextstepaction"] 
> [人事アプリでエンドポイントの発話をレビューする](luis-tutorial-review-endpoint-utterances.md) 

