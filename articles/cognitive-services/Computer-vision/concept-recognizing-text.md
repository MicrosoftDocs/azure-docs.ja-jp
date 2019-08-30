---
title: 印刷されたテキストと手書きのテキストの認識 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 画像に含まれる印刷されたテキストと手書きのテキストの Computer Vision API を使用した認識に関する概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e37b4c02692575f76582e8f62c3aff00b57424ed
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946293"
---
# <a name="recognize-printed-and-handwritten-text"></a>印刷されたテキストと手書きのテキストの認識

Computer Vision では、画像に出現する印刷されたテキストまたは手書きのテキストを検出して抽出する複数のサービスが提供されています。 これは、ノート取り、医療記録、セキュリティ、銀行取引などのさまざまなシナリオで役立ちます。 以下の 3 つのセクションでは、3 つの異なるテキスト認識 API の詳細を説明します。それぞれ、異なるユース ケースに最適化されています。

## <a name="read-api"></a>Read API

Read API では、最新の認識モデルを使用して画像内のテキスト コンテンツが検出され、識別されたテキストはコンピューターで読み取り可能な文字ストリームに変換されます。 これは、テキストが多く含まれる画像 (デジタル スキャンされたドキュメントなど) や、視覚ノイズが多く含まれる画像向けに最適化されています。 各テキスト行に対してどの認識モデルを使用するかが決定されるため、印刷されたテキストと手書きのテキストの両方を含む画像がサポートされます。 大きなドキュメントは結果を返すまでに数分かかる場合があるため、Read API は非同期的に実行されます。

読み取り操作では、認識された単語の元の行グループが出力で維持されます。 各行は境界ボックスの座標を持ち、行内の各単語もそれ自体の座標を持っています。 単語が低い信頼度で認識された場合は、その情報も伝えられます。 詳しくは、[Read API のリファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)をご覧ください。

> [!NOTE]
> この機能は、英語のテキストに対してのみ使用できます。

### <a name="image-requirements"></a>イメージの要件

Read API は、次の要件を満たす画像で動作します。

- 画像は、JPEG、PNG、BMP、PDF、または TIFF 形式である必要があります。
- 画像の寸法は、50 x 50 から 10000 x 10000 ピクセルの間である必要があります。 PDF ページは、17 x 17 インチ以下である必要があります。
- 画像のファイル サイズは、20 メガバイト (MB) 未満である必要があります。

### <a name="limitations"></a>制限事項

Free レベルのサブスクリプションを使用している場合、Read API では PDF または TIFF ドキュメントの最初の 2 ページのみが処理されます。 有料サブスクリプションでは、最大 200 ページが処理されます。 また、API で検出される行数は、1 ページあたり最大 300 行であることにも注意してください。

## <a name="ocr-optical-character-recognition-api"></a>OCR (光学式文字認識) API

Computer Vision の光学式文字認識 (OCR) API は Read API に似ていますが、同期的に実行され、大きいドキュメントには最適化されていません。 以前の認識モデルを使用しますが、より多くの言語で動作します。サポートされている言語の完全な一覧については、[言語サポート](language-support.md#text-recognition)に関するページを参照してください。

必要に応じて、OCR は、イメージの水平軸を中心とした回転のオフセットを度数で返すことによって、認識されたテキストの回転を修正します。 また、OCR では、以下の図に示すように、各単語のフレーム座標も提供されます。

![回転されている画像と、読み取られて輪郭が描かれたテキスト](./Images/vision-overview-ocr.png)

詳しくは、[OCR のリファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)をご覧ください。

### <a name="image-requirements"></a>イメージの要件

OCR API は、次の要件を満たす画像で動作します。

* 画像は、JPEG、PNG、GIF、または BMP 形式になっている必要があります。
* 入力イメージのサイズは、50 x 50 から 4200 x 4200 ピクセルまでにする必要があります。
* イメージ内のテキストは、90 度の倍数に、40 度までの小角度を加えた度数で回転させることができます。

### <a name="limitations"></a>制限事項

テキストが主な写真では、部分的に認識された単語により誤検知が発生する場合があります。 一部の写真、特にテキストのない写真では、イメージの種類によって、精度が異なる場合があります。

## <a name="recognize-text-api"></a>Recognize Text API

> [!NOTE]
> Recognize Text API は非推奨であり、Read API が優先されます。 Read API は同様の機能を備え、PDF、TIFF、および複数ページのファイルを処理するように更新されています。

Recognize Text API は OCR に似ていますが、非同期的に実行され、更新された認識モデルを使用します。 詳しくは、[Recognize Text API のリファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)をご覧ください。

### <a name="image-requirements"></a>イメージの要件

Recognize Text API は、次の要件を満たす画像で動作します。

- 画像は、JPEG、PNG、または BMP 形式になっている必要があります。
- 画像の寸法は、50 x 50 から 4200 x 4200 ピクセルの間である必要があります。
- 画像のファイル サイズは、4 メガバイト (MB) 未満である必要があります。

## <a name="limitations"></a>制限事項

テキスト認識操作の精度は、画像の品質によって異なります。 次の要因によって読み取りが不正確になる可能性があります。

* ぼやけたイメージ。
* 手書きまたは筆記体のテキスト。
* アーティスティックなフォント スタイル。
* 小さなテキスト サイズ。
* 複雑な背景、影、テキスト上のグレア、射影ひずみ。
* 単語の先頭の大文字のサイズが大きすぎるか欠落している。
* 下付き、上付き、または取り消し線テキスト。

## <a name="next-steps"></a>次の手順

[印刷されたテキストの抽出 (OCR)](./quickstarts/csharp-print-text.md) に関するクイック スタートに従って、簡単な C# アプリにテキストの認識を実装します。
