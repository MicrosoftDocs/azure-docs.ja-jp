---
title: 成人向けコンテンツ、わいせつなコンテンツ、不快なコンテンツ - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用したイメージの成人向けコンテンツの検出に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718512"
---
# <a name="detect-adult-content"></a>成人向けコンテンツを検出する

Computer Vision では、イメージ内にある成人向けの素材を検出できます。このため、開発者は、これらのイメージがソフトウェアに表示されないように制限できます。 0 ～ 1 のスコアを持つコンテンツ フラグが適用されるため、開発者は、自分の設定に応じて結果を解釈できます。

> [!NOTE]
> この機能の多くは、[Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) サービスでも提供されています。 より厳格なコンテンツ モデレーション シナリオ (テキスト モデレーションやヒューマン レビュー ワークフローなど) に対するソリューションが必要な場合は、このサービスをご確認ください。

## <a name="content-flag-definitions"></a>コンテンツ フラグの定義

"成人向け" 分類内には、次のようなカテゴリがあります。

- **成人向け**イメージは、露骨に性的な内容で、その多くがヌードや性的な行為を描写したものと定義されています。
- **わいせつ**なイメージは、性的なものを想起させるものの、**成人向け** とタグ付けされたイメージに比べると性的に明白な内容が少ないものと定義されています。
- **不快な**イメージは、血が描写されているものとして定義されています。

## <a name="use-the-api"></a>API の使用

成人向けコンテンツは、[Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API を使用して検出できます。 **visualFeatures** クエリ パラメーターに値 `Adult` を追加すると、API の JSON 応答で、3 つのブール型プロパティ `isAdultContent`、`isRacyContent`、`isGoryContent` が返されます。 メソッドでは、それぞれのカテゴリに対する信頼度スコアが 0 から 1 の範囲の値で表されている、対応するプロパティ `adultScore`、`racyScore`、`goreScore` も返されます。

- [クイック スタート:画像の分析 (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [クイック スタート:画像の分析 (REST API)](./quickstarts/csharp-analyze.md)
