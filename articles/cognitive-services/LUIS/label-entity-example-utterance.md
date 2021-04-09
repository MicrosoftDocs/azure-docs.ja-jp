---
title: エンティティにラベルを付ける発話例
description: LUIS ポータルの意図の詳細ページで、発話例内にサブエンティティがある機械学習エンティティにラベルを付ける方法について説明します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96019737"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>発話例内の機械学習エンティティにラベルを付ける

発話例内のエンティティにラベルを付けることで、エンティティがどのようなもので、発話内のどこに出現できるかが例として LUIS に示されます。

機械学習エンティティとサブエンティティにラベルを付けることができます。

正規表現エンティティ、リスト エンティティ、事前構築済みエンティティにラベルを付けることはできないため、エンティティまたはサブエンティティを作成してから、それらのエンティティをフィーチャーとして (該当する場合) エンティティまたはサブエンティティに追加します。

## <a name="label-example-utterances-from-the-intent-detail-page"></a>意図の詳細ページから発話例にラベルを付ける

発話内のエンティティの例にラベルを付けるには、その発話の意図を選択します。

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の **サブスクリプション** と **作成リソース** を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。
1. エンティティを抽出するためにラベル付けする発話例が含まれている意図を選択します。
1. ラベルを付けるテキストを選択し、エンティティを選択します。

## <a name="two-techniques-to-label-entities"></a>エンティティにラベルを付ける 2 つの方法

