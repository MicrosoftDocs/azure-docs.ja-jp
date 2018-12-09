---
title: LUIS アプリで発話の例を追加する
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) アプリケーションで発話を追加する方法を学習します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/24/2018
ms.author: diberry
ms.openlocfilehash: caf8628569481149ee41e8253f4759e15e49150f
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139125"
---
# <a name="add-an-entity-to-example-utterances"></a>発話の例にエンティティを追加する 

発話の例とは、ユーザーの質問や命令のサンプル テキストです。 Language Understanding (LUIS) に学習させるには、[意図](luis-concept-intent.md)に[発話の例](luis-concept-utterance.md)を追加する必要があります。

通常は、まず、発話の例を意図に追加し、次に意図ページでエンティティを作成して発話にラベルを付けます。 先にエンティティを作成する場合、[エンティティの追加](luis-how-to-add-entities.md)に関するページを参照してください。

## <a name="marking-entities-in-example-utterances"></a>発話の例のエンティティにマークを付ける

発話の例でエンティティにマークを付けるためにテキストを選択すると、インプレースのポップアップ メニューが表示されます。 このメニューを使用して、エンティティの作成または選択を行います。 

事前構築済みのエンティティや正規表現エンティティなど、特定の種類のエンティティは自動的にタグ付けされるため、発話の例でタグ付けすることはできません。 

## <a name="add-a-simple-entity"></a>シンプル エンティティを追加する

以下の手順では、意図ページの次の発話内でカスタム エンティティを作成してタグを付けます。

```text
Does John Smith work in Seattle?
```

