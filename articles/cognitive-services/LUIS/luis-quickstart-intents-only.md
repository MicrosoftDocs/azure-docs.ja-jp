---
title: 意図の予測
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、ユーザーの意図を予測するカスタム アプリを作成します。 このアプリは、メール アドレスや日付などの発話テキストからさまざまなデータ要素を抽出しないため、最も単純な種類の LUIS アプリです。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 534aa678df69a4ef71296005922fa59bbe7bbcfa
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277573"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>チュートリアル:ユーザーの意図を特定する LUIS アプリを構築する

このチュートリアルでは、発話 (テキスト) に基づいてユーザーの意図を予測する人事 (HR) カスタム アプリを作成します。 

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * 新しいアプリの作成 
> * 意図の作成
> * 発話の例を追加する
> * アプリのトレーニング
> * アプリの発行
> * エンドポイントからの意図の取得


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>意図としてのユーザーの意図

アプリの目的は、会話や自然言語テキストの意図を特定することです。 

`Are there any new positions in the Seattle office?`

これらの意図 (intention) は、**意図**(Intent) に分類されます。 

このアプリにはいくつかの意図があります。 

|意図|目的|
|--|--|
|ApplyForJob|ユーザーが仕事に応募するかどうかを特定します。|
|GetJobInformation|ユーザーが一般的な仕事または特定の仕事に関する情報を探しているかどうかを特定します。|
|なし|アプリが回答することが想定されていないことをユーザーが質問するかどうかを特定します。 この意図はアプリの作成の一部として指定され (指定した場合)、削除することはできません。 |

## <a name="create-a-new-app"></a>新しいアプリの作成

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>ジョブ情報の意図を作成する

1. **[Create new intent]\(意図の新規作成\)** を選択します。 新しい意図の名前として「`GetJobInformation`」と入力します。 この意図は、ユーザーが社内で空きのある仕事に関する情報を欲したときに予測されます。 

    ![Language Understanding (LUIS) の新しい意図ダイアログのスクリーンショット](media/luis-quickstart-intents-only/create-intent.png "Language Understanding (LUIS) の新しい意図ダイアログのスクリーンショット")

1. **[完了]** を選択します。

2. この意図に、ユーザーの質問として予想される発話例をいくつか追加します。

    | 発話の例|
    |--|
    |今日新しく登録された仕事はありますか。|
    |シアトル オフィスに新しいポストはありますか。|
    |エンジニアとしてリモート ワーカーまたは在宅勤務の空きのある仕事はありますか。|
    |データベースの仕事はありますか。|
    |タンパ オフィスにコワーキングの職の空きがないか探しています。|
    |サンフランシスコ オフィスでインターンシップ募集をしていませんか。|
    |大学でのパートタイムの職はありませんか。|
    |経理関連の新しい職を探しています|
    |ニューヨーク市での 2 か国語を話す必要がある職を探しています。|
    |経理関連の新しい職を探しています。|
    |新しい仕事はありますか。|
    |過去 2 日間に追加されたエンジニアのすべての職を教えてください。|
    |今日の新しい求人を教えてください。|
    |ロンドン オフィスで空きのある会計職にはどのようなものがありますか。|
    |シニア エンジニアではどのようなポストがありますか。|
    |仕事の一覧はどこにありますか。|

    [![MyStore の意図の新しい発話を入力しているスクリーンショット](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "MyStore の意図の新しい発話を入力しているスクリーンショット")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    "_発話例_" を提供することで、この意図についてはどのような種類の発話を予測する必要があるか、LUIS のトレーニングを行っています。 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>テストまたは発行する前に、アプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>アプリを発行してエンドポイントからクエリを実行する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>エンドポイントから意図の予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. アドレス バーの URL の末尾に移動し、「`I'm looking for a job with Natural Language Processing`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話の**クエリ**です。 この発話は、どの発話例とも同じではありません。 これは適切なテストであり、最上位のスコアリング意図として `GetJobInformation` 意図を返すはずです。 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    `verbose=true` querystring パラメーターでは、アプリのクエリの結果に**すべての意図**が含まれます。 現在このアプリにはまったくエンティティがないため、エンティティの配列は空になっています。 

    JSON の結果では、最上位のスコアリング意図が **`topScoringIntent`** プロパティとして識別されています。 すべてのスコアは 0 から 1 の範囲であり、1 に近いほどよいスコアです。 

## <a name="create-intent-for-job-applications"></a>ジョブ応募の意図を作成する

LUIS ポータルに戻り、ユーザーの発話が、仕事への応募に関するものかどうかを特定する新しい意図を作成します。

1. 右上のメニューから **[Build]\(ビルド\)** を選択し、アプリのビルドに戻ります。

1. 左側のメニューから**意図**を選択して、意図の一覧を表示します。

1. **[Create new intent]\(意図の新規作成\)** を選択して `ApplyForJob` という名前を入力します。 

    ![新しい意図を作成するための LUIS ダイアログ](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. ユーザーの質問として予想される複数の発話をこの意図に追加します。次はその例です。

    | 発話の例|
    |--|
    |ジョブ 123456 の申込書に記入して|
    |ポスト 654234 向けの職務経歴書|
    |パートタイム受付の求人への私の履歴書です。|
    |アート関連の事務処理の職に応募します。|
    |サンディエゴの研究/開発部門のサマー カレッジのインターンシップに応募します|
    |カフェテリアの臨時の職に履歴書を提出します。|
    |オハイオ州コロンバスでの新しい自動車のチームに履歴書を提出します|
    |新しい経理の仕事に応募したい|
    |ジョブ 456789 の経理職のインターンシップの情報はこちら|
    |ジョブ 567890 の書類|
    |タルサでの経理職のインターンシップの書類を添付しました。|
    |ホリデー配送職の書類|
    |シアトルでの新しい経理職に私の履歴書を送信してください。|
    |エンジニアのポストにレジュメを送信して|
    |職務経歴書です。 求人 234123 への履歴書です。|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

## <a name="train-again"></a>再度のトレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>再度の発行

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>意図の予測を再取得

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. ブラウザーの新しいウィンドウで、URL の最後に `Can I submit my resume for job 235986` と入力します。 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    結果には、新しい意図 **ApplyForJob** と既存の意図が含まれています。 

## <a name="client-application-next-steps"></a>クライアント アプリケーションの次の手順

LUIS は、JSON 応答を返した後は、この要求の処理を終えています。 LUIS は、ユーザーの発話に対する回答は提供しません。自然言語で、どのような種類の情報が質問されているかを識別するだけです。 会話のフォローアップは、Azure ボットなどのクライアント アプリケーションによって提供されます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>関連情報

* [エンティティの種類](luis-concept-entity-types.md)
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [How to test in LUIS portal (LUIS ポータルでのテスト方法)](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、人事 (HR) アプリを作成し、2 つの意図を作成して、各意図に発話例を追加しました。また、None 意図への発話例の追加、トレーニング、発行、およびエンドポイントのテストを行いました。 これらが、LUIS モデルを構築する基本的な手順です。 

このアプリで続行して、[シンプルなエンティティとフレーズ リストを追加](luis-quickstart-primary-and-secondary-data.md)します。

> [!div class="nextstepaction"]
> [このアプリに事前構築済みの意図とエンティティを追加する](luis-tutorial-prebuilt-intents-entities.md)
