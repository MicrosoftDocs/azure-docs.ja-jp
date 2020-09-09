---
title: インクルード ファイル
ms.topic: include
ms.custom: include file
ms.date: 5/17/2020
ms.openlocfilehash: 79a45825be66b44d869ef741917436de87a1f5d0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653213"
---
正規化の[概念](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)と、[version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API の使用方法を学習して、これらの設定を更新したり、LUIS ポータルの **[設定]** ページの **[管理]** セクションを使用したりします。


|UI 設定|API 設定|Information|
|--|--|--|
|Use non-deterministic training\(非決定的トレーニングを使用する\)|`UseAllTrainingData`|トレーニングでは、ネガティブ サンプリングのごく一部を使用します。 小規模なネガティブ サンプリングではなくすべてのデータを使用する場合は、`true` に設定します。 |
|Normalize diacritics\(分音記号を正規化する\)|`NormalizeDiacritics`|分音記号を正規化すると、発話内の分音記号を持つ文字が通常の文字に置き換えられます。 この設定は、分音記号をサポートする[言語](../luis-reference-application-settings.md#diacritics-normalization)でのみ利用できます。|
|Normalize punctuation\(句読点を正規化する\)|`NormalizePunctuation`|句読点を正規化すると、モデルがトレーニングされる前、およびエンドポイント クエリが予測される前に、発話から句読点が削除されます。|
|Normalize word forms\(単語形式を正規化する\)|`NormalizeWordForm`|ルートを超えた単語形式を無視します。|