1. 発話から `Seattle` を選択し、シンプル エンティティのラベルを付けます。

    [![発話内でシンプル エンティティのテキストを選択したスクリーンショット](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)

    > [!NOTE]
    > エンティティとしてタグ付けする単語を選択するときは:
    > * 1 つの単語の場合、単純にそれを選択します。 
    > * 2 語以上からなる場合、先頭を選択し、それから末尾を選択します。

1. エンティティ ドロップダウン ボックスが表示されたら、既存のエンティティを選択するか、新しいエンティティを追加できます。 新しいエンティティを追加するには、テキスト ボックスにその名前を入力し、**[新しいエンティティの作成]** を選択します。 

    ![エンティティ名を入力したスクリーンショット](./media/luis-how-to-add-example-utterances/hr-create-simple-2.png)

1. **[What type of entity do you want to create?]\(どのような種類のエンティティを作成しますか\)** ポップアップ ボックスで、エンティティ名を確認し、エンティティの種類として**シンプル**を選択して、**[完了]** を選択します。

    一般的には、シンプル エンティティのシグナルを強化するために[フレーズ リスト](luis-concept-feature.md)を使用します。

## <a name="add-a-list-entity"></a>リスト エンティティを追加する

リスト エンティティとは、システムにおける関連単語の集まりであり、固定かつ限定的です (テキスト厳密一致)。 

会社の部門のリストの場合、正規化された値 `Accounting` および `Human Resources` を含めることができます。 正規名にはそれぞれシノニム (類義語) があります。 部門の場合、シノニムとして部門の頭字語、数字、スラングなどを含めることができます。 エンティティを作成するとき、すべての値を知っている必要はありません。 実際にユーザーがシノニムで発話するのを見てから追加できます。

1. 発話の例のリストでは、特定の発話について、新しいリストに含めたい単語または語句を選択します。 次に、上部のテキストボックスにリストの名前を入力し、**[Create new entity]\(新しいエンティティの作成\)** を選択します。   

    ![リスト エンティティ名を入力したスクリーンショット](./media/luis-how-to-add-example-utterances/hr-create-list-1.png)


1. **[What type of entity do you want to create?]\(どのような種類のエンティティを作成しますか\)** ポップアップ ボックスで、エンティティの名前を入力し、種類として**リスト**を選択します。 このリスト アイテムのシノニムを追加し、**[完了]** を選択します。 

    ![リスト エンティティのシノニムを入力したスクリーンショット](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    リスト アイテムまたはアイテムのシノニムをさらに追加するには、他の発話にラベルを付けるか、左のナビゲーションにある **[エンティティ]** でエンティティを編集します。 エンティティの[編集](luis-how-to-add-entities.md#add-list-entities)では、対応するシノニムと一緒に追加アイテムを入力したり、リストをインポートしたりできます。 

## <a name="add-composite-entity"></a>複合エンティティを追加する

複合エンティティは、既存の**エンティティ**を元に親エンティティとして作成します。 

`Does John Smith work in Seattle?` という発話を想定した場合、複合発話は、1 つの親オブジェクトに含まれる従業員の名前と場所のエンティティ情報を返すことができます。 

従業員名の John Smith は、事前構築済みの [personName](luis-reference-prebuilt-person.md) エンティティです。 場所の Seattle は、カスタムのシンプル エンティティです。 これら 2 つのエンティティを作成して発話の例でタグ付けしたら、それらのエンティティをラップして複合エンティティにすることができます。 

1. 個々のエンティティをラップして複合エンティティにするには、複合エンティティの発話で**最初**のラベル付きエンティティ (一番左) を選択します。 ドロップダウン リストが表示され、この選択の選択肢が示されます。

1. ドロップダウン リストから **[Wrap composite entity]\(複合エンティティをラップする\)** を選択します。 

    ![[Wrap in composite entity]\(複合エンティティにラップする\) を選択したスクリーンショット](./media/luis-how-to-add-example-utterances/hr-create-composite-1.png)

1. 複合エンティティの最後の単語 (一番右) を選択します。 複合エンティティの後の緑の線にご注意ください。

1. ドロップダウン リストで複合エンティティ名を入力します。

    ![エンティティ名を入力したスクリーンショット](./media/luis-how-to-add-example-utterances/hr-create-composite-2.png)

    エンティティが正しくラップされていると、語句全体に緑の下線が付きます。

1. **[What type of entity do you want to create?]\(どのような種類のエンティティを作成しますか\)** ポップアップ ボックスで複合エンティティの詳細を確認し、**[完了]** を選択します。

    ![ポップアップのエンティティの詳細のスクリーン ショット](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. 複合エンティティに含まれる個々のエンティティは青で強調表示され、複合エンティティ全体は緑の下線付きで表示されます。 

    ![複合エンティティが表示された意図の詳細ページのスクリーンショット](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>階層構造エンティティを追加する

階層構造エンティティは、文脈から学習され、概念的に関連付けられる部類のエンティティです。 次の例では、エンティティに出発地と到着地が含まれます。 

発話 `Move John Smith from Seattle to Cairo` では、シアトルが出発地で、カイロが到着地です。 いずれの場所も文脈的に異なり、発話の中の単語の順序と選択から学習されます。

1. 意図ページの発話で、`Seattle` を選択し、エンティティ名 `Location` を入力して、キーボードの Enter を押します。

    ![階層構造エンティティのラベルを作成するダイアログ ボックスのスクリーンショット](./media/luis-how-to-add-example-utterances/hr-hier-1.png)

1. **[What type of entity do you want to create?]\(どのような種類のエンティティを作成しますか\)** ポップアップ ボックスで、**エンティティの種類**として _階層_ を選択し、子として `Origin` と `Destination` を追加して、**[完了]** を選択します。

    ![意図の詳細ページのスクリーンショット、到着地エンティティが強調表示されています](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. 発話の中の単語に親階層構造エンティティのラベルが付けられました。 その単語を子エンティティに割り当てる必要があります。 意図の詳細ページの発話に戻ります。 単語を選択し、選択したエンティティ名をドロップダウン リストから選択し、メニューを右にたどり、正しい子エンティティを選択します。

    ![意図の詳細ページのスクリーンショット、到着地エンティティが強調表示されています](./media/luis-how-to-add-example-utterances/hr-hier-3.png)

    >[!CAUTION]
    >子エンティティ名は、1 つのアプリのエンティティ全体で一意となる必要があります。 2 つの異なる階層構造エンティティには、同じ名前の子エンティティを含めることができません。 

## <a name="entity-status-predictions"></a>エンティティの状態の予測

LUIS ポータルで新しい発話を入力するとき、発話にエンティティ予測誤差が存在することがあります。 予測誤差とは、エンティティのラベル付けと、LUIS によるエンティティの予測の間の差異です。 

この差異は、LUIS ポータルでは発話の赤い下線で視覚的に示されます。 赤い下線は、エンティティの角かっこ内または角かっこ外に表示されることがあります。 

![エンティティ状態予測不一致のスクリーンショット](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

発話で赤い下線の付いた単語を選択します。 

予測に相違がある場合、エンティティ ボックスに赤い感嘆符の付いた **[Entity status]\(エンティティの状態\)** が表示されます。 エンティティの状態と、ラベル付けされたエンティティと予測されたエンティティの間の相違に関する情報を表示するには、**[Entity status]\(エンティティの状態\)** を選択した後、右側の項目を選択します。

![エンティティ状態予測不一致のスクリーンショット](./media/luis-how-to-add-example-utterances/entity-status.png)

赤い線は、次のいずれかのときに表示される可能性があります。

   * エンティティがラベル付けされる前に、発話が入力されたとき
   * エンティティ ラベルが適用されたとき
   * エンティティ ラベルが削除されたとき
   * そのテキストに対して複数のエンティティ ラベルが予測されたとき 

エンティティ予測の不一致を解決するには、次の解決策が役に立ちます。

|エンティティ|視覚的インジケータ|予測|解決策|
|--|--|--|--|
|発話が入力されたが、エンティティがまだラベル付けされていない。|赤い下線|予測は正しい。|予測された値でエンティティにラベルを付けます。|
|ラベル付けされていないテキスト|赤い下線|正しくない予測|この正しくないエンティティを使用している現在の発話を、すべての意図について確認する必要があります。 現在の発話には、このテキストが予測されたエンティティである、誤って学習された LUIS があります。
|正しくラベル付けされたテキスト|エンティティの青い強調表示、赤い下線|正しくない予測|さまざまな場所と使用方法で、正しくラベル付けされたエンティティを含む発話をさらに提供します。 現在の発話は、これがエンティティであることを LUIS に学習させるのに十分ではないか、または似たエンティティが同じコンテキストで出現しています。 LUIS が混乱しないよう、似たエンティティは 1 つのエンティティに結合する必要があります。 もう 1 つの解決策は、フレーズ リストを追加して単語の重要性を高めることです。 |
|誤ってラベル付けされたテキスト|エンティティの青い強調表示、赤い下線|正しい予測| さまざまな場所と使用方法で、正しくラベル付けされたエンティティを含む発話をさらに提供します。 

## <a name="other-actions"></a>その他のアクション

選択したグループまたは個々のアイテムとして発話の例にアクションを実行できます。 選択した発話の例のグループの場合、リストの上のコンテキスト メニューが変更されます。 単一のアイテムの場合、リストの上のコンテキスト メニューと、発話の各行の末尾に表示される個々のコンテキスト省略記号の両方を使用できます。 

### <a name="remove-entity-labels-from-utterances"></a>発話からエンティティ ラベルを削除する

機械学習したエンティティ ラベルは意図ページで発話から削除できます。 エンティティが機械学習ではない場合、発話から削除できません。 機械学習ではないエンティティを発話から削除する必要がある場合、アプリ全体からエンティティを削除する必要があります。 

機械学習したエンティティ ラベルを発話から削除するには、発話でそのエンティティを選択します。 次に、エンティティ ドロップダウン ボックスが表示されるのでそこから **[ラベルの削除]** を選択します。

![意図の詳細ページのスクリーンショット、[ラベルの削除] が強調表示されています](./media/luis-how-to-add-example-utterances/remove-label.png) 

### <a name="add-prebuilt-entity-label"></a>事前構築済みエンティティ ラベルを追加する

事前構築済みエンティティを LUIS アプリに追加する場合、発話にそれらのエンティティのタグを付ける必要はありません。 事前構築済みエンティティとその追加方法に関する詳細については、[エンティティの追加](luis-how-to-add-entities.md#add-prebuilt-entity)に関するページを参照してください。

### <a name="add-regular-expression-entity-label"></a>正規表現エンティティ ラベルを追加する

正規表現エンティティを LUIS アプリに追加する場合、発話にそれらのエンティティのタグを付ける必要はありません。 正規表現エンティティとその追加方法に関する詳細については、[エンティティの追加](luis-how-to-add-entities.md#add-regular-expression-entities)に関するページを参照してください。


### <a name="create-a-pattern-from-an-utterance"></a>発話からパターンを作成する

[予測の正確さを向上するためにパターンを追加する方法](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)に関するページをご覧ください。


### <a name="add-patternany-entity"></a>pattern.any エンティティを追加する

pattern.any エンティティを LUIS アプリに追加する場合、発話にそのエンティティのラベルを付けることはできません。 pattern.any エンティティはパターンでのみ有効です。 pattern.any エンティティとその追加方法に関する詳細については、[エンティティの追加](luis-how-to-add-entities.md#add-patternany-entities)に関するページを参照してください。

## <a name="train-your-app-after-changing-model-with-utterances"></a>発話を含むモデルの変更後にアプリをトレーニングする

発話を追加、編集、または削除したら、その変更について、エンドポイントのクエリに影響を与えるようにアプリを[トレーニング](luis-how-to-train.md)して[発行](luis-how-to-publish-app.md)します。 

## <a name="next-steps"></a>次の手順

意図で発話にラベルを付けたら、[複合エンティティ](luis-how-to-add-entities.md)を作成できます。
