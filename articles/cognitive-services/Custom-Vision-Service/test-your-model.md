---
title: Test and Retrain Your Model | Microsoft Docs
description: How to quickly test an image and then add it to the model and retrain the model.
services: cognitive-services
author: gitbeams
manager: juliakuz
ms.service: cognitive-services
ms.technology: custom vision service
ms.topic: article
ms.date: 04/28/2017
ms.author: gitbeams
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 549a7de09ca8cf5951565687c9aa33c34fba3fa5
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="test-and-retrain-your-model"></a>モデルのテストと再トレーニング

モデルをトレーニングした後、ローカルに保存されている画像またはオンラインの画像を使ってすぐにテストできます。このテストでは、直近でトレーニングされたイテレーションを使用します。

## <a name="test-your-model"></a>モデルのテスト :

1. トップメニューバーの右側にある **Quick Test** をクリックします。 **Quick Test** というウインドウが表示されます。

    ![The Quick Test button is shown in the upper right corner of the window.](./media/test-your-model/quick-test-button.png)

2. **Quick Test** ウインドウで、 **Submit Image** フィールドをクリックし、テストに使用する画像のURLを入力します。 ローカルに保存されている画像を使用したい場合は、**Browse local files** ボタンをクリックし、ローカルイメージファイルを選択します。

    ![The Quick Test window is shown. It has a URL field and a button for selecting local files.](./media/test-your-model/quick-test-results.png)

選択した画像がページの中央に表示されます。結果は、**Tags** と **Probability** の2列の表形式で画像の下に表示されます。 結果を確認し、**Quick Test** ウィンドウを閉じることができます。

このテスト画像をモデルに追加し、モデルを再トレーニングすることができます。

## <a name="add-a-test-image-and-retrain-your-model"></a>テスト画像の追加と再トレーニング : 

以下の手順に従って、クイックテストで使った画像をモデルに追加し、モデルを再トレーニングすることができます。しかし、1つのイメージを追加してモデルを再トレーニングしても、パフォーマンスは大幅に向上しません。 最良の結果を得るには、モデルを再トレーニングする前に多量の画像を追加しましょう。

1. トップメニューバーの **PREDICTIONS** タブをクリックします。
2. クイックテストで使った画像をクリックします。新しいウインドウで、画像が中央に表示されます。
3. 新しいウインドウで **My Tags** ドロップダウンリストから正しいタグを画像に割り当てます。ドロップダウンリストに適切なタグがない場合は、新しいタグを入力して **My Tags** フィールドの右側の **+** をクリックします。
4. ウインドウ下部の **Save and close** ボタンをクリックします。
5. トップメニューバーにある グリーンの **Train** ボタンをクリックします。