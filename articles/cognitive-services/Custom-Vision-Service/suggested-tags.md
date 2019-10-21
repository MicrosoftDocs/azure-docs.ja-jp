---
title: 推奨タグを使用して画像により早くラベルを付ける
titleSuffix: Azure Cognitive Services
description: このガイドでは、Custom Vision モデルをトレーニングするときに、推奨タグを使用してより迅速に多数の画像にラベルを付ける方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: pafarley
ms.openlocfilehash: 31b8dfc234ac99d6f04061d6596e3dc8113e8d0f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213829"
---
# <a name="label-images-faster-with-suggested-tags"></a>推奨タグを使用して画像により早くラベルを付ける

このガイドでは、Custom Vision モデルをトレーニングするときに、推奨タグ機能を使用してより迅速に多数の画像にラベルを付ける方法について説明します。 

ユーザーが Custom Vision モデルの画像にタグを付けるとき、サービスではモデルの最新のトレーニング済みイテレーションを使用して、タグなし画像のラベルを予測します。 次に、選択した信頼度しきい値と予測不確実性に基づいて、これらの予測が推奨タグとして表示されます。 その後、ユーザーは提案を確認または変更し、トレーニング用の画像を手動でタグ付けする処理を高速化できます。

## <a name="when-to-use-suggested-tags"></a>推奨タグを使用する場合

次の制限事項に注意してください。

* コンテンツが既にトレーニングされている画像の推奨タグのみを要求してください。 トレーニングを開始したばかりの新しいタグについて提案を取得しないでください。
* 推奨タグを使用できるのは、タグ付けされていない画像に対してのみです。既にタグ付けされた画像に対する追加のタグの提案を取得することはできません。

> [!IMPORTANT]
> 推奨タグ機能では、通常の予測と同じ[価格モデル](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)を使用します。 画像のセットに対して推奨タグを初めてトリガーするときは、予測呼び出しの場合と同じ料金が発生します。 その後、サービスでは選択した画像の結果が 30 日間データベースに格納され、その期間内はいつでも無料でそれらにアクセスできます。 30 日後に、推奨タグを再度要求すると、料金が発生します。

## <a name="suggested-tags-workflow"></a>推奨タグのワークフロー

次の手順で、推奨タグ機能の使用方法を示します。

1. すべてのトレーニング画像を Custom Vision プロジェクトにアップロードします。
1. データ セットの一部にラベルを付け、タグごとに同じ数の画像を選択します。
    > [!TIP]
    > 後で提案を必要とするすべてのタグを使用するようにしてください。
1. トレーニング プロセスを開始します。
1. トレーニングが完了したら、 **[タグなし]** ビューに移動し、左側のウィンドウの **[Get suggested tags]\(推奨タグを取得\)** ボタンを選択します。
    > [!div class="mx-imgBorder"]
    > ![推奨タグのボタンはタグなし画像のタブの下に表示されます。](./media/suggested-tags/suggested-tags-button.png)
1. 提案を必要とする画像のセットを選択します。 タグなし画像の一部に対する最初のタグの提案のみを取得する必要があります。 このプロセスを反復処理するにつれて、タグの提案が改善されます。
1. 推奨タグを確認し、正しくないものがあれば修正します。
    > [!TIP]
    > 推奨タグ付きの画像は、予測不確実性によって並べ替えられます (低い値は信頼度が高いことを示します)。 並べ替え順序は、 **[Sort by uncertainty]\(不確実性で並べ替え\)** オプションを使用して変更できます。 順序を **[high to low]\(高から低\)** に設定すると、まず、不確実性の高い予測を修正してから、不確実性の低いものをすばやく確認できます。
    * 画像分類プロジェクトでは、タグの選択と確認をバッチで行うことができます。 特定の推奨タグによってビューをフィルター処理し、誤ってタグ付けされている画像を選択解除してから、残りをバッチで確認します。
        > [!div class="mx-imgBorder"]
        > ![フィルター設定された IC のバッチ モードで推奨タグが表示されています。](./media/suggested-tags/ic-batch-mode.png)

        また、ギャラリーから画像を選択して、推奨タグを個別画像モードで使用することもできます。

        ![推奨タグが IC の個別画像モードで表示されています。](./media/suggested-tags/ic-individual-image-mode.png)
    * 物体検出プロジェクトでは、バッチ確認はサポートされていませんが、より系統的なラベル付けのために、推奨タグでフィルター処理や並べ替えを行うことができます。 タグなし画像の縮小版には、推奨タグの位置を示す境界ボックスのオーバーレイが表示されます。 推奨タグ フィルターを選択しない場合は、すべてのタグなし画像が境界ボックスのオーバーレイなしで表示されます。
        > [!div class="mx-imgBorder"]
        > ![フィルター設定された OD のバッチ モードで推奨タグが表示されています。](./media/suggested-tags/od-batch-mode.png)

        物体検出タグを確認するには、ギャラリー内の個々の画像にそれらを適用する必要があります。

        ![推奨タグが OD の個別画像モードで表示されています。](./media/suggested-tags/od-individual-image-mode.png)
1. トレーニング プロセスを再開します。
1. 提案の品質に満足できるまで、前の手順を繰り返します。

## <a name="next-steps"></a>次の手順

クイック スタートに従って、Custom Vision プロジェクトの作成とトレーニングを開始します。

* [分類器の構築](getting-started-build-a-classifier.md)
* [オブジェクト検出器の構築](get-started-build-detector.md)