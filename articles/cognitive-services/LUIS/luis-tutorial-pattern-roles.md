---
title: パターンの役割
titleSuffix: Azure Cognitive Services
description: パターンを使用すると、正しい形式のテンプレート発話からデータを抽出できます。 テンプレート発話は単純なエンティティとロールを使用して、移動元の場所や移動先の場所などの関連データを抽出します。
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: c0e3ac1d53cda2afb2184b92b0fd0afd662101bb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277513"
---
# <a name="tutorial-extract-contextually-related-patterns-using-roles"></a>チュートリアル:ロールを使って文脈的に関連するパターンを抽出する

このチュートリアルでは、パターンを使用して、正しい形式のテンプレート発話からデータを抽出します。 テンプレート発話は[単純なエンティティ](luis-concept-entity-types.md#simple-entity)と[ロール](luis-concept-roles.md)を使用して、出発地や目的地などの関連データを抽出します。  パターンを使用するとき、意図に必要な発話の例は少なくなります。


**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * サンプル アプリをインポートする
> * 新しいエンティティの作成
> * 新しい意図の作成
> * トレーニング
> * 発行
> * エンドポイントから意図とエンティティを取得する
> * ロールを持つパターンを作成する
> * 都市のフレーズ リストを作成する
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-roles-in-patterns"></a>パターンでロールを使う

ロールの目的は、文脈的に関連するエンティティを発話から抽出することです。 「`Move new employee Robert Williams from Sacramento and San Francisco`」という発話で、移動元の都市と移動先の都市の値は互いに関連しており、それぞれの場所を表すのに共通の言語を使用しています。 


新しい従業員の名前 Billy Patterson はまだリスト エンティティ **Employee** の一部ではありません。 外部のシステムに名前を送信して会社の資格情報を作成するために、まず新しい従業員の名前が抽出されます。 会社の資格情報が作成された後、その従業員の資格情報がリスト エンティティ **Employee** に追加されます。

新しい従業員と家族を、現在の都市から、架空の会社が所在する都市に移動させる必要があります。 新しい従業員はどの都市からも来る可能性があるため、場所を検出する必要があります。 リスト エンティティのようなセット リストは、リスト内の都市しか抽出されないため、機能しません。

移動元と移動先の都市に関連付けられたロール名は、すべてのエンティティ間で一意である必要があります。 ロールが一意であることを確認する簡単な方法は、命名方針によってそれらのロールを上位のエンティティと結び付けることです。 **NewEmployeeRelocation** エンティティは、2 つのロール **NewEmployeeReloOrigin** と **NewEmployeeReloDestination** があるシンプルなエンティティです。 Relo は Relocation (配置換え) の略です。

発話の例「`Move new employee Robert Williams from Sacramento and San Francisco`」には機械学習エンティティしか含まれていないため、エンティティが検出されるよう、十分な発話例を意図に提供することが重要です。  

**パターンを使用すると提供される発話の例を少なくすることができますが、エンティティが検出されない場合、パターンは一致しません。**

都市などの名前であるためシンプル エンティティの検出に問題がある場合、類似の値のフレーズ リストを追加することを検討してください。 これは、その種類の単語またはフレーズについて追加のシグナルを LUIS に与えることによって、都市名の検出を補助します。 フレーズ リストは、パターンが一致するために必要なエンティティ検出を補助することによってパターンを補助するにすぎません。 

## <a name="import-example-app"></a>サンプル アプリをインポートする
最後のチュートリアルで作成した、**HumanResources** という名前のアプリを引き続き使用します。 

次の手順に従います。

1.  [アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `roles` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

## <a name="create-new-entities"></a>新しいエンティティの作成

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. 左側のナビゲーションから **[エンティティ]** を選択します。 

3. **[Create new entity]\(新しいエンティティの作成\)** を選択します。

4. ポップアップ ウィンドウで、 **[簡易]** エンティティとして「`NewEmployee`」と入力します。

5. **[Create new entity]\(新しいエンティティの作成\)** を選択します。

6. ポップアップ ウィンドウで、 **[簡易]** エンティティとして「`NewEmployeeRelocation`」と入力します。

7. エンティティの一覧から **NewEmployeeRelocation** を選択します。 

8. 最初のロールを「`NewEmployeeReloOrigin`」と入力して Enter キーを押します。

9. 2 番目のロールを「`NewEmployeeReloDestination`」と入力して Enter キーを押します。

## <a name="create-new-intent"></a>新しい意図の作成
事前構築済みの keyPhrase エンティティが開始前に削除されてから、このセクションの手順を完了した後に再び追加される場合、以下の手順でエンティティにラベルを付けるほうが簡単なことがあります。 

1. 左側のナビゲーションで、 **[Intents]\(意図\)** を選択します。

2. **[Create new intent]\(意図の新規作成\)** を選択します。 

3. ポップアップ ダイアログ ボックスで、意図名として「`NewEmployeeRelocationProcess`」と入力します。

4. 次の発話例を入力し、新しいエンティティにラベルを付けます。 エンティティとロールの値は太字で示しています。 テキストにラベルを付けるほうが簡単な場合は、忘れずに**トークン ビュー**に切り替えてください。 

    意図にラベルを付けるとき、エンティティのロールは指定しません。 後でパターンを作成するときに指定します。 

    |発話|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Move **Bob Jones** from **Seattle** to **Los Colinas**|Bob Jones|Seattle、Los Colinas|
    |Move **Dave C. Cooper** from **Redmond** to **New York City**|Dave C. Cooper|Redmond、New York City|
    |Move **Jim Paul Smith** from **Toronto** to **West Vancouver**|Jim Paul Smith|Toronto、West Vancouver|
    |Move **J. Benson** from **Boston** to **Staines-upon-Thames**|J. Benson|Boston、Staines-upon-Thames|
    |Move **Travis "Trav" Hinton** from **Castelo Branco** to **Orlando**|Travis "Trav" Hinton|Castelo Branco、Orlando|
    |Move **Trevor Nottington III** from **Aranda de Duero** to **Boise**|Trevor Nottington III|Aranda de Duero、Boise|
    |Move **Dr.Greg Williams** from **Orlando** to **Ellicott City**|Dr.Greg Williams|Orlando、Ellicott City|
    |Move **Robert "Bobby" Gregson** from **Kansas City** to **San Juan Capistrano**|Robert "Bobby" Gregson|Kansas City、San Juan Capistrano|
    |Move **Patti Owens** from **Bellevue** to **Rockford**|Patti Owens|Bellevue、Rockford|
    |Move **Janet Bartlet** from **Tuscan** to **Santa Fe**|Janet Bartlet|Tuscan、Santa Fe|

    従業員の名前の接頭辞、単語数、構文、および接尾辞はさまざまです。 このことは、新しい従業員の名前のバリエーションを LUIS が理解する上で重要です。 都市名の単語数および構文もさまざまです。 この多様性は、これらのエンティティがユーザーの発話内でどのように出現する可能性があるかを LUIS に教えるために重要です。 
    
    いずれかのエンティティが同じ単語数であって他のバリエーションがない場合、このエンティティにはその単語数しかなく他のバリエーションは存在しないと LUIS に教えることになります。 LUIS には何も示されなかったので、より広範なバリエーションの集合を LUIS は正しく予測できないでしょう。 

    keyPhrase エンティティを削除した場合、今すぐアプリに追加し直してください。

## <a name="train"></a>トレーニング

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>発行

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. アドレスの URL の末尾に移動し、「`Move Wayne Berry from Miami to Mount Vernon`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 

    ```json
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

意図の予測スコアは約 50% にすぎません。 より高い数値がクライアント アプリケーションで必要な場合、これを修正する必要があります。 エンティティも予測されませんでした。

場所の 1 つが抽出されましたが、他の場所が抽出できませんでした。 

パターンは予測スコアを向上させますが、パターンが発話に一致する前にエンティティが正しく予測される必要があります。 

## <a name="pattern-with-roles"></a>ロールを持つパターン

1. 上部のナビゲーションで **[ビルド]** を選択します。

2. 左側のナビゲーションで **[Patterns]\(パターン\)** を選択します。

3. **[Select an intent]\(意図の選択\)** ドロップダウン リストから **NewEmployeeRelocationProcess** を選択します。 

4. 次のパターンを入力します。`move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    エンドポイントをトレーニングし、公開し、クエリしたところ、エンティティが見つからないためパターンに一致せず、したがって予測が改善しなかったという結果に失望するかもしれません。 これは、ラベルが付けられたエンティティに十分な発話例がないことが原因です。 さらに例を追加する代わりに、フレーズ リストを追加してこの問題を修正します。

## <a name="cities-phrase-list"></a>都市のフレーズ リスト
人の名前のような都市名は、単語と句読点が入り混じる可能性がある点で厄介です。 地域や世界の都市は既知であるため、LUIS で学習を始めるためには都市のフレーズ リストが必要です。 

1. 左側のメニューの **[Improve app performance]\(アプリのパフォーマンス改善\)** セクションから **[Phrase list]\(フレーズ リスト\)** を選択します。 

2. リストの名前を `Cities` にし、リストの`values`を次のように追加します。

    |フレーズ リストの値|
    |--|
    |シアトル|
    |San Diego|
    |New York City|
    |ロサンゼルス|
    |Portland|
    |Philadephia|
    |マイアミ|
    |ダラス|

    世界のすべての都市、または地域のすべての都市を追加しないでください。 何という都市かを LUIS がリストから一般化できる必要があります。 必ず、 **[These values are interchangeable]\(これらの値は交換可能\)** は選択したままにしてください。 この設定は、リスト上の単語がシノニムとして扱われることを意味します。 

3. アプリをトレーニングして公開します。

## <a name="get-intent-and-entities-from-endpoint"></a>エンドポイントから意図とエンティティを取得する

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. アドレスの URL の末尾に移動し、「`Move wayne berry from miami to mount vernon`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 

    ```json
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
   }
    ```

意図スコアは大きく向上し、ロール名がエンティティの応答に含まれるようになりました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ロールを持つエンティティと、発話の例を持つ意図を追加しました。 エンティティを使用した最初のエンドポイント予測では意図が正しく予測されましたが、低い信頼度スコアが得られました。 2 つのエンティティのうち 1 つだけが検出されました。 次に、このチュートリアルでは、エンティティのロールを使用するパターン、および発話中の都市名の価値を高めるためのフレーズ リストが追加されました。 2 番目のエンドポイント予測では信頼性の高いスコアが返され、両方のエンティティのロールが見つかりました。 

> [!div class="nextstepaction"]
> [LUIS アプリのベスト プラクティスを学ぶ](luis-concept-best-practices.md)
