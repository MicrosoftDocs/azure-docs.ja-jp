---
title: オブジェクトの検出 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API のオブジェクト検出機能に関連する概念と、使用方法および制限について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4269209017ecc0afa740bc3ed56cbdcbd915201e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96533844"
---
# <a name="detect-common-objects-in-images"></a>画像内の一般的なオブジェクトを検出する

オブジェクトの検出は[タグ付け](concept-tagging-images.md)に似ていますが、API で返されるのは、検出された各オブジェクトの境界ボックスの座標 (ピクセル単位) です。 たとえば犬や猫、人物が画像に含まれている場合、検出操作によって、それらのオブジェクトが、画像における対応する座標と共に一覧表示されます。 この機能を使用すれば、画像に含まれるオブジェクト間の関係を処理することができます。 画像内に同じタグのインスタンスが複数存在するかどうかも判別できます。

Detect API は、画像内で特定されたオブジェクトや生物に応じてタグを適用します。 この時点では、タグ付けの分類とオブジェクト検出の分類との間に、正式な関係は存在しません。 概念レベルで言えば、Detect API はオブジェクトや生物を検出するだけであるのに対し、Tag API では、コンテキスト上の用語 ("indoor" など) も対象となり、それらの位置を境界ボックスで特定することはできません。

## <a name="object-detection-example"></a>オブジェクト検出の例

次の JSON 応答は、Computer Vision がサンプル画像からオブジェクトを検出するときに返す内容を示しています。

![キッチンで Microsoft Surface デバイスを使用する女性](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>制限事項

検知漏れ (見落とされたオブジェクト) や詳細の利用制限の影響を回避または軽減できるように、オブジェクト検出の制限事項に注意することが重要です。

* オブジェクトが小さい (画像の 5 % 未満) 場合、通常、それらのオブジェクトは検出されません。
* オブジェクトが近くに配置されている場合 (積み重ねられた皿など)、通常、それらのオブジェクトは検出されません。
* オブジェクトは、ブランド名や製品名 (店の棚にある各種のソーダなど) によって区別されません。 ただし、[ブランド検出](concept-brand-detection.md)機能を使用すると画像からブランド情報を取得できます。

## <a name="use-the-api"></a>API の使用

オブジェクト検出機能は [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API に含まれています。 ネイティブ SDK または REST を呼び出すことでこの API を呼び出すことができます。 `Objects` を **visualFeatures** クエリ パラメーターに追加します。 その後、完全な JSON 応答が得られたら、`"objects"` セクションのコンテンツを対象に文字列を解析します。

* [クイック スタート: Computer Vision REST API またはクライアント ライブラリ](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
