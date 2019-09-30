---
title: オフライン評価 - Personalizer
titleSuffix: Azure Cognitive Services
description: オフライン評価で学習ループを分析する方法に関する説明
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: diberry
ms.openlocfilehash: a157b36ad2b4f5a7f2e50a9609d8acd621efd5a8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884501"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>オフライン評価で学習ループを分析する

オフライン評価を完了し、その結果を理解する方法について説明します。

オフライン評価を使用すると、アプリケーションの既定の動作と比較した場合の Personalizer の効果を測定し、どの特徴が最もパーソナル化に寄与しているかを学習し、新しい機械学習設定を自動的に検出できます。

詳細については、「[オフライン評価](concepts-offline-evaluation.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

* 構成済みの Personalizer ループ
* Personalizer ループには、代表的な量のデータが含まれている必要があります。意味のある評価結果を得るには、おおよそ、50,000 個以上のイベントをログに記録することをお勧めします。 必要に応じて、同じ評価で比較およびテストできる以前にエクスポートした_学習ポリシー_ ファイルを使用することもできます。

## <a name="steps-to-start-a-new-offline-evaluation"></a>新しいオフライン評価を開始する手順

1. [Azure portal](https://azure.microsoft.com/free/) で、Personalization リソースを見つけます。
1. Azure portal で、 **[評価]** セクションに移動し、 **[評価の作成]** を選択します。
    ![Azure portal で、**[評価]** セクションに移動し、**[評価の作成]** を選択します。](./media/offline-evaluation/create-new-offline-evaluation.png)
1. 次の設定を選択します。

    * 評価名
    * 開始日と終了日 - これらは過去の日付であり、評価で使用するデータの範囲を指定します。 このデータは、[[Data Retention]\(データ保有期間\)](how-to-settings.md) 設定で指定したとおりにログに存在する必要があります。
    * **[はい]** に設定された [最適化の検出]

    ![オフライン評価設定を選択する](./media/offline-evaluation/create-an-evaluation-form.png)

1. **[Ok]** を選択して評価を開始します。 

## <a name="results"></a>結果

処理するデータの量、比較する学習ポリシーの数、および最適化が要求されたかどうかによっては、評価の実行に時間がかかる場合があります。

完了したら、評価のリストから評価を選択できます。 

学習ポリシーの比較には、以下が含まれます。

* **オンライン ポリシー**:Personalizer で使用されている現在の学習ポリシー
* **ベースライン**:アプリケーションの既定値 (Rank 呼び出しで送信される最初のアクションによって決定される)
* **ランダム ポリシー**:指定されたアクションからランダムに選択したアクションを常に返す仮想的な優先度付け動作。
* **カスタム ポリシー**:評価の開始時にアップロードされる追加の学習ポリシー。
* **最適化されたポリシー**:最適化されたポリシーを検出するオプションを指定して評価を開始した場合は、それも比較され、ダウンロードしたりオンライン学習ポリシーにしたりして、現在のポリシーを置き換えることができます。

![オフライン評価設定の結果グラフ](./media/offline-evaluation/evaluation-results.png)

アクションおよびコンテキストに対する[特徴](concepts-features.md)の有効性。

## <a name="next-steps"></a>次の手順

* [オフライン評価の仕組み](concepts-offline-evaluation.md)を確認してください。
