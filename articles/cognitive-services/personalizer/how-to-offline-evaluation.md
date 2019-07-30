---
title: オフライン評価 - Personalizer
titleSuffix: Azure Cognitive Services
description: オフライン評価で学習ループを分析する方法に関する説明
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b719e6e693471415350007a4f4fabed917b8e12d
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722316"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>オフライン評価で学習ループを分析する方法


オフライン評価を完了し、その結果を理解する方法について説明します。

オフライン評価を使用すると、アプリケーションの既定の動作と比較した場合の Personalizer の効果を測定し、どの特徴が最もパーソナル化に寄与しているかを学習し、新しい機械学習設定を自動的に検出できます。

詳細については、「[オフライン評価](concepts-offline-evaluation.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

1. Personalizer ループが構成されている必要があります。
1. 意味のある評価結果を得るには、Personalizer ループのログに少なくとも 50,000 のイベントが含まれている必要があります。

必要に応じて、同じ評価で比較およびテストできる以前にエクスポートした_学習ポリシー_ ファイルを使用することもできます。

## <a name="steps-to-start-a-new-offline-evaluation"></a>新しいオフライン評価を開始する手順

1. Azure portal で目的のパーソナル化ループ リソースを見つけます。
1. [評価] セクションに移動します。
1. [New Evaluation] (新しい評価) をクリックします。
1. オフライン評価の開始日と終了日を選択します。 これらは過去の日付であり、評価で使用するデータの範囲を指定します。 このデータは、[[Data Retention]\(データ保有期間\)](how-to-settings.md) 設定で指定したとおりにログに存在する必要があります。
1. 必要に応じて、独自の学習ポリシーをアップロードできます。 
1. この期間に観察されたユーザーの動作に基づいて、最適化された学習ポリシーを Personalizer で作成するかどうかを指定します。
1. 評価を開始します。

## <a name="results"></a>結果

処理するデータの量、比較する学習ポリシーの数、および最適化が要求されたかどうかによっては、評価の実行に時間がかかる場合があります。

完了すると、次の結果が表示されます。

1. 次の項目を含む学習ポリシーの比較:
    * **オンライン ポリシー**:Personalizer で使用されている現在の学習ポリシー
    * **ベースライン**:アプリケーションの既定値 (Rank 呼び出しで送信される最初のアクションによって決定される)
    * **ランダム ポリシー**:指定されたアクションからランダムに選択したアクションを常に返す仮想的な優先度付け動作。
    * **カスタム ポリシー**:評価の開始時にアップロードされる追加の学習ポリシー。
    * **最適化されたポリシー**:最適化されたポリシーを検出するオプションを指定して評価を開始した場合は、それも比較され、ダウンロードしたりオンライン学習ポリシーにしたりして、現在のポリシーを置き換えることができます。

1. アクションおよびコンテキストに対する[特徴](concepts-features.md)の有効性。


## <a name="more-information"></a>詳細情報

* [オフライン評価の仕組み](concepts-offline-evaluation.md)を確認してください。