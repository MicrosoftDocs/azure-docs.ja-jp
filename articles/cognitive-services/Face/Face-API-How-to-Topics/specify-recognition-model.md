---
title: 認識モデルの指定方法 - Face
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Face アプリケーションで使用する認識モデルを選択する方法を示します。
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: cd4ed992557a3c333919b63cc36757beb2f70454
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433558"
---
# <a name="specify-a-face-recognition-model"></a>顔認識モデルを指定する

このガイドでは、Azure Face サービスを使用した顔の検出、識別、および類似検索のための顔認識モデルを指定する方法を示します。

Face サービスでは、機械学習モデルを使用して、画像内の人間の顔に対して操作を実行します。 お客様からのフィードバックや研究の進展に基づいてモデル精度の改善を続けており、これらの改善をモデルの更新として提供します。 開発者には、使用する顔認識モデルのバージョンを指定する選択肢があり、ユースケースに最も適したモデルを選択できます。

Azure Face サービスには、4 つの認識モデルが用意されています。 _recognition_01_ モデル (2017 年公開)、_recognition_02_ モデル (2019 年公開)、_recognition_03_ (2020 年公開) で作成された FaceList または **PersonGroup** を使用しているお客様に下位互換性を保証するために、これらのモデルも引き続きサポートされています。 **FaceList** または **PersonGroup** は常に、その作成時に指定された認識モデルを使用します。新しい顔がリストに追加されると、その顔はこのモデルに関連付けられます。 これは作成後に変更することはできません。また、対応する認識モデルを、対応する **FaceList** または **PersonGroup** と一緒に使用する必要があります。

都合のよいときに新しい認識モデルに移行することはできますが、選択した認識モデルを使用して、新しい FaceList と PersonGroup を作成する必要があります。

_recognition_04_ モデル (2021 年公開) は、現在使用できる最も正確なモデルです。 新しいお客様の場合は、このモデルを使用することをお勧めします。 _Recognition_04_ を使用すると、類似性比較と人物照合比較の両方の精度が向上します。 _Recognition_04_ を使用すると、フェイス カバー (サージカル マスク、N95 マスク、布マスク) を装着した登録されているユーザーの認識が向上します。 最新の _detection_03_ モデルを使用して、登録されているユーザーがフェイス カバーを装着しているかどうかを検出し、最新の _recognition_04_ モデルを使用してそれが誰であるかを認識する、安全でシームレスなユーザー エクスペリエンスを構築できるようになりました。 各モデルは他のモデルとは独立して動作します。1 つのモデルに対して設定された信頼度しきい値は、他の認識モデルで比較することを目的としたものではありません。

