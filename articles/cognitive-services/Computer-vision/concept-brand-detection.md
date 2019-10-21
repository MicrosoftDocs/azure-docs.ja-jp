---
title: ブランド検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用したブランド/ロゴの検出に関連する概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: c3a80cbdd166f77681665ee3675c1a71ce3a9bd6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967086"
---
# <a name="detect-popular-brands-in-images"></a>画像内の人気のブランドの検出

ブランド検出は[オブジェクト検出](concept-object-detection.md)の特殊なモードであり、世界中のロゴを数千単位で収録しているデータベースを利用し、画像や動画の中の商用ブランドを識別します。 この機能は、たとえば、ソーシャル メディアで最も人気のあるブランドや、メディアのプロダクト プレイスメントの中で最も普及しているブランドを検出する目的で使用できます。

Computer Vision サービスは、与えられた画像の中にブランド ロゴがあるかどうかを検出します。ブランド ロゴがある場合、ブランドの名前、信頼度スコア、ロゴの境界ボックスの座標を返します。

組み込みのロゴ データベースは、家電メーカーや衣料メーカーなど、人気のブランドを網羅しています。 探しているブランドが Computer Vision サービスでは検出されないことが判明した場合、[Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) サービスで独自のロゴ検出機能を作成し、トレーニングすると検出できることがあります。

## <a name="brand-detection-example"></a>ブランド検出の例

次の JSON 応答は、Computer Vision がサンプル画像からブランドを検出するときに返す内容を示しています。

![Microsoft のラベルにロゴが印刷された赤いシャツ](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

ブランド検出機能では、ロゴの画像と図案化されたブランド名が 2 つの別個のロゴとして検出されることがあります。

![Microsoft のラベルとロゴが印刷された灰色のスウェット シャツ](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>API の使用

ブランド検出機能は [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API に含まれています。 ネイティブ SDK または REST を呼び出すことでこの API を呼び出すことができます。 `Brands` を **visualFeatures** クエリ パラメーターに追加します。 その後、完全な JSON 応答が得られたら、`"brands"` セクションのコンテンツを対象に文字列を解析します。

* [クイック スタート:Computer Vision .NET SDK](./quickstarts-sdk/csharp-sdk.md)
* [クイック スタート:画像の分析 (REST API)](./quickstarts/csharp-analyze.md)
