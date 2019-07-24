---
title: 画像の中にある顔を検出する - Face API
titleSuffix: Azure Cognitive Services
description: 顔検出機能によって返されるさまざまなデータの使用方法について説明します。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588773"
---
# <a name="get-face-detection-data"></a>顔検出データの取得

このガイドでは、顔検出を使用して、特定の画像から性別、年齢、姿勢などの属性を抽出する方法について説明します。 このガイドのコード スニペットは、Face API クライアント ライブラリを使用して C# で記述されていますが、[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) を使用して同じ機能を使用することもできます。

このガイドでは、次の操作方法について説明します。

- 画像内での顔の位置と寸法を取得する。
- 画像から、顔のさまざまなランドマーク (瞳孔、鼻、口など) の位置を取得する。
- 検出された顔の属性 (性別、年齢、感情など) を推測する。

## <a name="setup"></a>セットアップ

このガイドでは、Face サブスクリプション キーとエンドポイント URL を使用して、`faceClient` という名前の **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** オブジェクトを既に作成していることを前提としています。 ここから、**[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (このガイドで使用しているメソッド) または **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)** のいずれかを呼び出すことによって、顔検出機能を使用することができます。 これを設定する方法について詳しくは、[顔検出のクイックスタート (C# 用)](../quickstarts/csharp-detect-sdk.md) をご覧ください。

このガイドでは、Detect 呼び出しの仕様 (どのような引数を渡すことができるかや、返されたデータで何ができるか) に重点を置いて説明を進めます。 操作が増えるほど処理時間が長くなるので、必要な機能だけを照会することをお勧めします。

## <a name="get-basic-face-data"></a>基本的な顔データの取得

顔を検出し、画像内での位置を取得するには、_returnFaceId_ パラメーターを **true** (既定値) に設定してしてメソッドを呼び出します。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

返された **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** オブジェクトを照会することで、顔の一意の ID と、ピクセル座標を示す四角形を取得できます。

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

顔の位置と寸法を解析する方法については、「**[FaceRectangle クラス](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)**」をご覧ください。 通常、この四角形には、目、眉、鼻、口が含まれます。頭の上部、耳、あごは必ずしも含まれません。 顔四角形を使用して頭部全体や上半身のポートレート (証明写真のような画像) をトリミングする場合は、各方向のマージンを操作して四角形を拡張することもできます。

## <a name="get-face-landmarks"></a>顔のランドマークの取得

顔のランドマークとは、顔の中の見つけやすいポイント (瞳孔や鼻の先など) のことです。 顔のランドマーク データは、_returnFaceLandmarks_ パラメーターを **true** に設定することで取得できます。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

既定では、27個 の定義済みランドマーク ポイントがあります。 次の図は、27 か所のポイントを示したものです。

![顔のランドマーク (全 27 か所) とラベルを示した図](../Images/landmarks.1.jpg)

返されるポイントは、顔の四角形のフレームと同様にピクセル単位です。 次のコードは、鼻と瞳孔の位置を取得する方法を示しています。

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

顔のランドマーク データは、顔の方向を正確に計算する目的にも使用できます。 たとえば、顔の回転は、口の中心から目の中心までのベクトルとして定義することができます。 次に示すのは、このベクターを計算するコードです。

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

顔の方向がわかったら、四角形の顔フレームを回転させて、より適切な向きに修正することができます。 画像内の顔をトリミングする場合は、画像をプログラムで回転させて、顔が常に縦に表示されるようにすることができます。

## <a name="get-face-attributes"></a>顔の属性の取得

顔検出 API では、顔の四角形とランドマークに加えて、顔に関するいくつかの概念属性を分析できます。 チェックの内容は次のとおりです

- Age
- 性別
- 笑顔の強さ
- 顔ひげ
- 眼鏡
- 3D 頭部姿勢
- Emotion

> [!IMPORTANT]
> これらの属性は統計的アルゴリズムを使用して予測されるので、必ずしも正確とは限りません。 属性データに基づいて意思決定を行う場合は、注意を払うようにしてください。
>

顔の属性を分析するには、_returnFaceAttributes_ パラメーターを **[FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** 値のリストに設定します。

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

その後、返されたデータへの参照を取得し、ニーズに応じてさらなる操作を行います。

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

各属性について詳しくは、「[Glossary](../Glossary.md)」(用語集) をご覧ください。

## <a name="next-steps"></a>次の手順

このガイドでは、顔検出のさまざまな機能を使用する方法について説明しました。 取得した顔データのさらなる詳細については、[用語集](../Glossary.md)をご覧ください。

## <a name="related-topics"></a>関連トピック

- [リファレンス ドキュメント (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [リファレンス ドキュメント (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
