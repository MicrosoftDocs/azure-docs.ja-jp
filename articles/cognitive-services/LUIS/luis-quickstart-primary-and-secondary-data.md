---
title: 'チュートリアル 7: LUIS での Simple エンティティとフレーズ リスト'
titleSuffix: Azure Cognitive Services
description: 発話から機械学習データを抽出する
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: f3e931344d2d2294c03756d630c688df1e5da9a8
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425256"
---
# <a name="tutorial-7-extract-names-with-simple-entity-and-phrase-list"></a>チュートリアル 7: Simple エンティティとフレーズ リストを使用して名前を抽出する

このチュートリアルでは、**Simple** エンティティを使用して発話から雇用ジョブ名の機械学習データを抽出します。 抽出精度を向上させるために、Simple エンティティに固有の用語のフレーズ リストを追加します。

このチュートリアルでは、ジョブ名を抽出する新しい Simple エンティティを追加します。 この LUIS アプリのシンプル エンティティの目的は、ジョブ名がどのようなもので、発話のどこで見つけられるかを、LUIS に教えることです。 発話のジョブ名部分は、単語の選択や発話の長さに基づいて、発話ごとに異なります。 LUIS には、ジョブ名を使用するすべての意図にわたってジョブ名の例が必要です。  

Simple エンティティがこのデータの種類に適しているのは次の場合です。

* データが単一の概念である。
* データが正規表現のような整形式ではない。
* 電話番号またはデータの事前構築済みエンティティのような一般的なデータではない。
* リスト エンティティなどの既知の単語のリストにデータが正確に一致しない。
* 複合エンティティや階層構造エンティティのような他のデータ項目がデータに含まれていない。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * 既存のチュートリアル アプリを使用する
> * Simple エンティティを追加してアプリから職務を抽出する
> * フレーズ リストを追加してジョブの単語のシグナルを強化する
> * トレーニング 
> * [発行] 
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>既存のアプリを使用する

最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

以前のチュートリアルの HumanResources アプリがない場合は、次の手順を使用します。

