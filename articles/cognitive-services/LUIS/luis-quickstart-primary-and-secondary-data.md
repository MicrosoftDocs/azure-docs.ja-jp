---
title: シンプルなエンティティ、フレーズ リスト
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、Simple エンティティを使用して発話から雇用ジョブ名の機械学習データを抽出します。 抽出精度を向上させるために、Simple エンティティに固有の用語のフレーズ リストを追加します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: a03a1224451411617f38c8cecafbef008aa08916
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276202"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>チュートリアル:Simple エンティティとフレーズ リストを使用して名前を抽出する

このチュートリアルでは、**Simple** エンティティを使用して発話から雇用ジョブ名の機械学習データを抽出します。 抽出精度を向上させるために、Simple エンティティに固有の用語のフレーズ リストを追加します。

Simple エンティティは、単語または語句に含まれている単一のデータ概念を検出します。

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * サンプル アプリをインポートする
> * Simple エンティティを追加する 
> * フレーズ リストを追加して単語のシグナルを強化する
> * トレーニング 
> * 発行 
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>シンプル エンティティ

このチュートリアルでは、ジョブ名を抽出する新しい Simple エンティティを追加します。 この LUIS アプリのシンプル エンティティの目的は、ジョブ名がどのようなもので、発話のどこで見つけられるかを、LUIS に教えることです。 発話のジョブ名部分は、単語の選択や発話の長さに基づいて、発話ごとに異なります。 LUIS には、ジョブ名を使用するすべての意図にわたってジョブ名の例が必要です。  

Simple エンティティがこのデータの種類に適しているのは次の場合です。

* データが単一の概念である。
* データが正規表現のような整形式ではない。
* 電話番号またはデータの事前構築済みエンティティのような一般的なデータではない。
* リスト エンティティなどの既知の単語のリストにデータが正確に一致しない。
* 複合エンティティやコンテキスト ロールのような他のデータ項目がデータに含まれていない。

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

## <a name="import-example-app"></a>サンプル アプリをインポートする

