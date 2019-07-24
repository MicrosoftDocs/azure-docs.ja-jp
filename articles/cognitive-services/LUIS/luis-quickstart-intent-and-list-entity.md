---
title: 完全なテキスト一致
titleSuffix: Azure Cognitive Services
description: 事前に定義された項目の一覧に一致するデータを取得します。 一覧の各項目には、同様に完全に一致するシノニムを含めることができます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 9083227dd81dca219666e07b70f487069413855d
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521273"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>チュートリアル:発話から完全なテキスト一致データを取得する

このチュートリアルでは、事前に定義された項目の一覧に一致するエンティティ データを取得する方法について説明します。 

**このチュートリアルで学習する内容は次のとおりです。**

<!-- green checkmark -->
> [!div class="checklist"]
> * アプリを作成する
> * 意図を追加する
> * リスト エンティティを追加する 
> * トレーニング 
> * 発行
> * エンドポイントから意図とエンティティを取得する

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>リスト エンティティとは

リスト エンティティは、発話内の単語に対する完全なテキストの一致です。 

一覧の各項目には、シノニムの一覧を含めることができます。 人事管理アプリの場合、正式な名称、一般的な略語、請求部門コードなど、いくつかの重要な情報で会社の部門を識別できます。 

人事管理アプリでは、従業員の異動先の部門を決定する必要があります。 

リスト エンティティがこのデータの種類に適しているのは次の場合です。

* データ値が既知のセットである。
* セットがこのエンティティ型の最大 LUIS [境界](luis-boundaries.md)を超えていない。
* 発話内のテキストがシノニムまたは正規名に完全に一致している。 LUIS では、完全なテキストの一致以外にリストは使用されません。 語幹抽出、複数形、その他のバリエーションは、リスト エンティティだけでは解決されません。 バリエーションを管理するには、オプションのテキスト構文で[パターン](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance)を使用することを検討します。 

## <a name="create-a-new-app"></a>新しいアプリの作成

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>従業員を別の部門に異動させる意図を作成する

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **[Create new intent]\(意図の新規作成\)** を選択します。 

3. ポップアップ ダイアログ ボックスに「`TransferEmployeeToDepartment`」と入力して、**[完了]** を選択します。 

    ![新しい意図の作成ダイアログのスクリーンショット](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. 発話の例を意図に追加します。

    |発話の例|
    |--|
    |move John W. Smith to the accounting department (John W. Smith を会計部門に異動させる)|
    |transfer Jill Jones from to R&D (Jill Jones を R&D に異動させる)|
    |Dept 1234 has a new member named Bill Bradstreet (部門 1234 に Bill Bradstreet という名前の新しいメンバーが加わる)|
    |Place John Jackson in Engineering (John Jackson をエンジニアリングに配属する) |
    |move Debra Doughtery to Inside Sales (Debra Doughtery を社内販売に移す)|
    |mv Jill Jones to IT (Jill Jones を IT にやる)|
    |Shift Alice Anderson to DevOps (Alice Anderson を DevOps にシフトする)|
    |Carl Chamerlin to Finance (Carl Chamerlin を財務に)|
    |Steve Standish to 1234 (Steve Standish を 1234 へ)|
    |Tanner Thompson to 3456 (Tanner Thompson を 3456 に)|

    [![発話例での意図のスクリーンショット](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "発話例での意図のスクリーンショット ショット")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>部門リスト エンティティ

これで **TransferEmployeeToDepartment** 意図に発話の例が含まれたので、LUIS では部門が何かを理解する必要があります。 

各項目のプライマリ ("_正規_") 名は部門名です。 各正規名のシノニムの例は次のとおりです。 

|正規名|シノニム|
|--|--|
|会計|acct<br>accting<br>3456|
|Development Operations|Devops<br>4949|
|Engineering|eng<br>enging<br>4567|
|Finance|fin<br>2020|
|Information Technology|IT<br>2323|
|Inside Sales|isale<br>insale<br>1414|
|Research and Development|R&D<br>1234|

1. 左のパネルで **[エンティティ]** を選びます。

1. **[Create new entity]\(新しいエンティティの作成\)** を選択します。

1. エンティティ ポップアップ ダイアログで、エンティティ名として「`Department`」を入力し、エンティティ型として **[リスト]** を指定します。 **[完了]** を選択します。  

    [![新しいエンティティ作成のポップアップ ダイアログのスクリーンショット](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "新しいエンティティ作成のポップアップ ダイアログのスクリーンショット")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. 部門エンティティ ページで、新しい値として「`Accounting`」と入力します。

    [![値入力のスクリーンショット](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "値入力のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

1. シノニムに対しては、前の表からシノニムを追加します。

    [![シノニム入力のスクリーンショット](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "シノニム入力のスクリーンショット")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

1. 続けてすべての正規名とそのシノニムを追加します。 

## <a name="add-example-utterances-to-the-none-intent"></a>発話の例を None 意図に追加する 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>意図への変更をテストできるようにアプリをトレーニングする 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>トレーニング済みのモデルがエンドポイントからクエリ可能になるようにアプリを発行する

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>エンドポイントから意図およびエンティティ予測を取得する

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. アドレスの URL の末尾に移動し、「`shift Joe Smith to IT`」と入力します。 最後の querystring パラメーターは `q` です。これは発話の**クエリ**です。 ラベル付けされた発話に、この発話と一致するものはありません。したがって、テストは適切で、`TransferEmployeeToDepartment` 意図が返され `Department` が抽出されるはずです。

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>関連情報

* [リスト エンティティ](luis-concept-entity-types.md#list-entity)の概念に関する情報
* [トレーニング方法](luis-how-to-train.md)
* [発行方法](luis-how-to-publish-app.md)
* [LUIS ポータルでのテスト方法](luis-interactive-test.md)


## <a name="next-steps"></a>次の手順
このチュートリアルでは、新しい意図を作成し、発話の例を追加した後、発話から完全なテキスト一致を抽出するためのリスト エンティティを作成しました。 トレーニングおよびアプリの発行後、エンドポイントのクエリによって意図を識別し、抽出されたデータを取得しました。

引き続きこのアプリで、[複合エンティティを追加します](luis-tutorial-composite-entity.md)。

> [!div class="nextstepaction"]
> [ロールを持つ事前構築済みエンティティをアプリに追加する](tutorial-entity-roles.md)

