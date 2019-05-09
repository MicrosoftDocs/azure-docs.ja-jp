---
title: 成人向けコンテンツとわいせつなコンテンツについて - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用したイメージの成人向けコンテンツとわいせつなコンテンツの検出に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312539"
---
# <a name="detect-adult-and-racy-content"></a>成人向けコンテンツとわいせつなコンテンツの検出

Computer Vision では、イメージ内にある成人向けの素材を検出できます。このため、開発者は、そのようなイメージがソフトウェアに表示されないように制限できます。 0 ～ 1 のスコアを持つコンテンツ フラグが適用されるため、開発者は、自分の設定に応じて結果を解釈できます。 

> [!NOTE]
> この機能は、 [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) サービスでも利用できます。 より厳格なコンテンツ モデレーション シナリオ (テキスト モデレーションやヒューマン レビュー ワークフローなど) に対するソリューションが必要な場合は、このサービスをご確認ください。

## <a name="content-flag-definitions"></a>コンテンツ フラグの定義

**成人向け**イメージは、性的な内容で、その多くがヌードや性的な行為を描写したものと定義されています。 

**わいせつ**なイメージは、性的なものを想起させるものの、**成人向け** とタグ付けされたイメージに比べると性的に明白な内容が少ないものと定義されています。 

## <a name="identify-adult-and-racy-content"></a>成人向けコンテンツとわいせつなコンテンツの識別

[Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API。

イメージ分析メソッドでは、成人向けコンテンツとわいせつなコンテンツのそれぞれを示すメソッドの JSON 応答で、`isAdultContent` と `isRacyContent` の 2 つのブール プロパティが返されます。 このメソッドでは、`adultScore` と `racyScore` の 2 つのプロパティも返されます。これは、それぞれ成人向けコンテンツとわいせつなコンテンツを識別するための信頼度スコアを表しています。

## <a name="next-steps"></a>次の手順

[ドメイン固有のコンテンツの検出](concept-detecting-domain-content.md)と[顔の検出](concept-detecting-faces.md)についての概念を学習します。