1.  意図のチュートリアルから [app JSON ファイル](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `simple` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>意図の発話の例でエンティティにマークを付ける

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **[Intents]\(意図\)** ページで、意図 **ApplyForJob** を選択します。 

1. 発話 `I want to apply for the new accounting job` で、`accounting` を選択し、ポップアップ メニューの一番上のフィールドに「`Job`」と入力して、 **[新しいエンティティの作成]** を選択します。 

    [![エンティティの作成手順が強調表示されている 'ApplyForJob' の意図を示した LUIS のスクリーンショット](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "エンティティの作成手順が強調表示されている 'ApplyForJob' の意図を示した LUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. ポップアップ ウィンドウでエンティティ名と型を確認し、 **[Done]\(完了\)**  を選択します。

    ![名前に "Job"、型に "Simple" が指定された、Simple エンティティの作成ポップアップ モデル ダイアログ](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. 残りの発話で、ジョブに関連する単語を **Job** エンティティでマークします。これを行うには、単語またはフレーズを選択してから、ポップアップ メニューで **Job** を選択します。 

    [![ジョブ エンティティのラベル付けが強調表示されたLUIS のスクリーンショット](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "ジョブ エンティティのラベル付けが強調表示されたLUIS のスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>発話例をさらに追加してエンティティにマークを付ける

シンプル エンティティでは、予測精度を高めるために多くの例が必要です。 
 
1. 発話を追加し、職務に関する単語またはフレーズを **Job** エンティティとしてマークします。 

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
    |写真撮影の職に応募します。|写真撮影|

## <a name="mark-job-entity-in-other-intents"></a>他の意図のジョブ エンティティにマークを付ける

1. 左側のメニューから **[Intents]\(意図\)** を選びます。

1. 意図の一覧から **[GetJobInformation]** を選択します。 

1. 発話の例のジョブにラベルを付けます。

    発話の例が他の意図よりも多く含まれている意図の方が、予測精度が上である可能性が高くなります。 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>意図への変更をテストできるようにアプリをトレーニングする 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>トレーニング済みのモデルがエンドポイントからクエリ可能になるようにアプリを発行する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>エンドポイントから意図およびエンティティ予測を取得する 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Here is my c.v. for the engineering job`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされたどの発話とも同じではないので、よいテストであり、`ApplyForJob` 発話が返される必要があります。

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    LUIS は正しい意図 **ApplyForJob** を見つけ、正しいエンティティ **Job** を `engineering` の値と共に抽出しました。


## <a name="names-are-tricky"></a>名前には注意が必要
LUIS アプリによって、確実性の高い適切な意図が検出され、ジョブ名が抽出されました。ただし、名前には注意が必要です。 発話 `This is the lead welder paperwork` を見てみましょう。  

次の JSON では、LUIS は正しい意図 `ApplyForJob` で応答していますが、`lead welder` はジョブ名として抽出されませんでした。 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

名前は何でもかまわないため、シグナルを強化する単語のフレーズ リストが LUIS にある場合、LUIS はエンティティをより正確に予測します。

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>ジョブに関連する単語のシグナルを強化するためにジョブに関連する単語のフレーズ リストを追加する

Azure-Samples の GitHub リポジトリから [jobs-phrase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) ファイルを開きます。 リストに含まれるジョブの単語およびフレーズは 1,000 を超えます。 自分にとって意味のあるジョブの単語がリストにあるか確認します。 単語またはフレーズがリストにない場合は、自分で追加します。

1. LUIS アプリの **[ビルド]** セクションで、 **[アプリのパフォーマンス向上]** メニューの **[フレーズ リスト]** を選択します。

1. **[Create new phrase list]\(新しいフレーズ リストの作成\)** を選択します。 

1. 新しいフレーズ リスト `JobNames` に名前を付けて、そのリストを jobs-phrase-list.csv から **[値]** テキスト ボックスにコピーします。

    [![新しいフレーズ リストの作成ダイアログ ポップアップのスクリーンショット](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "新しいフレーズ リストの作成ダイアログ ポップアップのスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    フレーズ リストに単語をさらに追加したい場合は、 **[推奨]** を選択してから、新しい **[Related Values]\(関連する値\)** を確認し、関連のある単語を追加します。 

    これらの値は、すべてがジョブのシノニムとして扱われる必要があるため、 **[These values are interchangeable]\(これらの値は交換可能\)** をオンのままにしておきます。 交換可能と交換不能の[フレーズ リストの概念](luis-concept-feature.md#how-to-use-phrase-lists)について学習してください。

1. **[完了]** を選択して、フレーズ リストをアクティブにします。

    [![フレーズ リストの値ボックスに単語が表示されている、新しいフレーズ リストの作成ダイアログ ポップアップのスクリーンショット](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "フレーズ リストの値ボックスに単語が表示されている、新しいフレーズ リストの作成ダイアログ ポップアップのスクリーンショット")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. フレーズ リストが使用されるように、アプリを再度トレーニングして発行します。

1. 同じ発話 `This is the lead welder paperwork.` を含むエンドポイントにクエリを再実行します。

    JSON 応答には、抽出された次のエンティティが含まれています。

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>関連情報

* [意図のチュートリアル (エンティティなし)](luis-quickstart-intents-only.md)
* [Simple エンティティ](luis-concept-entity-types.md)の概念に関する情報
* [フレーズ リスト](luis-concept-feature.md)の概念に関する情報
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Human Resources アプリで、機械学習された Simple エンティティを使用して発話からジョブ名を見つけます。 ジョブ名はさまざまな単語または語句である可能性があるため、ジョブ名の語を強化するためのフレーズ リストがアプリに必要でした。 

> [!div class="nextstepaction"]
> [事前構築済みの KeyPhrase エンティティを追加する](luis-quickstart-intent-and-key-phrase.md)
