---
title: 言語サポート - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision 機能でサポートされる自然言語の一覧。
services: cognitive-services
author: patrickfarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60759607"
---
# <a name="language-support-for-computer-vision"></a>Computer Vision の言語サポート

Computer Vision の一部の機能では、複数の言語がサポートされています。ここに記載されていない機能では、英語だけがサポートされています。

## <a name="text-recognition"></a>テキストの認識

Computer Vision では、さまざまな言語のテキストを認識できます。 具体的には、[OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API はさまざまな言語をサポートしますが、[Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API および [Recognize Text](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API は英語のみをサポートします。 この機能および各 API の利点の詳細については、「[印刷されたテキストと手書きのテキストの認識](concept-recognizing-text.md)」を参照してください。

OCR は入力素材の言語を自動的に検出するため、API 呼び出しで言語コードを指定する必要はありません。 ただし、言語コードは JSON 応答の `"language"` ノードの値として常に返されます。

|言語| 言語コード | OCR API |
|:-----|:----:|:-----:|
|アラビア語 | `ar`|✔ |
|中国語 (簡体字) | `zh-Hans`|✔ |
|中国語 (繁体字) | `zh-Hant`|✔ |
|チェコ語 | `cs` |✔ |
|デンマーク語 | `da` |✔ |
|オランダ語 | `nl` |✔ |
|英語 | `en` |✔ |
|フィンランド語 | `fi` |✔ |
|フランス語 | `fr` |✔ |
|ドイツ語 | `de` |✔ |
|ギリシャ語 | `el` |✔ |
|ハンガリー語 | `hu` |✔ |
|イタリア語 | `it` |✔ |
|日本語 | `ja` |✔ |
|韓国語 | `ko` |✔ |
|ノルウェー語 | `nb` |✔ |
|ポーランド語 | `pl` |✔ |
|ポルトガル語 | `pt` |✔ |
|ルーマニア語 | `ro` |✔ |
|ロシア語 | `ru` |✔ |
|セルビア語 (キリル文字) | `sr-Cyrl` |✔ |
|セルビア語 (ラテン) | `sr-Latn` |✔ |
|スロバキア語 | `sk` |✔ |
|スペイン語 | `es` |✔ |
|スウェーデン語 | `sw` |✔ |
|トルコ語 | `tr` |✔ |

## <a name="image-analysis"></a>画像分析

[Analyze - Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API の一部のアクションは、`language` クエリ パラメーターで指定された他の言語で結果を返すことができます。 他のアクションは、どの言語が指定されていても英語で結果を返し、その他のアクションはサポートされていない言語に対して例外をスローします。 アクションは `visualFeatures` および `details` クエリ パラメーターで指定されます。画像分析で実行できるすべてのアクションの一覧については、[概要](home.md)に関するページを参照してください。

|言語 | 言語コード | Categories | Tags | 説明 | 成人向けコンテンツ | ブランド | 色 | 顔 | ImageType | Objects | 有名人 | ランドマーク |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|中国語 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|英語 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|日本語 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|ポルトガル語 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|スペイン語 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>次の手順

このガイドで説明されている Computer Vision 機能を使ってみます。

* [ローカル画像の分析 (REST)](./quickstarts/csharp-analyze.md)
* [印刷されたテキストの抽出 (REST)](./quickstarts/csharp-print-text.md)