---
title: エンティティにラベルを付ける発話例
titleSuffix: Azure Cognitive Services
description: LUIS ポータルの意図の詳細ページで、発話例内にサブコンポーネントがある機械学習エンティティにラベルを付ける方法について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: diberry
ms.openlocfilehash: 58e813d30273db4e011039aa43cd59c61507895e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74383704"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>発話例内の機械学習エンティティにラベルを付ける

発話例内のエンティティにラベルを付けることで、エンティティがどのようなもので、発話内のどこに出現できるかが例として LUIS に示されます。 

## <a name="labeling-machine-learned-entity"></a>機械学習エンティティにラベルを付ける

「`hi, please I want a cheese pizza in 20 minutes`」というフレーズについて考えます。 

1. エンティティの左端のテキストを選択し、次に右端のテキストを選択して、ラベルを付けるエンティティ（この例では、Complete Order）を選択します。 次の画像では、_Complete Order_ にラベルが付けられています。

    > [!div class="mx-imgBorder"]
    > ![完全な機械学習エンティティにラベルを付ける](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. ポップアップ ウィンドウからエンティティを選択します。 ラベル付けされた完全なピザ注文エンティティには、ラベルが付いたすべての単語が (英語では左から右へ) 含まれています。 

## <a name="review-labeled-text"></a>ラベルが付けられたテキストをレビューする

ラベル付けした後、発話例を確認し、選択したテキスト範囲に、選択したエンティティと下線が付加されていることを確認します。 実線は、そのテキストにラベルが付けられたことを示しています。 

> [!div class="mx-imgBorder"]
> ![ラベルが付けられた完全な機械学習エンティティ](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>予測されたエンティティを確定する

テキストの範囲が点線のボックスで囲まれていて、エンティティ名が発話の上にある場合は、テキストは予測されていますが、"_ラベルはまだ付けられていない_" ことを示します。 予測をラベルに変更するには、発話行を選択してから、 **[Confirm entity predictions]\(エンティティ予測を確定\)** を選択します。

> [!div class="mx-imgBorder"]
> ![完全な機械学習エンティティを予測する](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

または、テキストの上にあるエンティティ名を選択し、表示されるメニューから **[Confirm Prediction]\(予測を確定\)** を選択することもできます。

> [!div class="mx-imgBorder"]
> ![メニューを使用して完全な機械学習エンティティを予測する](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>エンティティ パレットのカーソルを使用してペイントすることでエンティティにラベルを付ける

エンティティ パレットは、以前のラベル付けエクスペリエンスの代わりとして機能します。 これにより、テキストにブラシを適用することで、そのテキストにエンティティのラベルを瞬時に付けることができます。

1. 発話テーブルの右上にある蛍光ペン アイコンを選択して、エンティティ パレットを開きます。 

    > [!div class="mx-imgBorder"]
    > ![機械学習エンティティのエンティティ パレット](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. エンティティ コンポーネントを選択します。 この操作は、新しいカーソルによって視覚的に示されます。 このカーソルは、ポータル内でマウスの動きに従います。 

    > [!div class="mx-imgBorder"]
    > ![機械学習エンティティのエンティティ パレット](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. 発話例の中で、カーソルによってエンティティに _ペイント_ します。

    > [!div class="mx-imgBorder"]
    > ![機械学習エンティティのエンティティ パレット](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>機械学習エンティティのサブコンポーネントにラベルを付ける

エンティティ内のサブコンポーネントには、最上位レベルのエンティティとまったく同じようにラベルが付けられます。 テキスト選択時、ポップアップ ウィンドウで選択できるエンティティは、テキストが出現するコンテキストに関連しています。 たとえば、5 レベルの機械学習エンティティがあり、第 1 レベルおよび第 2 レベルのラベルが付いたテキスト (発話例の下のラベルが付いたエンティティ名で示されます) を選択している場合、ポップアップ ウィンドウで選択できるエンティティは、第 3 レベルのコンポーネントのコンテキストに限定されます。 テキストに他のエンティティのラベルを付けるには、 **[Label as another entity]\(別のエンティティとしてラベルを付ける\)** オプションを選択します。

> [!div class="mx-imgBorder"]
> ![機械学習エンティティのエンティティ パレット](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

親にもラベルが付けられている場合にのみ、サブコンポーネントにラベルを付けることができます。 

## <a name="unlabel-entities"></a>エンティティのラベルの解除

エンティティのラベルを解除するには、テキストの下にあるエンティティ名を選択し、 **[Unlabel]\(ラベル解除\)** を選択します。 ラベルを解除するエンティティに、ラベル付きのサブコンポーネントがある場合、最初にサブコンポーネントのラベルを解除する必要があります。 

## <a name="editing-labels-using-the-entity-palette"></a>エンティティ パレットを使用してラベルを編集する

ラベル付け中に間違えた場合、エンティティ パレットは、即時に編集できる使いやすいツールです。 たとえば、誤って余分な単語にエンティティ ラベルを適用し、そこにラベル付きのサブコンポーネントが既にある場合、エンティティ パレットを使用して、必要な短い範囲の単語にブラシを適用できます。

例:

1. Pizza Type サブコンポーネントが "cheese pizza with" に適用されています。これには、余分で不適切な単語 "with" が含まれています。

    > [!div class="mx-imgBorder"]
    > ![機械学習エンティティのエンティティ パレット](media/label-utterances/edit-label-with-palette-1.png)

2. エンティティ パレットを使用して、[Pizza Type] を選択し、"cheese pizza" にブラシを適用します。 この結果、cheese pizza にのみ Pizza Type としてラベルが付けられます。

    > [!div class="mx-imgBorder"]
    > ![機械学習エンティティのエンティティ パレット](media/label-utterances/edit-label-with-palette-2.png)


## <a name="labels-for-matching-text-entities"></a>照合テキスト エンティティのラベル

照合テキスト エンティティには、事前構築済みエンティティ、正規表現エンティティ、リスト エンティティ、および Pattern.any エンティティが含まれます。 これらには LUIS によって自動的にラベルが付けられるため、ユーザーが手動でラベルを付ける必要はありません。

## <a name="entity-prediction-errors"></a>エンティティ予測エラー

エンティティ予測エラーは、予測されたエンティティが、ラベル付けされたエンティティと一致しないことを示します。 これは、発話の横に表示される警告インジケーターによって視覚化されます。

> [!div class="mx-imgBorder"]
> ![機械学習エンティティのエンティティ パレット](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>次の手順

[ダッシュボード](luis-how-to-use-dashboard.md)を使用し、[エンドポイントの発話をレビュー](luis-how-to-review-endpoint-utterances.md)して、アプリの予測品質を向上させます。