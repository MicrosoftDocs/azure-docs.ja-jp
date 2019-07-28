---
title: Media Services v3 .NET を使用したカスタム変換のエンコード - Azure | Microsoft Docs
description: このトピックでは、Azure Media Services v3 で .NET を使用してカスタム変換をエンコードする方法について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 05/03/2019
ms.author: juliako
ms.openlocfilehash: 2167a74dc81bdbb2562211cf5c0195a755941d9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148331"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>カスタム変換を使用してエンコードする方法 - .NET

Azure Media Services を使用してエンコードする場合、[ファイルのストリーム配信](stream-files-tutorial-with-api.md)に関するチュートリアルで示されている業界のベスト プラクティスに基づき、推奨される組み込みプリセットのいずれかを使用して速やかに作業を開始できます。 また、特定のシナリオやデバイス要件をターゲットとするカスタム プリセットを作成することもできます。

## <a name="considerations"></a>考慮事項

カスタム プリセットを作成するときは、次の考慮事項が適用されます。

* AVC コンテンツの高さと幅のすべての値は、4 の倍数である必要があります。
* Azure Media Services v3 では、エンコード ビットレートはすべてビット/秒単位で指定されます。 これは v2 API のプリセットとは異なります。v2 では、キロビット/秒が単位として使用されていました。 たとえば、v2 でビットレートが 128 (キロビット/秒) に指定されていた場合、v3 では 128000 (ビット/秒) に設定されます。

## <a name="prerequisites"></a>前提条件 

[Media Services アカウントを作成する](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>サンプルのダウンロード

次のコマンドを使って、.NET Core サンプルが含まれる GitHub リポジトリを、ご利用のコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
カスタム プリセットのサンプルは、[EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) フォルダーにあります。

## <a name="create-a-transform-with-a-custom-preset"></a>カスタム プリセットを使用して変換を作成する 

新しい [Transform](https://docs.microsoft.com/rest/api/media/transforms) を作成するときは、出力として生成するものを指定する必要があります。 必須のパラメーターは、下記のコードで示すように [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) オブジェクトです。 各 **TransformOutput** には **Preset** が含まれます。 **Preset** では、目的の **TransformOutput** の生成に使用されるビデオやオーディオの処理操作の詳細な手順が記述されています。 次の **TransformOutput** では、カスタム コーデックとレイヤー出力の設定を作成します。

[Transform](https://docs.microsoft.com/rest/api/media/transforms) を作成するときは、次のコードに示すように、最初に **Get** メソッドを使って変換が既に存在するかどうかを確認する必要があります。 Media Services v3 では、エンティティが存在しない場合 (大文字と小文字の区別がない名前のチェック)、エンティティに対する **Get** メソッドは **null** を返します。

### <a name="example"></a>例

次の例では、この変換が使用されたときに生成される一連の出力を定義します。 まず、オーディオ エンコードのための AacAudio レイヤーと、ビデオ エンコードのための 2 つの H264Video レイヤーを追加します。 ビデオ レイヤーでは、出力ファイル名で使用できるようにラベルを割り当てます。 次に、出力にサムネイルも含めます。 次の例では、入力ビデオの解像度の 50%、および 3 つのタイムスタンプ (入力ビデオの長さの {25%, 50%, 75}) で生成された、PNG 形式の画像を指定します。 最後に、出力ファイルの形式 (ビデオとオーディオの形式とサムネイルの形式) を指定します。 複数の H264Layer があるため、レイヤーごとに一意の名前を生成するマクロを使用する必要があります。 `{Label}` または `{Bitrate}` のいずれかのマクロを使用できます。この例では、前者が示されています。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>次の手順

[ファイルのストリーミング](stream-files-tutorial-with-api.md) 