意図の詳細ページでは、2 つのラベル付けの手法がサポートされています。
* [エンティティ パレット](#label-with-the-entity-palette-visible)からエンティティまたはサブエンティティを選択し、発話例のテキスト内から選択する。 スキーマに合わせて正しいエンティティまたはサブエンティティを操作していることを視覚的に確認できるため、この方法をお勧めします。
* 最初に発話例のテキスト内から選択する。 この操作を行うと、[ラベル付けの選択](#how-to-label-entity-from-in-place-menu)のポップアップ メニューが表示されます。

## <a name="label-with-the-entity-palette-visible"></a>エンティティ パレットが表示されているラベル

[エンティティについてスキーマを計画](luis-how-plan-your-app.md)した後は、ラベル付け中に **エンティティ パレット** を表示したままにしておきます。 **エンティティ パレット** は、どのエンティティを抽出するかについての計画のリマインダーになります。

**エンティティ パレット** にアクセスするには、発話例の一覧の上のコンテキスト ツールバーで、 **@** 記号を選択します。

> [!div class="mx-imgBorder"]
> ![意図の詳細ページのエンティティ パレットのスクリーンショット。](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>エンティティ パレットからエンティティにラベルを付ける方法

エンティティ パレットは、以前のラベル付けエクスペリエンスの代わりとして機能します。 これにより、テキストにブラシを適用することで、そのテキストにエンティティのラベルを瞬時に付けることができます。

1. 発話テーブルの右上にある **@** 記号を選択して、エンティティ パレットを開きます。

2. パレットからラベルを付けるエンティティを選択します。 この操作は、新しいカーソルによって視覚的に示されます。 このカーソルは、LUIS ポータル内でマウスの動きに従います。

3. 発話例の中で、カーソルによってエンティティに _ペイント_ します。

    > [!div class="mx-imgBorder"]
    > ![カーソルでペイントされたエンティティのスクリーンショット。](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>エンティティ パレットからエンティティをフィーチャーとして追加する

エンティティ パレットの下部セクションでは、現在選択されているエンティティにフィーチャーを追加できます。 すべて既存のエンティティとフレーズの一覧から選択することも、新しいフレーズの一覧を作成することもできます。

> [!div class="mx-imgBorder"]
> ![フィーチャーとしてのエンティティが表示されたエンティティ パレットのスクリーンショット](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>エンティティのロールをラベル付けする

エンティティのロールのラベル付けは、**エンティティ パレット** を使用して行います。

1. [Intent detail]\(意図の詳細\) ページのコンテキスト ツール バーから **[Entity palette]\(エンティティ パレット\)** を選択します。
1. エンティティ パレットが開いたら、エンティティの一覧からエンティティを選択します。
1. エンティティの一覧の下で、既存のロールを選択します。
1. 発話の例のテキストに、エンティティのロールをラベル付けします。

## <a name="how-to-label-entity-from-in-place-menu"></a>埋め込みメニューからエンティティにラベルを付ける方法

埋め込みでのラベル付けにより、発話内のテキストを迅速に選択してラベルを付けることができます。 また、ラベルが付けられたテキストから機械学習エンティティまたはリスト エンティティを作成することもできます。

`hi, please I want a cheese pizza in 20 minutes` という発話例があるとします。

エンティティの左端のテキストを選択し、次に右端のテキストを選択して、埋め込みメニューから、ラベルを付けるエンティティを選択します。

> [!div class="mx-imgBorder"]
> ![ラベル付けが完了した機械学習エンティティ](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>ラベルが付けられたテキストをレビューする

ラベル付けした後、発話例を確認し、選択したテキスト範囲に、選択したエンティティと下線が付加されていることを確認します。 実線は、そのテキストにラベルが付けられたことを示しています。

> [!div class="mx-imgBorder"]
> ![ラベルが付けられた機械学習エンティティ](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>予測されたエンティティを確定する

テキストの範囲が点線のボックスで囲まれている場合、そのテキストは予測されていますが、"_ラベルはまだ付けられていない_" ことを示します。 予測をラベルに変更するには、発話行を選択してから、コンテキスト ツールバーで **[Confirm entites]\(エンティティの確定\)** を選択します。

## <a name="relabeling-over-existing-entities"></a>既存のエンティティに再度ラベルを付ける

既にラベルが付けられているテキストに再度ラベルを付けると、LUIS で既存のラベルが分割またはマージされる場合があります。

## <a name="labeling-for-punctuation"></a>句読点のラベル付け

句読点にラベルを付ける必要はありません。 句読点が発話予測に与える影響を制御するには、[アプリケーションの設定](luis-reference-application-settings.md)を使用します。

## <a name="unlabel-entities"></a>エンティティのラベルの解除

> [!NOTE]
> ラベルを解除できるのは、機械学習エンティティのみです。 正規表現エンティティ、リスト エンティティ、または事前構築済みエンティティにラベルを付けたり、ラベルを解除したりすることはできません。

エンティティのラベルを解除するには、対象のエンティティを選択し、埋め込みメニューで **[Unlabel]\(ラベル解除\)** を選択します。

> [!div class="mx-imgBorder"]
> ![エンティティのラベル付けの解除を示すスクリーンショット](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>親エンティティと子エンティティの自動ラベル付け

親エンティティにラベルを付けると、現在トレーニング済みのバージョンに基づいて予測できるサブエンティティにもラベルが付けられます。

サブエンティティにラベルを付けると、その親にもラベルが自動的に付けられます。

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>非機械学習エンティティの自動ラベル付け

非機械学習エンティティには、事前構築済みエンティティ、正規表現エンティティ、リスト エンティティ、pattern.any エンティティが含まれます。 これらには LUIS によって自動的にラベルが付けられるため、ユーザーが手動でラベルを付ける必要はありません。

## <a name="intent-prediction-errors"></a>意図予測のエラー

意図予測エラーは、現在のトレーニング済みアプリではその発話例が意図に対して予測されないことを示します。

意図の詳細ページで[これらのエラーを表示する](luis-how-to-add-intents.md#intent-prediction-errors)方法について確認してください。

## <a name="entity-prediction-errors"></a>エンティティ予測エラー

エンティティ予測エラーは、予測されたエンティティが、ラベル付けされたエンティティと一致しないことを示します。 これは、発話の横に表示される警告インジケーターによって視覚化されます。

> [!div class="mx-imgBorder"]
> ![機械学習エンティティのエンティティ パレット](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>次のステップ

[ダッシュボード](luis-how-to-use-dashboard.md)を使用し、[エンドポイントの発話をレビュー](luis-how-to-review-endpoint-utterances.md)して、アプリの予測品質を向上させます。
