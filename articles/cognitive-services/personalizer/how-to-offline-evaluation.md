---
title: オフライン評価の実行方法 - Personalizer
titleSuffix: Azure Cognitive Services
description: この記事では、オフライン評価を使用してご利用のアプリの有用性を測定し、学習ループを分析する方法について示します。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a473085f9c94ca42a75d01b342d60cc33836b096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "88244841"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>オフライン評価で学習ループを分析する

オフライン評価を完了し、その結果を理解する方法について説明します。

オフライン評価を使用すると、アプリケーションの既定の動作と比較した場合の Personalizer の効果を測定し、どの特徴が最もパーソナル化に寄与しているかを学習し、新しい機械学習の値を自動的に検出できます。

詳細については、「[オフライン評価](concepts-offline-evaluation.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* 構成済みの Personalizer ループ
* Personalizer ループには、代表的な量のデータが含まれている必要があります。意味のある評価結果を得るには、おおよそ、50,000 個以上のイベントをログに記録することをお勧めします。 必要に応じて、同じ評価で比較およびテストできる以前にエクスポートした _学習ポリシー_ ファイルを使用することもできます。

## <a name="run-an-offline-evaluation"></a>オフライン評価を実行する

1. [Azure portal](https://azure.microsoft.com/free/cognitive-services) で、Personalizer リソースを見つけます。
1. Azure portal で、 **[評価]** セクションに移動し、 **[評価の作成]** を選択します。
    ![Azure portal で、**[評価]** セクションに移動し、**[評価の作成]** を選択します。](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 次の値を構成します。

    * 評価名。
    * [開始日] と [終了日] - これらの日付では、評価で使用するデータの範囲を指定します。 このデータは、[[Data Retention]\(データ保有期間\)](how-to-settings.md) 値で指定したとおりにログに存在する必要があります。
    * [最適化の検出] を **[はい]** に設定します。

    > [!div class="mx-imgBorder"]
    > ![オフライン評価設定を選択する](./media/offline-evaluation/create-an-evaluation-form.png)

1. **[Ok]** を選択して評価を開始します。

## <a name="review-the-evaluation-results"></a>評価の結果を確認する

処理するデータの量、比較する学習ポリシーの数、および最適化が要求されたかどうかによっては、評価の実行に時間がかかる場合があります。

完了したら、評価の一覧から評価を選択し、 **[アプリケーションのスコアを他の潜在的な学習設定と比較します]** を選択できます。 現在の学習ポリシーのパフォーマンスと新しいポリシーを比較したい場合は、この機能を選択します。

1. [学習ポリシー](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)のパフォーマンスを確認します。

    > [!div class="mx-imgBorder"]
    > [![評価結果を確認する](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. モデルをデータに最適に向上させるポリシーを適用するには、 **[適用]** を選択します。

## <a name="next-steps"></a>次のステップ

* [オフライン評価のしくみ](concepts-offline-evaluation.md)についてさらに詳しく学習します。
