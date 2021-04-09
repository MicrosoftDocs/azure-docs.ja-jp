---
title: 成人向けコンテンツ、わいせつなコンテンツ、不快なコンテンツ - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用した画像の成人向けコンテンツの検出に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d5961ecae2fbc154ae6f1acd74df2bb74024fa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96532620"
---
# <a name="detect-adult-content"></a>成人向けコンテンツを検出する

Computer Vision では、イメージ内にある成人向けの素材を検出できます。このため、開発者は、これらのイメージがソフトウェアに表示されないように制限できます。 0 - 1 のスコアを持つコンテンツ フラグが適用されるため、開発者は、自分の設定に応じて結果を解釈できます。

> [!NOTE]
> この機能の多くは、[Azure Content Moderator](../content-moderator/overview.md) サービスでも提供されています。 より厳格なコンテンツ モデレーション シナリオ (テキスト モデレーションやヒューマン レビュー ワークフローなど) に対するソリューションが必要な場合は、このサービスをご確認ください。

## <a name="content-flag-definitions"></a>コンテンツ フラグの定義

"成人向け" 分類には、次のようなカテゴリが含まれます。

- **成人向け** 画像は、露骨に性的な内容で、その多くがヌードや性的な行為を描写したものです。
- **わいせつ** な画像は、性的なものを想起させるものの、**成人向け** とタグ付けされたイメージに比べると性的に明白な内容が少ないものです。
- **不快な** 画像は、血が描写されているものです。

## <a name="use-the-api"></a>API の使用

成人向けコンテンツは、[Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API を使用して検出できます。 **visualFeatures** クエリ パラメーターに値 `Adult` を追加すると、API の JSON 応答で、3 つのブール型プロパティ `isAdultContent`、`isRacyContent`、`isGoryContent` が返されます。 メソッドでは、それぞれのカテゴリに対する信頼度スコアが 0 から 1 の範囲の値で表されている、対応するプロパティ `adultScore`、`racyScore`、`goreScore` も返されます。

- [クイック スタート: Computer Vision REST API またはクライアント ライブラリ](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
