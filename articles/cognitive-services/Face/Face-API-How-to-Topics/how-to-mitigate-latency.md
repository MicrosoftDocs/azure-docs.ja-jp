---
title: Face サービスを使用するときの待機時間を軽減する方法
titleSuffix: Azure Cognitive Services
description: Face サービスを使用するときの待機時間を軽減する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: 2c771509de5ac246bac0d8e006a5d0b884a410b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "101706811"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>方法: Face サービスを使用するときの待機時間を軽減する

Face サービスを使用すると、待機時間が発生する可能性があります。 待機時間とは、ネットワーク経由で通信するときに発生するあらゆる種類の遅延のことです。 一般に、待機時間には次のような原因が考えられます。
- 各パケットが送信元から送信先まで移動する必要がある物理的な距離。
- 伝送媒体に関する問題。
- 伝送パス上にあるルーターまたはスイッチでのエラー。
- ウイルス対策アプリケーション、ファイアウォール、およびその他のセキュリティ メカニズムでパケットを検査するために必要な時間。
- クライアントまたはサーバー アプリケーションでの誤動作。

このトピックでは、待機時間に関して可能性のある Azure Cognitive Services の使用に固有の原因と、その原因を軽減する方法について説明します。

> [!NOTE]
> Azure Cognitive Services からは、待機時間に関するサービス レベル アグリーメント (SLA) は提供されません。

## <a name="possible-causes-of-latency"></a>待機時間の考えられる原因

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Cognitive Services とリモート URL の間の低速の接続

一部の Azure Cognitive Services には、指定したリモート URL からデータを取得するメソッドが用意されています。 たとえば、Face サービスの [DetectWithUrlAsync メソッド](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_)を呼び出すときは、サービスで顔の検出を試みる画像の URL を指定できます。

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

その後、Face サービスでリモート サーバーから画像をダウンロードする必要があります。 Face サービスからリモート サーバーへの接続が低速である場合は、検出メソッドの応答時間に影響します。

これを回避するには、[Azure Premium Blob Storage に画像を格納する](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)ことを検討します。 次に例を示します。

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>サイズの大きいアップロード

一部の Azure Cognitive Services には、アップロードしたファイルからデータを取得するメソッドが用意されています。 たとえば、Face サービスの [DetectWithStreamAsync メソッド](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_)を呼び出すときは、サービスで顔の検出を試みる画像をアップロードできます。

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

アップロードするファイルが大きい場合は、次の理由により、`DetectWithStreamAsync` メソッドの応答時間に影響します。
- ファイルのアップロードに時間がかかります。
- ファイルのサイズに比例して、サービスによる処理時間が長くなります。

軽減策:
- [Azure Premium Blob Storage に画像を格納する](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet)ことを検討します。 次に例を示します。
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- アップロードするファイルを小さくすることを検討します。
    - [顔検出の入力データ](../concepts/face-detection.md#input-data)および[顔認識の入力データ](../concepts/face-recognition.md#input-data)に関するガイドラインを参照してください。
    - 顔検出の場合、検出モデル `DetectionModel.Detection01` を使用しているときは、画像ファイルのサイズを小さくすると処理速度が向上します。 検出モデル `DetectionModel.Detection02` を使用しているときは、画像ファイルが 1920 x 1080 より小さい場合にのみ、画像ファイルのサイズを小さくすると処理速度が向上します。
    - 顔認識の場合、顔のサイズを 200 x 200 ピクセルまで小さくしても、認識モデルの精度には影響しません。
    - `DetectWithUrlAsync` および `DetectWithStreamAsync` メソッドのパフォーマンスは、画像内の顔の数にも依存します。 Face サービスからは、1 つの画像に対して最大で 100 個の顔を返すことができます。 顔には、四角形のサイズの大きいものから小さいものの順に、ランクが付けされます。
    - 複数のサービス メソッドを呼び出す必要がある場合、アプリケーションの設計がそれに対応している場合は、それらを並列に呼び出すことを検討します。 たとえば、顔の比較を実行するために 2 つの画像で顔を検出する必要がある場合は、次のようにします。
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>コンピューティング リソースと Face サービスの間の低速接続

コンピューターと Face サービスの間の接続が遅い場合は、サービス メソッドの応答時間に影響します。

軽減策:
- Face サブスクリプションを作成するときに、アプリケーションがホストされている場所に最も近いリージョンを選択します。
- 複数のサービス メソッドを呼び出す必要がある場合、アプリケーションの設計がそれに対応している場合は、それらを並列に呼び出すことを検討します。 前のセクションの例を参照してください。

## <a name="next-steps"></a>次のステップ

このガイドでは、Face サービスを使用するときの待機時間を軽減する方法について説明しました。 次に、既存の PersonGroup および FaceList オブジェクトから、それぞれ LargePersonGroup および LargeFaceList オブジェクトにスケールアップする方法を確認してください。

> [!div class="nextstepaction"]
> [例:大規模なフィーチャーを使用する](how-to-use-large-scale.md)

## <a name="related-topics"></a>関連トピック

- [リファレンス ドキュメント (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [リファレンス ドキュメント (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)