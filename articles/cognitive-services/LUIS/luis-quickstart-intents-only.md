---
title: 'チュートリアル 1: カスタム LUIS アプリで意図を検出する'
titleSuffix: Azure Cognitive Services
description: ユーザーの意図を予測するカスタム アプリを作成します。 このアプリは、メール アドレスや日付などの発話テキストからさまざまなデータ要素を抽出しないため、最も単純な種類の LUIS アプリです。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 30c9f572d77caacbeecf5f15d74fd8517e9fa883
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426861"
---
# <a name="tutorial-1-build-custom-app-to-determine-user-intentions"></a>チュートリアル 1: ユーザーの意図を特定するカスタム アプリを構築する

このチュートリアルでは、発話 (テキスト) に基づいてユーザーの意図を予測する人事 (HR) カスタム アプリを作成します。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。

アプリの目的では、会話や自然言語テキストの意図を特定することです。 これらの意図 (intention) は、**意図**(Intent) に分類されます。 このアプリにはいくつかの意図があります。 最初の意図 **`GetJobInformation`** は、ユーザーが社内で有効な仕事に関する情報を必要としているタイミングを識別します。 2 番目の意図 **`None`** は、このアプリの_領域_ (範囲) 外にいるユーザーからのすべての発話に対して使用されます。 3 番目の意図 **`ApplyForJob`** は後で、仕事への応募に関するすべての発話に対して追加されます。 この 3 番目の意図は、誰かが仕事に応募するときには仕事の情報は既に知られているはずであるため、`GetJobInformation` とは異なります。 ただし、単語の選択によっては、どちらも仕事に関することであるため、意図の特定が難しい場合があります。

LUIS は、JSON 応答を返した後は、この要求の処理を終えています。 LUIS は、ユーザーの発話に対する回答は提供しません。自然言語で、どのような種類の情報が質問されているかを識別するだけです。 

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * 新しいアプリの作成 
> * 意図の作成
> * 発話の例を追加する
> * アプリのトレーニング
> * アプリの発行
> * エンドポイントからの意図の取得

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>新しいアプリの作成

1. URL が [https://www.luis.ai](https://www.luis.ai) の LUIS ポータルにサインインします。 

2. **[Create new app]\(新しいアプリの作成\)** を選択します。  

    [![](media/luis-quickstart-intents-only/app-list.png "Language Understanding (LUIS) の [My Apps]\(マイ アプリ) ページのスクリーンショット")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. ポップアップ ダイアログ ボックスで、名前として「`HumanResources`」と入力し、既定のカルチャである **[English]\(英語)** はそのままにします。 説明は空のままにします。

    ![LUIS の新規アプリ](./media/luis-quickstart-intents-only/create-app.png)

    次に、アプリの **[Intents]\(意図\)** ページに **None** 意図が表示されます。

## <a name="getjobinformation-intent"></a>GetJobInformation 意図

1. **[Create new intent]\(意図の新規作成\)** を選択します。 新しい意図の名前として「`GetJobInformation`」と入力します。 この意図は、ユーザーが社内で空きのある仕事に関する情報を欲したすべてのタイミングで予測されます。

    ![](media/luis-quickstart-intents-only/create-intent.png "Language Understanding (LUIS) の [New intent]\(新しい意図) ダイアログ ボックスのスクリーンショット")

2. _発話例_ を提供することで、この意図についてはどのような種類の発話を予測する必要があるか、LUIS のトレーニングを行っています。 この意図に、ユーザーの質問として予想される発話例をいくつか追加します。次はその例です。

    | 発話の例|
    |--|
    |今日新しく登録された仕事はありますか。|
    |シニア エンジニアではどのようなポストがありますか。|
    |データベースの仕事はありますか。|
    |経理関連の新しい職を探しています|
    |仕事の一覧はどこにありますか。|
    |新しい仕事はありますか。|
    |シアトル オフィスに新しいポストはありますか。|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "MyStore 意図用の新しい発話を入力しているスクリーンショット")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    


## <a name="none-intent"></a>None 意図 
クライアント アプリケーションは、その発話が、アプリケーションの主題の領域外にあるかどうかを知る必要があります。 LUIS が発話に対して **None** 意図を返した場合、クライアント アプリケーションは、ユーザーが会話の終了を望んでいるかどうかを確認することができます。 ユーザーが終了を望んでいない場合、クライアント アプリケーションは会話を続行するよう追加の指示を出すこともできます。 

主題の領域外にあるこれらの発話例は、**None** 意図にグループ化します。 空白のままにしないでください。 

1. 左側のパネルから **[Intents]\(意図\)** を選びます。

2. **None** 意図を選択します。 ユーザーが入力する可能性があっても、この人事アプリには関係のない 3 つの発話を追加します。 求人に関するアプリの場合に、**None** 意図となるものをいくつか示します。

    | 発話の例|
    |--|
    |吠える犬はうっとうしい|
    |ピザを注文して|
    |海の中のペンギン|


## <a name="train"></a>トレーニング 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>[発行]

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent"></a>意図の取得

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレス バーの URL の末尾に移動し、「`I'm looking for a job with Natural Language Processing`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話の**クエリ**です。 この発話は、どの発話例とも同じではありません。 これは適切なテストであり、最上位のスコアリング意図として `GetJobInformation` 意図を返すはずです。 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

    結果には、このアプリ内の**すべての意図**が含まれています (現在は 2 つ)。 現在このアプリにはまったくエンティティがないため、エンティティの配列は空になっています。 

    JSON の結果では、最上位のスコアリング意図が **`topScoringIntent`** プロパティとして識別されています。 すべてのスコアは 0 から 1 の範囲であり、1 に近いほどよいスコアです。 

## <a name="applyforjob-intent"></a>ApplyForJob 意図
LUIS の Web サイトに戻り、ユーザーの発話が、仕事への応募に関するものかどうかを特定する新しい意図を作成します。

1. 右上のメニューから **[Build]\(ビルド\)** を選択し、アプリのビルドに戻ります。

2. 左側のメニューから **[Intents]\(意図\)** を選びます。

3. **[Create new intent]\(意図の新規作成\)** を選択して `ApplyForJob` という名前を入力します。 

    ![新しい意図を作成するための LUIS ダイアログ](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. ユーザーの質問として予想される複数の発話をこの意図に追加します。次はその例です。

    | 発話の例|
    |--|
    |新しい経理の仕事に応募したい|
    |ジョブ 123456 の申込書に記入して|
    |エンジニアのポストにレジュメを送信して|
    |ポスト 654234 向けの職務経歴書|
    |ジョブ 567890 の書類|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "ApplyForJob 意図用の新しい発話を入力しているスクリーンショット")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    現時点ではその意図が正しいことを LUIS が判断できないため、ラベル付きの意図は赤で囲まれています。 アプリをトレーニングすることで、LUIS にその発話が正しい意図であることを指示します。 

## <a name="train-again"></a>再度のトレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>再度の発行

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-again"></a>再度の意図の取得

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. ブラウザーの新しいウィンドウで、URL の最後に `Can I submit my resume for job 235986` と入力します。 

    ```JSON
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

    結果には、新しい意図 **ApplyForJob** と既存の意図が含まれています。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、人事 (HR) アプリを作成し、2 つの意図を作成して、各意図に発話例を追加しました。また、None 意図への発話例の追加、トレーニング、発行、およびエンドポイントのテストを行いました。 これらが、LUIS モデルを構築する基本的な手順です。 

> [!div class="nextstepaction"]
> [このアプリに事前構築済みの意図とエンティティを追加する](luis-tutorial-prebuilt-intents-entities.md)
