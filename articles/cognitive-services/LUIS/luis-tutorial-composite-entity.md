---
title: チュートリアル:複合エンティティのチュートリアル - LUIS
titleSuffix: Azure Cognitive Services
description: さまざまな種類の抽出されたデータを、1 つの包含するエンティティにバンドルするための複合エンティティを追加します。 データをバンドルすることにより、クライアント アプリケーションはさまざまなデータ型で関連データを簡単に抽出できます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 0e72563f366330f841d1a61ed67956b6314c769a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893189"
---
# <a name="tutorial-group-and-extract-related-data"></a>チュートリアル:関連するデータのグループ化と抽出
このチュートリアルでは、さまざまな種類の抽出されたデータを、1 つの包含するエンティティにバンドルするための複合エンティティを追加します。 データをバンドルすることにより、クライアント アプリケーションはさまざまなデータ型で関連データを簡単に抽出できます。

複合エンティティの目的は、関連するエンティティを親カテゴリのエンティティにグループ化することです。 複合エンティティが作成される前は、情報は個別のエンティティとして存在しています。

複合エンティティは、データが次のようなものであるため、この種のデータに適しています。

* 相互に関連している。
* さまざまなエンティティ型を使用する。
* クライアント アプリによって情報の単位としてグループ化され、処理される必要がある。

[!INCLUDE [Only valid with current portal](includes/old-portal-only.md)]

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * サンプル アプリをインポートする
> * 意図を作成する
> * 複合エンティティを追加する
> * トレーニング
> * 発行
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>サンプル アプリをインポートする

1.  リスト エンティティのチュートリアルから[アプリの JSON ファイル](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json)をダウンロードして保存します。

2. JSON を新しいアプリにインポートします。

3. **[管理]** セクションの **[バージョン]** タブで、バージョンを複製し、それに `composite` という名前を付けます。 複製は、元のバージョンに影響を及ぼさずに LUIS のさまざまな機能を使用するための優れた方法です。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

## <a name="composite-entity"></a>複合エンティティ

このアプリでは、部署名は **Department** リスト エンティティで定義されていて、シノニムを含みます。

**TransferEmployeeToDepartment** 意図には、従業員の新しい部署への異動を要求する発話の例が含まれます。

この意図に含まれる発話の例を次に示します。

|発話の例|
|--|
|move John W. Smith to the accounting department (John W. Smith を会計部門に異動させる)|
|transfer Jill Jones from to R&D (Jill Jones を R&D に異動させる)|

異動要求には、部署名と従業員名が含まれる必要があります。

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>一般的な種類のデータを抽出するのに役立つ PersonName 事前構築済みエンティティを追加する

LUIS には、一般的なデータ抽出のための事前構築済みエンティティが用意されています。

1. 上部のナビゲーションから **[ビルド]** を選択し、左側のナビゲーション メニューから **[エンティティ]** を選択します。

1. **[Manage prebuilt entity]\(事前構築済みエンティティの管理\)** ボタンを選択します。

1. 事前構築済みエンティティの一覧から **[personName](luis-reference-prebuilt-person.md)** を選択し、 **[完了]** を選択します。

    ![[number] が選択されている事前構築済みエンティティ ダイアログのスクリーンショット](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    このエンティティは、クライアント アプリケーションに名前の認識を追加するのに役立ちます。

## <a name="create-composite-entity-from-example-utterances"></a>発話の例から複合エンティティを作成する

1. 左側のナビゲーションで、 **[Intents]\(意図\)** を選択します。

1. 意図の一覧から **TransferEmployeeToDepartment** を選択します。

1. 発話 `place John Jackson in engineering` で、personName エンティティ `John Jackson` を選択し、以下の発話に対するポップアップ メニュー リストで **[Wrap in composite entity]\(複合エンティティにラップする\)** を選択します。

    ![ラップ複合を選択するドロップダウン ダイアログのスクリーンショット](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. 次に、発話内の最後のエンティティ `engineering` を直ちに選択します。 選択された単語の下に、複合エンティティを示す緑色のバーが描画されます。 ポップアップ メニューで、複合名 `TransferEmployeeInfo` を入力して Enter キーを押します。

    ![複合名を入力するドロップダウン ダイアログのスクリーンショット](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. **[What type of entity do you want to create?]\(どのような種類のエンティティを作成しますか?\)** では、必要なすべてのフィールドが一覧にあります: `personName` と `Department`。 **[完了]** を選択します。 事前構築済みのエンティティ personName が複合エンティティに追加されました。 複合エンティティの開始トークンと終了トークンの間に事前構築済みエンティティを出現させることができた場合、複合エンティティにはそれらの事前構築済みエンティティが含まれている必要があります。 事前構築済みエンティティが含まれていない場合、複合エンティティは正しく予測されませんが、個別の要素は正しく予測されます。

    ![複合名を入力するドロップダウン ダイアログのスクリーンショット](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>発話の例に複合エンティティのラベルを付ける

1. 各発話の例で、複合に含まれている左端のエンティティを選択します。 次に、 **「Wrap in composite entity」(複合エンティティにラップする)** を選択します。

1. 複合エンティティ内の最後の単語を選択してから、ポップアップ メニューから **[TransferEmployeeInfo]** を選択します。

1. 意図のすべての発話に複合エンティティのラベルが付けられていることを確認します。

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>意図への変更をテストできるようにアプリをトレーニングする

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>トレーニング済みのモデルがエンドポイントからクエリ可能になるようにアプリを発行する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. アドレスの URL の末尾に移動し、「`Move Jill Jones to DevOps`」と入力します。 最後のクエリ文字列パラメーターは `q` です。これは発話のクエリです。

    このテストは複合が正しく抽出されたことを確認するためであるため、テストには、既存のサンプル発話または新しい発話のどちらを含めることもできます。 適切なテストでは、複合エンティティ内のすべての子エンティティを含めます。

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   この発話は、複合エンティティの配列を返します。 各エンティティには、型と値が与えられます。 子エンティティごとの精度を高めるには、複合の配列項目の型と値の組み合わせを使用して、エンティティの配列内の対応する項目を見つけます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>関連情報

* [リスト エンティティのチュートリアル](luis-quickstart-intents-only.md)
* [複合エンティティ](luis-concept-entity-types.md)の概念に関する情報
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、既存のエンティティをカプセル化するための複合エンティティを作成しました。 これによりクライアント アプリケーションは、会話を続けるために、さまざまなデータ型の関連データのグループを見つけることができます。 この Human Resources アプリのクライアント アプリケーションは、移動が開始および終了する必要がある日時を問い合わせることができました。 電話機などのその他の物品の移動について問い合わせることもできました。

> [!div class="nextstepaction"]
> [エンドポイントの発話を確認して不確かな予測を修正する](luis-tutorial-review-endpoint-utterances.md)