以下、モデルの競合を回避しながら、さまざまな顔の操作で選択されたモデルを指定する方法について説明します。 上級ユーザーであり、最新モデルに切り替えるべきかどうかを判断したい場合は、「[さまざまなモデルを評価する](#evaluate-different-models)」セクションに進み、新しいモデルを評価したり、現在のデータ セットを使用して結果を比較したりしてください。


## <a name="prerequisites"></a>前提条件

AI による顔の検出と識別の概念を理解している必要があります。 そうでない場合は、まずこれらのガイドを参照してください。

* [顔検出の概念](../concepts/face-detection.md)
* [顔認識の概念](../concepts/face-recognition.md)
* [画像内の顔を検出する方法](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>指定されたモデルで顔を検出する

顔の検出では、人間の顔の視覚的な目印を識別し、それらの境界ボックスの位置を見つけます。 また、顔の特徴を抽出して保存し、識別に使用します。 この情報のすべてが 1 つの顔の表現を形成します。

認識モデルは顔の特徴の抽出時に使用されるので、検出操作を実行するときにモデルのバージョンを指定できます。

[Face - Detect] API を使用するときは、`recognitionModel` パラメーターを使用してモデルのバージョンを割り当てます。 使用できる値は次のとおりです。
* recognition_01
* recognition_02
* recognition_03
* recognition_04


必要に応じて、_returnRecognitionModel_ パラメーター (既定値は **false**) を指定し、_recognitionModel_ が応答で返されるかどうかを指定できます。 したがって、[Face - Detect] REST API の要求 URL は次のようになります。

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

クライアント ライブラリを使用している場合、バージョンを表す文字列を渡すことによって `recognitionModel` の値を割り当てることができます。 未割り当てのままにした場合は、既定のモデル バージョン `recognition_01` が使用されます。 .NET クライアント ライブラリの次のコード例を参照してください。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>指定されたモデルで顔を識別する

Face サービスは画像から顔データを抽出し、([Add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API の呼び出しなどによって) それを **Person** オブジェクトに関連付けることができます。また、複数の **Person** オブジェクトを **PersonGroup** にまとめて格納することができます。 次に、[Face - Identify] の呼び出しによって) 新しい顔を **PersonGroup** と比較し、そのグループ内の一致した人物を識別することができます

**PersonGroup** は、すべての **Person** に対して 1 つの一意な認識モデルを持つ必要があります。これは、グループを作成する ([PersonGroup - Create] または [LargePersonGroup - Create]) ときに `recognitionModel` パラメーターを使用して指定できます。 このパラメーターを指定しない場合、元の `recognition_01` モデルが使用されます。 グループは常に、その作成時に指定された認識モデルを使用します。新しい顔がグループに追加されると、その顔はこのモデルに関連付けられます。これはグループの作成後に変更することはできません。 **PersonGroup** に設定されているモデルを調べるには、_returnRecognitionModel_ パラメーターを **true** に設定して [PersonGroup - Get] API を使用します。

.NET クライアント ライブラリの次のコード例を参照してください。

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

このコードでは、`mypersongroupid` という ID の **PersonGroup** を作成し、_recognition_02_ モデルを使用して顔の特徴を抽出するように設定します。

それに対応して、顔検出時に ([Face - Detect] API によって) この **PersonGroup** と比較するために使用するモデルを指定する必要があります。 使用するモデルは常に **PersonGroup** の設定と一致している必要があります。していない場合、互換性のないモデルが原因で操作が失敗します。

[Face - Identify] API に変更はありません。検出でモデルのバージョンを指定することだけが必要です。

## <a name="find-similar-faces-with-specified-model"></a>指定されたモデルで似た顔を検索する

類似検索のための認識モデルも指定できます。 [FaceList - Create] または [LargeFaceList - Create] で顔のリストを作成するときに、`recognitionModel` でモデルのバージョンを割り当てることができます。 このパラメーターを指定しない場合、既定では `recognition_01` モデルが使用されます。 顔リストは常に、その作成時に指定された認識モデルを使用します。新しい顔がリストに追加されると、その顔はこのモデルに関連付けられます。これは作成後に変更できません。 顔リストに設定されているモデルを調べるには、_returnRecognitionModel_ パラメーターを **true** に設定して [FaceList - Get] API を使用します。

.NET クライアント ライブラリの次のコード例を参照してください。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");
```

このコードは、特徴抽出のための _recognition_04_ モデルを使用して、`My face collection` という名前の顔リストを作成します。 新しく検出された顔に似た顔をこの顔リストから検索するときは、その顔が _recognition_04_ モデルを使用して検出 ([Face - Detect]) 済みである必要があります。 前のセクションと同様に、モデルは一貫している必要があります。

[Face - Find Similar] API に変更はありません。検出でモデルのバージョンを指定するだけです。

## <a name="verify-faces-with-specified-model"></a>指定されたモデルで顔を検証する

[Face - Verify] API は、2 つの顔が同じ人のものかどうかチェックします。 認識モデルについては Verify API での変更はありませんが、互いに比較できるのは同じモデルで検出された顔だけです。

## <a name="evaluate-different-models"></a>さまざまなモデルを評価する

独自のデータに対してさまざまな認識モデルのパフォーマンスを比較したい場合は、次の手順を実行する必要があります。
1. _recognition_01_、_recognition_02_、_recognition_03_、_recognition_04_ をそれぞれ使用して、4 つの PersonGroup を作成します。
1. 画像データを使用して顔を検出し、これらの 4 つの **PersonGroup** 内の **Person** に登録します。 
1. PersonGroup - Train API を使用して PersonGroup をトレーニングします。
1. 4 つすべての **PersonGroup** に対して Face - Identify でテストを実行し、結果を比較します。


信頼度しきい値 (顔を識別するために要求されるモデルの信頼度を決定する 0 ～ 1 の値) をいつも指定する場合、モデルごとに異なるしきい値を使用することが必要な場合があります。 あるモデルのしきい値を別のモデルと共有することは想定されておらず、共有しても同じ結果が得られるとは限りません。

## <a name="next-steps"></a>次のステップ

この記事では、さまざまな Face サービスの API で使用する認識モデルを指定する方法について説明しました。 次は、クイック スタートに従って顔検出を使い始めてみましょう。

* [Face .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Face Python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Face Go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc