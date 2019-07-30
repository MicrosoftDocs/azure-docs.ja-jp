---
title: HeadPose 属性を使用する
titleSuffix: Azure Cognitive Services
description: HeadPose 属性を使用して、顔の四角形を自動的に回転する方法、または頭のジェスチャをビデオ フィードから検出する方法について説明します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 168b4fce873206e39a32a83da3dc5509b431d6a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058579"
---
# <a name="use-the-headpose-attribute"></a>HeadPose 属性を使用する

このガイドでは、検出された顔の HeadPose 属性を使用していくつかの重要なシナリオを実現する方法について説明します。

## <a name="rotate-the-face-rectangle"></a>顔の四角形を回転する

顔矩形は、検出される顔ごとに返されますが、これはイメージ内の顔の位置とサイズをマーク付けします。 既定では、この四角形は常に (辺は垂直方向および水平方向) イメージに揃えますが、顔が傾斜している場合は合わせるのが非効率になります。 プログラムでイメージ内の顔をトリミングする場合、トリミングする四角形を回転する方が良いでしょう。

[Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) サンプル アプリでは、HeadPose 属性を使用して、検出された顔の長方形を回転します。

### <a name="explore-the-sample-code"></a>サンプル コードの検証

HeadPose 属性を使用すれば、顔矩形を回転するようプログラミングできます。 顔を検出する際に、この属性を指定する場合 (「[How to detect faces](HowtoDetectFacesinImage.md)」 (顔の検出方法)参照)、クエリは後で実行きます。 [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF)アプリの次のメソッドから、 **DetectedFace**オブジェクトの一覧を取得し **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** オブジェクトの一覧を返します。 ここで**Face**はカスタム クラスであり、更新された四角形の座標など顔データを保存します。 新しい値は、**上部**、**左**、**幅**、および**高さ**用に計算し新しいフィールドの**FaceAngle**は回転を指定します。

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>更新された四角形の表示

ここでは、返された使える**Face**オブジェクトをディスプレイで使用できます。 [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml)の次の行は、新しい四角形をこのデータからレンダリングする方法を示します。

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>頭のジェスチャを検出する

HeadPose の変化をリアルタイムで追跡することで、うなずきや頭の揺れのような頭のジェスチャを検出することができます。 この機能をカスタムのライブネス検出器として使用できます。

ライブネス検出とは、対象が実在の人物であって画像やビデオの表現ではないと判定するタスクのことです。 頭のジェスチャの検出器は、特に人の画像表現と区別してライブネスを検証するために役立つ 1 つの方法として機能する可能性があります。

> [!CAUTION]
> 頭のジェスチャをリアルタイムで検出するには、Face API を高速に (1 秒に 1 回より多く) 呼び出す必要があります。 Free レベル (f0) サブスクリプションをご利用の場合、これは不可能です。 有料レベルのサブスクリプションをご利用の場合、頭のジェスチャ検出のための高速 API 呼び出しのコストを計算済みであることを確認してください。

頭のジェスチャ検出の実施例については、GitHub の [Face API HeadPose Sample](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) を参照してください。

## <a name="next-steps"></a>次の手順

回転した顔矩形の実施例については、GitHub の [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) アプリを参照してください。 または、[Face API HeadPose サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) アプリを参照してください。HeadPose 属性をリアルタイムに追跡して頭の動きを検出します。