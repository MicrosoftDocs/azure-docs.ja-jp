---
title: 画像の中にある顔を検出する - Face
titleSuffix: Azure Cognitive Services
description: このガイドでは、顔検出を使用して、特定の画像から性別、年齢、姿勢などの属性を抽出する方法について説明します。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a15cce45c527a92c99e0488661e0b67bb8e2371
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713067"
---
# <a name="get-face-detection-data"></a>顔検出データの取得

このガイドでは、顔検出を使用して、特定の画像から性別、年齢、姿勢などの属性を抽出する方法について説明します。 このガイドのコード スニペットは Azure Cognitive Services Face クライアント ライブラリを使用して C# で記述されています。 同じ機能を [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) でも使用できます。

このガイドでは、次の方法について説明します。

- 画像内での顔の位置と寸法を取得する。
- 画像から、瞳孔、鼻、口など、顔のさまざまなランドマークの位置を取得する。
- 検出された顔から性別、年齢、感情などの属性を推測する。

## <a name="setup"></a>セットアップ

このガイドでは、Face サブスクリプション キーとエンドポイント URL を使用して、`faceClient` という名前の [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) オブジェクトを既に作成していることを前提としています。 ここから、[DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) (このガイドで使用) または [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) のいずれかを呼び出すことによって、顔検出機能を使用することができます。 この機能を設定する方法については、クイックスタートのいずれかに従ってください。

このガイドでは、Detect 呼び出しの仕様、たとえば、渡すことができる引数や返されたデータで実行できることを中心に説明を進めます。 必要な機能だけを照会することをお勧めします。 操作が増えると、完了までの時間が長くなります。

## <a name="get-basic-face-data"></a>基本的な顔データの取得

顔を検出し、画像内での位置を取得するには、_returnFaceId_ パラメーターを **true** に設定して、[DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) または [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) メソッドを呼び出します。 これは、既定の設定です。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

返された [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) オブジェクトを照会して、そのオブジェクトの一意の ID と、顔のピクセル座標を示す四角形を取得できます。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

顔の位置と寸法を解析する方法については、「[FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle)」を参照してください。 通常、この四角形には、目、眉、鼻、口が含まれます。 頭の上部、耳、あごは必ずしも含まれません。 顔の四角形を使用して、頭部全体をトリミングしたり、上半身のポートレート (証明写真のような画像) を取得したりするために、四角形を各方向に拡張できます。

## <a name="get-face-landmarks"></a>顔のランドマークの取得

[顔のランドマーク](../concepts/face-detection.md#face-landmarks)とは、瞳孔、鼻の先など、顔の中の見つけやすいポイントのことです。 顔のランドマーク データを取得するには、_detectionModel_ パラメーターを **DetectionModel.Detection01** に、_returnFaceLandmarks_ パラメーターを **true** に設定します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

次のコードは、鼻と瞳孔の位置を取得する方法を示しています。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

顔のランドマーク データを使用して、顔の方向を正確に計算することもできます。 たとえば、顔の回転は、口の中心から目の中心までのベクターとして定義できます。 この次のコードではこのベクターが計算されます。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

顔の方向がわかったら、四角形の顔フレームを回転させて、より適切な向きに修正できます。 画像内の顔をトリミングするには、顔が常に縦に表示されるように、画像をプログラムで回転させます。

## <a name="get-face-attributes"></a>顔の属性の取得

顔検出 API では、顔の四角形とランドマークに加えて、顔に関するいくつかの概念属性を分析できます。 完全な一覧については、[顔の属性](../concepts/face-detection.md#attributes)の概念に関するセクションを参照してください。

顔の属性を分析するには、_detectionModel_ パラメーターを **DetectionModel.Detection01** に、_returnFaceAttributes_ パラメーターを [FaceAttributeType Enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) 値のリストに設定します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

その後、返されたデータへの参照を取得し、ニーズに応じてさらに操作します。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

各属性の詳細については、「[Face detection and attributes (顔の検出と属性)](../concepts/face-detection.md)」概念ガイドを参照してください。

## <a name="next-steps"></a>次のステップ

このガイドでは、顔検出のさまざまな機能を使用する方法について説明しました。 次は詳細なチュートリアルに従って、お使いのアプリにこれらの機能を統合します。

- [チュートリアル:画像内の顔データを表示する WPF アプリを作成する](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>関連トピック

- [リファレンス ドキュメント (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [リファレンス ドキュメント (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