1.  [アプリの JSON ファイル](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-composite-HumanResources.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `simple` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

## <a name="simple-entity"></a>シンプル エンティティ
Simple エンティティは、単語または語句に含まれている単一のデータ概念を検出します。

チャット ボットからの次の発話について考えます。

|発話|抽出できるジョブの名前|
|:--|:--|
|新しい経理の仕事への応募を希望しています。|経理|
|Please submit my resume for the engineering position. (技術系のポストに私の履歴書を提出してください。)|engineering (技術系)|
|ジョブ 123456 の申込書に記入してください|123456|

ジョブ名は名詞、動詞、または複数の単語で構成されるフレーズであるため、判断するのが困難です。 例: 

|[ジョブ]|
|--|
|エンジニア|
|ソフトウェア エンジニア|
|シニア ソフトウェア エンジニア|
|エンジニアリング チーム リーダー |
|航空管制官|
|車両運転者|
|救急車運転手|
|監督|
|成型工|
|機械工|

この LUIS アプリには、複数の意図にジョブ名があります。 すべての意図の発話で、これらの単語にラベルを付けることで、ジョブ自体と、そのジョブ名が発話のどこに出現するかを LUIS に認識させることができます。

発話例でエンティティがマークされたら、フレーズ リストを追加して、Simple エンティティのシグナルを強化することが重要です。 フレーズ リストは正確な一致として使用**されず**、期待するすべての可能な値である必要はありません。 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **[Intents]\(意図\)** ページで、意図 **ApplyForJob** を選択します。 

3. 発話 `I want to apply for the new accounting job` で、`accounting` を選択し、ポップアップ メニューの一番上のフィールドに「`Job`」と入力して、**[新しいエンティティの作成]** を選択します。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "エンティティの作成手順が強調表示されている \"ApplyForJob\" 意図を含む LUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. ポップアップ ウィンドウでエンティティ名と型を確認し、**[Done]\(完了\)**  を選択します。

    ![名前に "Job"、型に "Simple" が指定された、Simple エンティティの作成ポップアップ モデル ダイアログ](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. 発話 `Submit resume for engineering position` の中の `engineering` という単語にジョブのエンティティとしてラベルを付けます。 `engineering` という単語を選択し、ポップアップ メニューから **[ジョブ]** を選択します。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Job エンティティのラベル付けが強調表示されている LUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    すべての発話にラベルを付けますが、5  件の発話では、職務関連の単語やフレーズについて LUIS に認識させるには不十分です。 数値が使用されている職務には、例は不要です。正規表現エンティティが使用されているためです。 単語または語句のジョブには少なくともさらに 15 例が必要です。 

6. 発話を追加し、職務に関する単語またはフレーズを **Job** エンティティとしてマークします。 ジョブの種類は、雇用サービスの雇用全体にわたって一般的なものです。 特定の業界に関連するジョブが必要な場合は、それをジョブの単語に反映させる必要があります。 

    |発話|Job エンティティ|
    |:--|:--|
    |研究開発のプログラム マネージャー デスクに応募します|プログラム マネージャー|
    |Here is my line cook application. (調理補助への応募書類です。)|調理補助|
    |キャンプ指導員に申し込むための履歴書を添付しました。|キャンプ指導員|
    |職務経歴書です。 管理アシスタントに応募します。|管理アシスタント|
    |販売管理の仕事への応募を希望しています。|管理、販売|
    |新しい経理の仕事に対する履歴書です。|経理|
    |バーテンダー アシスタントへの応募書類を添付しました。|バーテンダー アシスタント|
    |I'm submitting my application for roofer and framer. (屋根職人、組立工への応募書類を提出します。)|屋根職人、フレーマー|
    |こちらの履歴書をご確認ください。 バス運転手に応募します。|バス運転手|
    |正看護師です。 履歴書をご確認ください。|正看護師|
    |新聞広告で拝見した教職への応募に必要な書類を提出いたします。|教職|
    |職務経歴書です。 果物/野菜の在庫管理のポストへの応募を希望します。|在庫管理|
    |タイル職人に応募します。|タイル|
    |造園技師に応募するための履歴書を添付します。|造園技師|
    |生物学教授への応募に必要な履歴書を同封します。|生物学教授|
    |写真撮影の職に応募します。|写真撮影|git 

## <a name="label-entity-in-example-utterances"></a>発話例での Label エンティティ

エンティティのラベル付けまたは _マーク付け_ は、発話例の中でエンティティが見つかった位置を LUIS に示します。

1. 左側のメニューから **[Intents]\(意図\)** を選びます。

2. 意図の一覧から **[GetJobInformation]** を選択します。 

3. 発話の例のジョブにラベルを付けます。

    |発話|Job エンティティ|
    |:--|:--|
    |データベースの仕事はありますか。|データベース|
    |経理関連の新しい職を探しています|経理|
    |シニア エンジニアではどのようなポストがありますか。|シニア エンジニア|

    発話の例は他にもありますが、ジョブの単語が含まれていません。

## <a name="train"></a>トレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>[発行]

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Here is my c.v. for the programmer job`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、`ApplyForJob` 発話が返される必要があります。

    ```JSON
    {
      "query": "Here is my c.v. for the programmer job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9826467
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9826467
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0218927357
        },
        {
          "intent": "MoveEmployee",
          "score": 0.007849265
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00349470088
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00348804821
        },
        {
          "intent": "None",
          "score": 0.00319909188
        },
        {
          "intent": "FindForm",
          "score": 0.00222647213
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00211193133
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00172086991
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00138010911
        }
      ],
      "entities": [
        {
          "entity": "programmer",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 33,
          "score": 0.5230502
        }
      ]
    }
    ```
    
    LUIS は正しい意図 **ApplyForJob** を見つけ、正しいエンティティ **Job** を `programmer` の値と共に抽出しました。


## <a name="names-are-tricky"></a>名前には注意が必要
LUIS アプリによって、確実性の高い適切な意図が検出され、ジョブ名が抽出されました。ただし、名前には注意が必要です。 発話 `This is the lead welder paperwork` を見てみましょう。  

次の JSON では、LUIS は正しい意図 `ApplyForJob` で応答していますが、`lead welder` はジョブ名として抽出されませんでした。 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

名前は何でもかまわないため、シグナルを強化する単語のフレーズ リストが LUIS にある場合、LUIS はエンティティをより正確に予測します。

## <a name="to-boost-signal-add-phrase-list"></a>シグナルを強化するためにフレーズ リストを追加する

LUIS-Samples の GitHub リポジトリから [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) ファイルを開きます。 リストに含まれるジョブの単語およびフレーズは 1,000 を超えます。 自分にとって意味のあるジョブの単語がリストにあるか確認します。 単語またはフレーズがリストにない場合は、自分で追加します。

1. LUIS アプリの **[ビルド]** セクションで、**[アプリのパフォーマンス向上]** メニューの **[フレーズ リスト]** を選択します。

2. **[Create new phrase list]\(新しいフレーズ リストの作成\)** を選択します。 

3. 新しいフレーズ リスト `Job` に名前を付けて、そのリストを jobs-phrase-list.csv から **[値]** テキスト ボックスにコピーします。 [Enter] を選択します。 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "新しいフレーズ リストの作成ダイアログ ポップアップのスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    フレーズ リストに単語をさらに追加したい場合は、**[Related Values]\(関連する値\)** を確認し、関連のある単語を追加します。 

4. **[保存]** を選択して、フレーズ リストをアクティブにします。

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "フレーズ リストの値ボックスに単語が入力されている、新しいフレーズ リストの作成ダイアログ ポップアップのスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. フレーズ リストが使用されるように、アプリを再度[トレーニング](#train)して[発行](#publish)します。

6. 同じ発話 `This is the lead welder paperwork.` を含むエンドポイントにクエリを再実行します。

    JSON 応答には、抽出された次のエンティティが含まれています。

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Human Resources アプリで、機械学習された Simple エンティティを使用して発話からジョブ名を見つけます。 ジョブ名はさまざまな単語または語句である可能性があるため、ジョブ名の語を強化するためのフレーズ リストがアプリに必要でした。 

> [!div class="nextstepaction"]
> [事前構築済みの KeyPhrase エンティティを追加する](luis-quickstart-intent-and-key-phrase.md)
