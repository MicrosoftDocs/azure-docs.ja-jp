---
title: エンティティのロール
titleSuffix: Azure Cognitive Services
description: ロールは、パターンでのみ使用されるエンティティの名前付きコンテキスト サブタイプです。 たとえば、発話 `buy a ticket from New York to London` では、ロンドンとニューヨークは両方とも都市ですが、文章内での意味はそれぞれ異なります。 ニューヨークは出発地、ロンドンは目的地です。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 5fa922cb91d34483256faf4dcf70569aa2f17b97
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522488"
---
# <a name="entity-roles-for-contextual-subtypes"></a>コンテキスト サブタイプのエンティティのロール

ロールにより、エンティティは名前付きサブタイプを持つことができます。 ロールは、任意の事前構築済みまたはカスタムのエンティティ型で使用でき、発話とパターンの両方の例で使用できます。 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>ロールの機械学習エンティティの例

発話「**ニューヨーク**発**ロンドン**着の切符を買う」では、ニューヨークとロンドンは両方とも都市ですが、文章内での意味はそれぞれ異なります。 ニューヨークは出発地、ロンドンは目的地です。 

```
buy a ticket from New York to London
```

ロールでは、こうした違いに対して名前が付けられます。

|エンティティの種類|エンティティ名|Role|目的|
|--|--|--|--|
|シンプル|Location|origin|飛行機が離陸する場所|
|シンプル|Location|destination|飛行機が着陸する場所|

## <a name="non-machine-learned-entity-example-of-roles"></a>ロールの非機械学習エンティティの例

発話「8 時から 9 時までの会議をスケジュールする」では、両方の数字は時間を示していますが、発話内での意味は時間ごとに異なります。 ロールでは、こうした違いに対して名前が付けられます。 

```
Schedule the meeting from 8 to 9
```

|エンティティの種類|ロール名|値|
|--|--|--|
|事前構築済みの datetimeV2|開始時間|8|
|事前構築済みの datetimeV2|終了時間|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>発話内の複数のエンティティは、ロールと同じものですか? 

発話には複数のエンティティが存在する可能性があり、それらのエンティティはロールを使用せずに抽出できます。 エンティティのバージョンに値があることを文のコンテキストが示している場合、ロールを使用する必要があります。 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>意味のない重複にロールを使用しない

発話に場所の一覧が含まれる場合 `I want to travel to Seattle, Cairo, and London.`、これは各項目に追加の意味がない一覧です。 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>重複が意味を示す場合はロールを使用する

意味を持つ場所の一覧が発話に含まれている場合 `I want to travel from Seattle, with a layover in Londen, landing in Cairo.`、この出発地、乗り継ぎ、目的地の意味をロールでキャプチャする必要があります。

### <a name="roles-can-indicate-order"></a>ロールで順序を示すことができる

抽出したい順序を示すように発話が変更された場合 `I want to first start with Seattle, second London, then third Cairo`、いくつかの方法で抽出できます。 ロールを示すトークン `first start with`、`second`、`third` にタグを付けることができます。 事前構築済みエンティティ **Ordinal** と **GeographyV2** 事前構築済みエンティティを複合エンティティで使用して、順序と場所の概念を取り込むこともできます。 

## <a name="how-are-roles-used-in-example-utterances"></a>発話の例ではロールをどのように使用していますか?

エンティティにロールがあり、そのエンティティが発話の例でマークされている場合、エンティティのみを選ぶか、エンティティとロールを選ぶかを選択できます。 

次の発話の例では、エンティティとロールを使用しています。

|トークン ビュー|エンティティ ビュー|
|--|--|
|**シアトル**についてさらに知りたいと思っています|{Location} についてさらに知りたいと思っています|
|シアトルからニューヨークへのチケットを購入する|{Location:Origin} から {Location:Destination} へのチケットを購入する|

## <a name="how-are-roles-related-to-hierarchical-entities"></a>ロールは階層エンティティとどのように関連していますか?

これまでのパターンの使用と同様に、発話の例のすべてのエンティティに対してロールが使用可能になりました。 あらゆる場所で使用できるため、階層エンティティの代わりとなるものです。 新しいエンティティは、階層エンティティを使用するのではなく、ロールを使用して作成する必要があります。 

階層エンティティは最終的に非推奨になります。

## <a name="how-are-roles-used-in-patterns"></a>パターンでのロールの使用方法
パターンのテンプレートの発話では、ロールが発話内で使用されます。 

|エンティティ ロールのあるパターン|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>パターンでのロールの構文
エンティティとロールは、かっこ `{}` で囲まれています。 エンティティとロールは、コロンで区切られています。 

## <a name="entity-roles-versus-collaborator-roles"></a>エンティティのロールとコラボレーターのロール

エンティティのロールは、LUIS アプリのデータ モデルに適用されます。 [コラボレーター](luis-concept-collaborator.md)のロールは、オーサリング アクセス許可のレベルに適用されます。 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>次の手順

* 非機械学習エンティティのエンティティ ロールを使用した[実践的なチュートリアル](tutorial-entity-roles.md)を使用します
* [ロール](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)を追加する方法を確認します
