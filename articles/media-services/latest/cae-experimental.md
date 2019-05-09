---
title: コンテンツに対応したエンコードの試験的プリセット - Azure |Microsoft Docs
description: この記事では、Azure Media Services でのコンテンツに対応したエンコードについて説明します
services: media-services
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: sethm
ms.custom: ''
ms.openlocfilehash: 3c50502a8b873503ee937914fac5f2d92cb23a2b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287546"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>コンテンツに対応したエンコードの試験的プリセット

[アダプティブ ビットレート ストリーミング](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)によるコンテンツ配信の準備をするためには、ビデオを複数のビット レート (高から低まで) でエンコードする必要があります。 ビットレートが低くなるにつれて品質が適切に低下するため、ビデオの解像度も低下します。 [ H264MultipleBitrate1080p](../previous/media-services-mes-preset-h264-multiple-bitrate-1080p.md) などの一部の固定エンコーディング プリセットで見られるように、これによりいわゆるエンコーディング ラダー (解像度とビットレートのテーブル) が生成されます。

## <a name="overview"></a>概要

2015 年 12 月に Netflix が[ブログ](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2)を公開した後、1 つのプリセットですべてのビデオに対応するアプローチを打開することへの関心が高まりました。 それ以降、コンテンツに対応したエンコーディングのための複数のソリューションが Marketplace でリリースされました。概要については、[この記事](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx)をご覧ください。 コンテンツに対応すること、エンコーディング ラダーを個々のビデオの複雑さに合わせてカスタマイズまたはチューニングすることがその狙いです。 各解像度では、それを超えると品質が向上しても知覚できないビットレートが存在します。エンコーダーはこの最適なビットレート値で動作します。 最適化の次のレベルでは、コンテンツに基づいて解像度を選択します。たとえば、PowerPoint プレゼンテーションのビデオは、720p を下回ってもメリットはありません。 さらに進むと、ビデオ内の各ショットの設定を最適化するようにエンコーダーに任せることができます。 Netflix は 2018 年に[このようなアプローチ](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830)について説明しました。

2017 年初頭、Microsoft は、ソース ビデオの品質と解像度のばらつきの問題に対処するため、[アダプティブ ストリーミング](autogen-bitrate-ladder.md) プリセットをリリースしました。 お客様には、一部は 1080p、その他は 720p、2、3 が SD やそれ以下の解像度など、さまざまなコンテンツの組み合わせがありました。 さらに、すべてのソース コンテンツが映画やテレビ スタジオの高品質メザニンであるとは限りませんでした。 アダプティブ ストリーミング プリセットは、ビットレート ラダーが入力メザニンの解像度または平均ビットレートを絶対に超えないようにすることで、これらの問題を解決します。

試験的なコンテンツに対応したエンコーディング プリセットは、広範な計算解析を必要とせずに、エンコーダーに与えられた解像度に対して最適なビットレート値を探させるカスタム ロジックを組み込むことによって、そのメカニズムを拡張します。 その結果、この新しいプリセットは、アダプティブ ストリーミング プリセットよりもビットレートの出力は低くなりますが、品質は向上します。 次のサンプル グラフをご覧ください。[PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) や [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion) などの品質メトリックを使用して比較を示しています。 ソースはエンコーダーを強調するため、映画やテレビ番組からの複雑度の高いショットの短いクリップを連結して作成されています。 定義上、このプリセットはコンテンツごとに異なる結果を生成します。つまり、コンテンツによっては、ビットレートの大幅な低下や品質の向上が見られない場合もあります。

![PSNR を使用したレート歪み (RD) 曲線](media/cae-experimental/msrv1.png)

**図 1:複雑性が高いソースの PSNR メトリックを使用したレート歪み (RD) 曲線**

![VMAF を使用したレート歪み (RD) 曲線](media/cae-experimental/msrv2.png)

**図 2:複雑性が高いソースの VMAF メトリックを使用したレート歪み (RD) 曲線**

現時点でプリセットは、複雑性が高く高品質のソース ビデオ (映画、テレビ番組) に合わせてチューニングされています。 低品質のコンテンツ (PowerPoint プレゼンテーションなど) や低品質のビデオへの対応が進行中です。 このプリセットは、アダプティブ ストリーミング プリセットと同じ解像度のセットも使用します。 Microsoft は、コンテンツに基づいて最小セットの解像度を選択する方法に取り組んでいます。 以下は、入力が低品質であること (低ビットレートのために圧縮アーティファクトが多い) をエンコーダーが判断できた、別のカテゴリのソース コンテンツの結果です。 試験的プリセットでは、ほとんどのクライアントが失速することなくストリームを再生できるように、エンコーダーが 1 つの出力レイヤーだけを生成することを判断しています。

![PSNR を使用した RD 曲線](media/cae-experimental/msrv3.png)

**図 3:低品質の入力 (1080 p) に対して PSNR を使用した RD 曲線**

![VMAF を使用した RD 曲線](media/cae-experimental/msrv4.png)

**図 4:低品質の入力 (1080 p) に対して VMAF を使用した RD 曲線**

## <a name="use-the-experimental-preset"></a>試験的プリセットを使用する

次のようにこのプリセットを使用する変換を作成することができます。 [このような](stream-files-tutorial-with-api.md)チュートリアルを使用すると、コードを次のように更新することができます。

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> "試験的" という接頭辞は、ここでは、基になるアルゴリズムが現在開発中であることを示すために使用されています。 ビットレート ラダーを生成するために使用されるロジックは、堅牢であり、さまざまな入力条件に適応するアルゴリズムに集中することを目的として、時間の経過とともに変化する可能性があり、今後変化するでしょう。 このプリセットを使用したエンコード ジョブは引き続き出力分に基づいて課金され、出力アセットは DASH や HLS などのプロトコルでストリーミング エンドポイントから配信できます。

## <a name="next-steps"></a>次の手順

ここまで、このビデオの最適化の新しいオプションについて理解できましたら、試してみてください。この記事の最後にあるリンクを使用してフィードバックをお送りいただくか、<amsved@microsoft.com> に直接ご連絡ください。
