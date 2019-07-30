---
title: 認識モデルの指定方法 - Face API
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Face API アプリケーションで使用する認識モデルを選択する方法を示します。
services: cognitive-services
author: longl
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: longl
ms.openlocfilehash: fd60923351970dfe5aa5705a0508dbd39941ef58
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68254357"
---
# <a name="specify-a-face-recognition-model"></a>顔認識モデルを指定する

このガイドでは、Azure Face API を使用した顔の検出、識別、および類似検索のための顔認識モデルを指定する方法を示します。

Face API では、機械学習モデルを使用して、画像内の人間の顔に対して操作を実行します。 お客様からのフィードバックや研究の進展に基づいてモデル精度の改善を続けており、これらの改善をモデルの更新として提供します。 開発者には、使用する顔認識モデルのバージョンを指定する選択肢があり、ユースケースに最も適したモデルを選択できます。

新しいユーザーは、最新のモデルを使用することをお勧めします。 以下、モデルの競合を回避しながら、さまざまな顔の操作でそれを指定する方法について説明します。 上級ユーザーであり、最新モデルに切り替えるべきかどうかわからない場合は、[さまざまなモデルを評価する](#evaluate-different-models)のセクションに進み、新しいモデルを評価したり、現在のデータ セットを使用して結果を比較したりしてください。

## <a name="prerequisites"></a>前提条件

AI による顔の検出と識別の概念を理解している必要があります。 そうでない場合は、まず以下の方法ガイドを参照してください。

* [画像内の顔を検出する方法](HowtoDetectFacesinImage.md)
* [画像内の顔を識別する方法](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>指定されたモデルで顔を検出する

顔の検出では、人間の顔の視覚的な目印を識別し、それらの境界ボックスの位置を見つけます。 また、顔の特徴を抽出して保存し、識別に使用します。 この情報のすべてが 1 つの顔の表現を形成します。

認識モデルは顔の特徴の抽出時に使用されるので、検出操作を実行するときにモデルのバージョンを指定できます。

[Face - Detect] API を使用するときは、`recognitionModel` パラメーターを使用してモデルのバージョンを割り当てます。 使用できる値は次のとおりです。

* `recognition_01`
* `recognition_02`

必要に応じて、_returnRecognitionModel_ パラメーター (既定値は **false**) を指定し、_recognitionModel_ が応答で返されるかどうかを指定できます。 したがって、[Face - Detect] REST API の要求 URL は次のようになります。

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

クライアント ライブラリを使用している場合、バージョンを表す文字列を渡すことによって `recognitionModel` の値を割り当てることができます。
未割り当てのままにした場合は、既定のモデル バージョン (_recognition_01_) が使用されます。 .NET クライアント ライブラリの次のコード例を参照してください。

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_02", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>指定されたモデルで顔を識別する

Face API は画像から顔データを抽出し、([Add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API の呼び出しなどによって) それを **Person** オブジェクトに関連付けることができます。また、複数の **Person** オブジェクトを **PersonGroup** にまとめて格納することができます。 次に、[Face - Identify] の呼び出しによって) 新しい顔を **PersonGroup** と比較し、そのグループ内の一致した人物を識別することができます

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

類似検索のための認識モデルも指定できます。 [FaceList - Create] または [LargeFaceList - Create] で顔のリストを作成するときに、`recognitionModel` でモデルのバージョンを割り当てることができます。 このパラメーターを指定しない場合、元の `recognition_01` モデルが使用されます。 顔リストは常に、その作成時に指定された認識モデルを使用します。新しい顔がリストに追加されると、その顔はこのモデルに関連付けられます。これは作成後に変更できません。 顔リストに設定されているモデルを調べるには、_returnRecognitionModel_ パラメーターを **true** に設定して [FaceList - Get] API を使用します。

.NET クライアント ライブラリの次のコード例を参照してください。

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_02");
```

このコードは、特徴抽出のための _recognition_02_ モデルを使用して、`My face collection` という名前の顔リストを作成します。 新しく検出された顔に似た顔をこの顔リストから検索するときは、その顔が _recognition_02_ モデルを使用して検出 ([Face - Detect]) 済みである必要があります。 前のセクションと同様に、モデルは一貫している必要があります。

[Face - Find Similar] API に変更はありません。検出でモデルのバージョンを指定するだけです。

## <a name="verify-faces-with-specified-model"></a>指定されたモデルで顔を検証する

[Face - Verify] API は、2 つの顔が同じ人のものかどうかチェックします。 認識モデルについては Verify API での変更はありませんが、互いに比較できるのは同じモデルで検出された顔だけです。 したがって、2 つの顔がどちらも `recognition_01` または `recognition_02` を使用して検出されている必要があります。

## <a name="evaluate-different-models"></a>さまざまなモデルを評価する

特定のデータに対して _recognition_01_ モデルと _recognition_02_ モデルのパフォーマンスを比較したい場合、次の手順を実行する必要があります。

1. 2 つの **PersonGroup** を作成し、それぞれ _recognition_01_ と _recognition_02_ を指定します。
1. 画像データを使用して顔を検出し、これら 2 つの **PersonGroup** の **Person** にそれらの顔を登録し、[PersonGroup - Train] API でトレーニング プロセスをトリガーします。
1. 両方の **PersonGroup** に対して [Face - Identify] でテストを実行し、結果を比較します。

信頼度しきい値 (顔を識別するために要求されるモデルの信頼度を決定する 0 ～ 1 の値) をいつも指定する場合、モデルごとに異なるしきい値を使用することが必要な場合があります。 あるモデルのしきい値を別のモデルと共有することは想定されておらず、共有しても同じ結果が得られるとは限りません。

## <a name="next-steps"></a>次の手順

この記事では、さまざまな Face サービスの API で使用する認識モデルを指定する方法について説明しました。 次は、クイック スタートに従って顔検出を使い始めてみましょう。

* [画像内の顔を検出する](../quickstarts/csharp-detect-sdk.md)

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
