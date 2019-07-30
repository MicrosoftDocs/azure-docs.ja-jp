---
title: 検出モデルの指定方法 - Face API
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Face API アプリケーションで使用する顔検出モデルを選択する方法を示します。
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: yluiu
ms.openlocfilehash: 26ab3cb247309aa21791ca5a984f39ef40ce9a78
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249627"
---
# <a name="specify-a-face-detection-model"></a>顔検出モデルを指定する

このガイドでは、Azure Face API の顔検出モデルを指定する方法を示します。

Face API では、機械学習モデルを使用して、画像内の人間の顔に対して操作を実行します。 お客様からのフィードバックや研究の進展に基づいてモデル精度の改善を続けており、これらの改善をモデルの更新として提供します。 開発者には、使用する顔検出モデルのバージョンを指定する選択肢があり、自分のユースケースに最も適したモデルを選択できます。

特定の顔操作での顔検出モデルの指定方法について、後続の説明をお読みください。 Face API は、顔の画像を他の形式のデータに変換するときは常に、顔検出を使用します。

最新モデルを使用すべきかどうかわからない場合は、「[さまざまなモデルを評価する](#evaluate-different-models)」のセクションに進み、新しいモデルを評価したり、現在のデータセットを使用して結果を比較したりしてください。

## <a name="prerequisites"></a>前提条件

AI による顔検出の概念を理解している必要があります。 そうでない場合は、顔検出に関する概念ガイドまたはハウツー ガイドを参照してください。

* [顔検出の概念](../concepts/face-detection.md)
* [画像内の顔を検出する方法](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>指定されたモデルで顔を検出する

顔検出では、人間の顔の境界ボックス位置を見つけ、それらの視覚的な目印を識別します。 顔の特徴を抽出し、後で[認識](../concepts/face-recognition.md)操作で使用するために保存します。

[Face - Detect] API を使用するときは、`detectionModel` パラメーターを使用してモデルのバージョンを割り当てることができます。 使用できる値は次のとおりです。

* `detection_01`
* `detection_02`

[Face - Detect] REST API の要求 URL は次のようになります。

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

クライアント ライブラリを使用している場合は、適切な文字列を渡すことによって `detectionModel` の値を割り当てることができます。 未割り当てのままにした場合、API は既定のモデル バージョン (`detection_01`) を使用します。 .NET クライアント ライブラリの次のコード例を参照してください。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_02", detectionModel: "detection_02");
```

## <a name="add-face-to-person-with-specified-model"></a>指定したモデルを使用して Person に顔を追加する

Face API は画像から顔データを抽出し、[PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API によってそれを **Person** オブジェクトに関連付けることができます。 この API 呼び出しでは、[Face - Detect] と同じ方法で検出モデルを指定できます。

.NET クライアント ライブラリの次のコード例を参照してください。

```csharp
// Create a PersonGroup and add a person with face detected by "detection_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_02");
```

このコードは、ID `mypersongroupid` を持つ **PersonGroup** を作成し、それに **Person** を追加します。 次に、`detection_02` モデルを使用して、この **Person** に顔を追加します。 *detectionModel* パラメーターを指定しない場合、API は既定のモデルである `detection_01` を使用します。

> [!NOTE]
> **Person** オブジェクト内のすべての顔に同じ検出モデルを使用する必要はありません。また、([Face - Identify] API などで) **Person** オブジェクトと比較するために新しい顔を検出するときに、同じ検出モデルを使用する必要もありません。

## <a name="add-face-to-facelist-with-specified-model"></a>指定したモデルを使用して FaceList に顔を追加する

既存の **FaceList** オブジェクトに顔を追加するときにも検出モデルを指定できます。 .NET クライアント ライブラリの次のコード例を参照してください。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_02");
```

このコードは、`My face collection` という **FaceList** を作成し、それに `detection_02` モデルを使用して顔を追加します。 *detectionModel* パラメーターを指定しない場合、API は既定のモデルである `detection_01` を使用します。

> [!NOTE]
> **FaceList** オブジェクト内のすべての顔に同じ検出モデルを使用する必要はありません。また、**FaceList** オブジェクトと比較するために新しい顔を検出するときに、同じ検出モデルを使用する必要もありません。

## <a name="evaluate-different-models"></a>さまざまなモデルを評価する

各顔検出モデルは、異なるタスクに合わせて最適化されています。 相違点の概要については、次の表を参照してください。

|**detection_01**  |**detection_02**  |
|---------|---------|
|すべての顔検出操作に対する既定の選択です。 | 2019 年 5 月にリリースされ、すべての顔検出操作でオプションとして利用可能です。
|小さい顔、横顔、不鮮明な顔には最適化されていません。  | 小さい顔、横顔、不鮮明な顔での精度が向上しています。 |
|検出呼び出しで顔の属性 (頭部姿勢、年齢、感情など) が指定されている場合は、それらを返します。 |  顔の属性を返しません。     |
|検出呼び出しで顔のランドマークが指定されている場合は、それらを返します。   | 顔のランドマークを返しません。  |

`detection_01` と `detection_02` モデルのパフォーマンスを比較する最良の方法は、それらをサンプル データセットで使用することです。 さまざまな画像、特に多数の顔や識別しづらい顔に対して、それぞれの検出モデルを使用して [Face - Detect] API を呼び出すことをお勧めします。 各モデルによって返される顔の数に注意してください。

## <a name="next-steps"></a>次の手順

この記事では、さまざまな Face API で使用する検出モデルを指定する方法について説明しました。 次は、クイック スタートに従って顔検出を使い始めてみましょう。

* [画像内の顔を検出する (.NET SDK)](../quickstarts/csharp-detect-sdk.md)

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
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
