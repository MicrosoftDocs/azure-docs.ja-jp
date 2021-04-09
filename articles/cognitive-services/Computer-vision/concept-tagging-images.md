---
title: コンテンツ タグ - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API の画像タグ付け機能に関連する概念について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a75e55cd51890173b6733ab5f8c0f1bcbfeef0f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96532977"
---
# <a name="applying-content-tags-to-images"></a>コンテンツ タグの画像への適用

Computer Vision では、数千個の認識可能なオブジェクト、生物、風景、動作に基づいて、タグが返されます。 タグが不明瞭な場合や、常識的でない場合は、API 応答により、既知の設定のコンテキストでタグの意味を明確にするための "ヒント" が示されます。 タグは分類として整理されず、継承階層は存在しません。 一連のコンテンツ タグでは、完全な文章で書式設定された人間が判読できる言語として表示されるイメージの "説明" の基礎が形成されます。 現時点では、イメージの説明でサポートされている言語は英語のみであることに注意してください。

イメージのアップロード後、またはイメージ URL の指定後に、Computer Vision のアルゴリズムにより、イメージで識別されたオブジェクト、生物、動作に基づいて、タグが出力されます。 タグ付けの対象は、前景の人物などの主題に限らず、背景 (屋内または屋外)、家具、道具、植物、動物、アクセサリ、ガジェットなども含まれます。

## <a name="image-tagging-example"></a>イメージのタグ付けの例

次の JSON 応答は、サンプル イメージで検出された視覚的特徴のタグを付けるときに、Computer Vision から返される内容を示します。

![青い家と前庭](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>API の使用

タグ付け機能は [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API に含まれています。 ネイティブ SDK または REST を呼び出すことでこの API を呼び出すことができます。 `Tags` を **visualFeatures** クエリ パラメーターに追加します。 その後、完全な JSON 応答が得られたら、`"tags"` セクションのコンテンツを対象に文字列を解析します。

* [クイック スタート: Computer Vision REST API またはクライアント ライブラリ](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>次のステップ

[イメージの分類](concept-categorizing-images.md)および[イメージの説明](concept-describing-images.md)に関連する概念を学習します。
