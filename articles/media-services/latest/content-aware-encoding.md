---
title: コンテンツに対応したエンコードのプリセット - Azure Media Services
description: この記事では、Microsoft Azure Media Services v3 でのコンテンツに対応したエンコードについて説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d08e09905841f6068f2bac45680a8e5a011c158
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297367"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>コンテンツに対応したエンコードのプリセットを使用して、特定の解像度に最適なビットレートの値を検索する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[アダプティブ ビットレート ストリーミング](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)によるコンテンツ配信の準備をするためには、ビデオを複数のビット レート (高から低まで) でエンコードする必要があります。 これによりビットレートが低くなるにつれてビデオの解像度も低下するため、品質のグレースフル デグラデーションが保証されます。 このような複数のビットレート エンコードにより、いわゆるエンコード ラダー (解像度とビットレートの表) が生成されます。Media Services の[組み込みエンコード プリセット](/rest/api/media/transforms/createorupdate#encodernamedpreset)をご覧ください。

処理中のコンテンツに注意し、エンコード ラダーを個々のビデオの複雑さに合わせてカスタマイズまたはチューニングする必要があります。 各解像度では、それを超えると品質が向上しても知覚できないビットレートが存在します。エンコーダーはこの最適なビットレート値で動作します。 最適化の次のレベルでは、コンテンツに基づいて解像度を選択します。たとえば、PowerPoint プレゼンテーションのビデオは、720p を下回ってもメリットはありません。 さらに進むと、ビデオ内の各ショットの設定を最適化するようにエンコーダーに任せることができます。 

Microsoft の[アダプティブ ストリーミング](autogen-bitrate-ladder.md) プリセットは、ソース ビデオの品質と解像度のばらつきの問題に部分的に対処しています。 お客様のところには、一部が 1080 p でそれ以外が 720 p、またいくつかは SD やそれ以下の解像度というように、さまざまなコンテンツが混在しています。 さらに、すべてのソース コンテンツが映画やテレビ スタジオの高品質メザニンであるとは限りません。 アダプティブ ストリーミング プリセットは、ビットレート ラダーが入力メザニンの解像度または平均ビットレートを絶対に超えないようにすることで、これらの問題を解決します。 ただし、このプリセットでは、解像度とビットレート以外のソース プロパティは確認されません。

## <a name="the-content-aware-encoding"></a>コンテンツに対応したエンコード 

コンテンツに対応したエンコード プリセットは、広範な計算解析を必要とせずに、エンコーダーに与えられた解像度に対して最適なビットレート値を探させるカスタム ロジックを組み込むことによって、"アダプティブ ビットレート ストリーミング" のメカニズムを拡張します。 このプリセットでは、一連の GOP 配列 MP4 が作成されます。 入力コンテンツを指定すると、サービスによって入力コンテンツに対する最初の簡単な分析が実行され、その結果を使用して、アダプティブ ストリーミングによる配信に最適なレイヤーの数、適切なビット レートと解像度の設定が決定されます。 このプリセットは、複雑さが低から中程度のビデオに特に有効です。つまり、ビットレートはアダプティブ ストリーミング プリセットよりも低いものの、視聴者には快適なエクスペリエンスを提供できる品質を備えた出力ファイルということになります。 出力には、ビデオとオーディオがインターリーブされた MP4 ファイルが含まれるようになります

次のサンプル グラフは、[PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) や [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion) などの品質メトリックを使用した比較を示しています。 ソースはエンコーダーを強調するため、映画やテレビ番組からの複雑度の高いショットの短いクリップを連結して作成されています。 定義上、このプリセットはコンテンツごとに異なる結果を生成します。つまり、コンテンツによっては、ビットレートの大幅な低下や品質の向上が見られない場合もあります。

![PSNR を使用したレート歪み (RD) 曲線](media/content-aware-encoding/msrv1.png)

"**図 1:複雑性が高いソースの PSNR メトリックを使用したレート歪み (RD) 曲線**

![VMAF を使用したレート歪み (RD) 曲線](media/content-aware-encoding/msrv2.png)

"**図 2:複雑性が高いソースの VMAF メトリックを使用したレート歪み (RD) 曲線**

以下は、入力が低品質であること (低ビットレートのために圧縮アーティファクトが多い) をエンコーダーが判断できた、別のカテゴリのソース コンテンツの結果です。 コンテンツに対応したプリセットでは、ほとんどのクライアントが失速することなくストリームを再生できるように、十分に低いビットレートで 1 つの出力レイヤーだけを生成することがエンコーダーによって決定されます。

![PSNR を使用した RD 曲線](media/content-aware-encoding/msrv3.png)

"**図 3:低品質の入力 (1080 p) に対して PSNR を使用した RD 曲線**

![VMAF を使用した RD 曲線](media/content-aware-encoding/msrv4.png)

"**図 4:低品質の入力 (1080 p) に対して VMAF を使用した RD 曲線**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>コンテンツに対応したエンコード プリセットの使用方法 

次のようにこのプリセットを使用する変換を作成することができます。 

変換の出力を使用するチュートリアルについては、「[次のステップ](#next-steps)」 セクションを参照してください。 (チュートリアルに示されているように) 出力アセットは、MPEG-DASH や HLS などのプロトコルで Media Services ストリーミング エンドポイントから配信することができます。

> [!NOTE]
> ContentAwareEncodingExperimental ではなく、必ず **ContentAwareEncoding** プリセットを使用してください。

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> `ContentAwareEncoding` プリセットを使用したエンコード ジョブは、出力時間 (分) に基づいて課金されます。 
  
## <a name="next-steps"></a>次のステップ

* [チュートリアル:Media Services v3 を使用してビデオをアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - REST](stream-files-tutorial-with-rest.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - CLI](stream-files-cli-quickstart.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - .NET](stream-files-dotnet-quickstart.md)
* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - Node.js](stream-files-nodejs-quickstart.md)
