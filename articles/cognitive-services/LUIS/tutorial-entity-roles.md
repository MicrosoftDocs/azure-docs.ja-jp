---
title: チュートリアル:ロールを持つ文脈データ - LUIS
titleSuffix: Azure Cognitive Services
description: コンテキストに基づいて関連するデータを見つけます。 たとえば、あるビルやオフィスから別のビルやオフィスへの物理的な移動の出発地と到着地が関連します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 29e43692c1eb543768934a961a2bb8ae5a023b1d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894605"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>チュートリアル:コンテキストが関連するデータを発話から抽出する

このチュートリアルでは、コンテキストに基づいて関連するデータを検索します。 たとえば、ある都市から別の都市へ移動する場合の出発地と目的地です。 両方のデータが必要である可能性があり、これらは互いに関連しています。

ロールは、任意の事前構築済みまたはカスタムのエンティティ型で使用でき、発話とパターンの両方の例で使用できます。

[!INCLUDE [Only valid with current portal](includes/old-portal-only.md)]

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * 新しいアプリを作成する
> * 意図を追加する
> * ロールを使用して、出発地と目的地の情報を取得する
> * トレーニング
> * 発行
> * エンドポイントから意図とエンティティのロールを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>関連データ

このアプリでは、従業員の移動に関して出発地の都市と目的地の都市の場所を判断します。 アプリでは、GeographyV2 事前構築済みエンティティを使用して都市名を識別し、ロールを使用して発話内の場所の種類 (出発地と目的地) を判定します。

抽出するエンティティ データが次に該当する場合は、ロールを使用する必要があります。

* 発話のコンテキストで相互に関連する。
* 選択した特定の単語を使用して、それぞれのロールを表す。 ("から/への"、"を出て/に向かう"、"から/に" などの単語)。
* 両方のロールが同じ発話内に頻出し、LUIS がこの頻出するコンテキストにおける使用状況から学習することができる。
* クライアント アプリによって情報の単位としてグループ化され、処理される必要がある。

## <a name="create-a-new-app"></a>新しいアプリの作成

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>従業員を都市間で移動させる意図を作成する

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **[Create new intent]\(意図の新規作成\)** を選択します。

1. ポップアップ ダイアログ ボックスに「`MoveEmployeeToCity`」と入力して、 **[完了]** を選択します。

    ![新しい意図の作成ダイアログのスクリーンショット](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. 発話の例を意図に追加します。

    |発話の例|
    |--|
    |シアトルを離任する John W. Smith をオーランドへ移動させる|
    |Jill Jones をシアトルからカイロに転任させる|
    |John Jackson をタンパから出してアトランタに着任させる |
    |Debra Doughtery をシカゴからタルサに移動させる|
    |カイロを離任する Jill Jones をタンパに移動|
    |Alice Anderson をオークランドからレドモンドへ移す|
    |サンフランシスコの Carl Chamerlin をレドモンドへ|
    |Steve Standish をサンディエゴからベルビューへ転任させる |
    |カンザスシティの Tanner Thompson を昇進させてシカゴへ移す|

    [![MoveEmployee 意図で新しい発話が指定された LUIS のスクリーンショット](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>事前構築済みエンティティ geographyV2 を追加する

事前構築済みエンティティ geographyV2 は、都市名を含む場所の情報を抽出します。 発話にはコンテキスト内で相互に関連する 2 つの都市名が含まれているため、ロールを使用してそのコンテキストを抽出します。

1. 左側のナビゲーションから **[エンティティ]** を選択します。

1. **[Add prebuilt entity]\(作成済みエンティティの追加\)** を選択し、検索バーで `geo` を選択して事前構築済みエンティティをフィルター処理します。

    ![geographyV2 事前構築済みエンティティをアプリに追加する](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. 該当するチェック ボックスをオンにし、 **[完了]** を選択します。
1. **[エンティティ]** の一覧で、 **[geographyV2]** を選択して新しいエンティティを開きます。
1. `Origin` と `Destination` の 2 つのロールを追加します。

    ![事前構築済みエンティティにロールを追加する](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. 左側のナビゲーションから **[Intents]\(意図\)** を選択し、次に **MoveEmployeeToCity** 意図を選択します。 都市名は、あらかじめ構築されたエンティティ **geographyV2** のラベルが付いていることに注意してください。
1. 一覧の最初の発話で、出発地を選択します。 ドロップダウン メニューが表示されます。 一覧の **[geographyV2]** を選択し、メニューに従って **[Origin]\(出発地\)** を選択します。
1. 前の手順の方法を使用して、すべての発話内の場所のすべてのロールをマークします。


## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>意図への変更をテストできるようにアプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>トレーニング済みのモデルがエンドポイントからクエリ可能になるようにアプリを発行する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. アドレス バーの URL の末尾に移動し、「`Please move Carl Chamerlin from Tampa to Portland`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 この発話はラベル付けされた発話のどれとも同じではありません。したがって、テストは適切で、抽出されたエンティティと共に `MoveEmployee` 意図が返されます。

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.9706451
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.9706451
        },
        {
          "intent": "None",
          "score": 0.0307451729
        }
      ],
      "entities": [
        {
          "entity": "tampa",
          "type": "builtin.geographyV2.city",
          "startIndex": 32,
          "endIndex": 36,
          "role": "Origin"
        },
        {
          "entity": "portland",
          "type": "builtin.geographyV2.city",
          "startIndex": 41,
          "endIndex": 48,
          "role": "Destination"
        }
      ]
    }
    ```

    正しい意図が予測され、エンティティ配列の対応する **entities** プロパティには出発地と到着地の両方のロールが含まれています。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>関連情報

* [エンティティの概念](luis-concept-entity-types.md)
* [ロールの概念](luis-concept-roles.md)
* [事前構築済みエンティティの一覧](luis-reference-prebuilt-entities.md)
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)
* [ロール](luis-concept-roles.md)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、新しい意図を作成し、出発地と到着地のコンテキストから学習されたデータに関する発話の例を追加しました。 アプリがトレーニングおよび発行されたら、クライアント アプリケーションはその情報を使用して、関連する情報を含む移動チケットを作成できます。

> [!div class="nextstepaction"]
> [複合エンティティを追加する方法を確認する](luis-tutorial-composite-entity.md)
