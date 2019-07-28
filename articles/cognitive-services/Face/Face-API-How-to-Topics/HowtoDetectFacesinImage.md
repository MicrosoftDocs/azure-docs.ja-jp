---
title: 画像の中にある顔を検出する - Face API
titleSuffix: Azure Cognitive Services
description: 顔検出機能によって返されるさまざまなデータの使用方法について説明します。
services: cognitive-services
author: stevemsft
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.openlocfilehash: 46bd1bdd55725878bc7b1bd55d5e24b78d82aada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124542"
---
# <a name="get-face-detection-data"></a>顔検出データの取得

このガイドでは、顔検出を使用して、特定の画像から性別、年齢、姿勢などの属性を抽出する方法について説明します。 このガイドのコード スニペットは Azure Cognitive Services Face API クライアント ライブラリを使用して C# で記述されています。 同じ機能を [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) でも使用できます。

このガイドでは、次の方法について説明します。

- 画像内での顔の位置と寸法を取得する。
- 画像から、瞳孔、鼻、口など、顔のさまざまなランドマークの位置を取得する。
- 検出された顔から性別、年齢、感情などの属性を推測する。

## <a name="setup"></a>セットアップ

このガイドでは、Face サブスクリプション キーとエンドポイント URL を使用して、`faceClient` という名前の [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) オブジェクトを既に作成していることを前提としています。 ここから、[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet) (このガイドで使用) または [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet) のいずれかを呼び出すことによって、顔検出機能を使用することができます。 この機能を設定する方法については、[顔検出のクイックスタート (C# 用)](../quickstarts/csharp-detect-sdk.md) をご覧ください。

このガイドでは、Detect 呼び出しの仕様、たとえば、渡すことができる引数や返されたデータで実行できることを中心に説明を進めます。 必要な機能だけを照会することをお勧めします。 操作が増えると、完了までの時間が長くなります。

## <a name="get-basic-face-data"></a>基本的な顔データの取得

顔を検出し、画像内での位置を取得するには、_returnFaceId_ パラメーターを **true** に設定して、メソッドを呼び出します。 これは、既定の設定です。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

返された [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) オブジェクトを照会して、そのオブジェクトの一意の ID と、顔のピクセル座標を示す四角形を取得できます。

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

顔の位置と寸法を解析する方法については、「[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)」を参照してください。 通常、この四角形には、目、眉、鼻、口が含まれます。 頭の上部、耳、あごは必ずしも含まれません。 顔の四角形を使用して、頭部全体をトリミングしたり、上半身のポートレート (証明写真のような画像) を取得したりするために、四角形を各方向に拡張できます。

## <a name="get-face-landmarks"></a>顔のランドマークの取得

[顔のランドマーク](../concepts/face-detection.md#face-landmarks)とは、瞳孔、鼻の先など、顔の中の見つけやすいポイントのことです。 顔のランドマーク データを取得するには、_returnFaceLandmarks_ パラメーターを **true** に設定します。

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

次のコードは、鼻と瞳孔の位置を取得する方法を示しています。

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

顔のランドマーク データを使用して、顔の方向を正確に計算することもできます。 たとえば、顔の回転は、口の中心から目の中心までのベクターとして定義できます。 この次のコードではこのベクターが計算されます。

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

顔の方向がわかったら、四角形の顔フレームを回転させて、より適切な向きに修正できます。 画像内の顔をトリミングするには、顔が常に縦に表示されるように、画像をプログラムで回転させます。

## <a name="get-face-attributes"></a>顔の属性の取得

顔検出 API では、顔の四角形とランドマークに加えて、顔に関するいくつかの概念属性を分析できます。 完全な一覧については、[顔の属性](../concepts/face-detection.md#attributes)の概念に関するセクションを参照してください。

顔の属性を分析するには、_returnFaceAttributes_ パラメーターを [FaceAttributeType Enum](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) 値のリストに設定します。

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

その後、返されたデータへの参照を取得し、ニーズに応じてさらに操作します。

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

各属性の詳細については、「[Face detection and attributes (顔の検出と属性)](../concepts/face-detection.md)」概念ガイドを参照してください。

## <a name="next-steps"></a>次の手順

このガイドでは、顔検出のさまざまな機能を使用する方法について説明しました。 次は詳細なチュートリアルに従って、お使いのアプリにこれらの機能を統合します。

- [チュートリアル:画像内の顔データを表示する WPF アプリを作成する](../Tutorials/FaceAPIinCSharpTutorial.md)
- [チュートリアル:画像内の顔を検出してフレームに収める Android アプリを作成する](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)

## <a name="related-topics"></a>関連トピック

- [リファレンス ドキュメント (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [リファレンス ドキュメント (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)