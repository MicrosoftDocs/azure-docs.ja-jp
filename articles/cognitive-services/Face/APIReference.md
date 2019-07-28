---
title: API リファレンス - Face API
titleSuffix: Azure Cognitive Services
description: API リファレンスでは、Person、LargePersonGroup/PersonGroup、LargeFaceList/FaceList、Face アルゴリズム API に関する情報を説明します。
services: cognitive-services
author: stevemsft
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: reference
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f4258f34bb7d353ee4e76f4675f4ef672a4a8c78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816399"
---
# <a name="api-reference"></a>API リファレンス

Azure Face API は、顔検出および顔認識のアルゴリズムを提供するクラウド ベースの API です。 Face API は、以下のカテゴリで構成されます。

- Face アルゴリズム API:[検出](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[類似アイテムの検索](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[検証](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)、[識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[グループ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)などのコア機能をカバーします。
- [FaceList API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b):[類似アイテムの検索](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)の FaceList の管理に使用します。
- [LargePersonGroup Person API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40):[識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)の LargePersonGroup の人の顔の管理に使用します。
- [LargePersonGroup API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d):[識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)の LargePersonGroup データセットの管理に使用します。
- [LargeFaceList API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc):[類似アイテムの検索](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)の LargeFaceList の管理に使用します。
- [PersonGroup Person API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c):[識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)の PersonGroup の人の顔の管理に使用します。
- [PersonGroup API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244):[識別](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)の PersonGroup データセットの管理に使用します。
- [Snapshot API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-take):サブスクリプション間のデータ移行用のスナップショットを管理するために使用します。
