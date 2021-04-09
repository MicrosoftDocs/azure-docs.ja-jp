---
title: Media Encoder Standard (MES) 用のタスク プリセット | Microsoft Docs
description: このトピックでは、Media Encoder Standard (MES) 用のサービスが定義されたサンプル プリセットの概要を説明します。
author: IngridAtMicrosoft
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 74e557aa02f5ab83828262209653bf8a78fdd2d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011648"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Media Encoder Standard (MES) 用のタスク プリセット

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

**Media Encoder Standard** で事前に定義された一連のシステム エンコードを使って、エンコード ジョブを作成できます。 Media Services でのストリーミング用にビデオをエンコードする場合は、"アダプティブ ストリーミング" プリセットを使うことをお勧めします。 このプリセットを指定すると、Media Encoder Standard は[ビットレート ラダーを自動生成](media-services-autogen-bitrate-ladder-with-mes.md)します。 

### <a name="creating-custom-presets-from-samples"></a>サンプルからカスタム プリセットを作成する
Media Services では、特定のエンコーディング ニーズと要件を満たすようにプリセットのすべての値をカスタマイズできます。 エンコード プリセットをカスタマイズする必要がある場合、カスタム構成のテンプレートとして、このセクションで説明されている以下のシステム プリセットのいずれかから始めることができます。 これらのプリセット内の各要素の意味と各要素に有効な値の説明については、「[Media Encoder Standard スキーマ](media-services-mes-schema.md)」を参照してください。  
  
> [!NOTE]
>  4k エンコードのプリセットを使用する場合は、`S3` 予約ユニットの種類を取得する必要があります。 詳細については、「 [Encoding の規模の設定方法](./media-services-scale-media-processing-overview.md)」を参照してください。  

#### <a name="video-rotation-default-setting-in-presets"></a>プリセットのビデオ回転の既定設定:
Media Encoder Standard では、ビデオの回転は既定で有効になっています。 モバイル デバイスで縦向きモードでビデオを録画した場合、これらのプリセットを使用すると、エンコード前に横向きモードに回転されます。
 
## <a name="available-presets"></a>使用可能なプリセット: 

 [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md): GOP がアラインメントされた、ビットレートが 6,000 kbps ～ 400 kbps、音声が AAC 5.1 である 8 つの MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md): GOP がアラインメントされた、ビットレートが 6,000 kbps ～ 400 kbps、音声が AAC ステレオである 8 つの MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 16x9 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md): GOP がアラインメントされた、ビットレートが 8,500 kbps ～ 200 kbps、音声が AAC ステレオである 8 つの MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md): GOP がアラインメントされた、ビットレートが 1,900 kbps ～ 400 kbps、音声が AAC 5.1 である 5 つの MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md): GOP がアラインメントされた、ビットレートが 1,900 kbps ～ 400 kbps、音声が AAC ステレオである 5 つの MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md): GOP がアラインメントされた、ビットレートが 20,000 kbps ～ 1,000 kbps、音声が AAC 5.1 である 12 の MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md): GOP がアラインメントされた、ビットレートが 20,000 kbps ～ 1,000 kbps、音声が AAC ステレオである 12 の MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 4x3 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md): GOP がアラインメントされた、ビットレートが 8,500 kbps ～ 200 kbps、音声が AAC ステレオである 8 つの MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md): GOP がアラインメントされた、ビットレートが 1,600 kbps ～ 400 kbps、音声が AAC 5.1 である 5 つの MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md): GOP がアラインメントされた、ビットレートが 1,600 kbps ～ 400 kbps、音声が AAC ステレオである 5 つの MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md): GOP がアラインメントされた、ビットレートが 3,400 kbps ～ 400 kbps、音声が AAC 5.1 である 6 つの MP4 ファイルを生成します。  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md): GOP がアラインメントされた、ビットレートが 3,400 kbps ～ 400 kbps、音声が AAC ステレオである 6 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md): ビットレートが 6,750 kbps、音声が AAC 5.1 である 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md): ビットレートが 6,750 kbps、音声が AAC ステレオである 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md): ビットレートが 18,000 kbps、音声が AAC 5.1 である 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md): ビットレートが 18,000 kbps、音声が AAC ステレオである 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md): ビットレートが 1,800 kbps、音声が AAC 5.1 である 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md): ビットレートが 1,800 kbps、音声が AAC ステレオである 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md): ビットレートが 2,200 kbps、音声が AAC 5.1 である 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md): ビットレートが 2,200 kbps、音声が AAC ステレオである 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md): ビットレートが 4,500 kbps、音声が AAC 5.1 である 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 720p for Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md): ビットレートが 2,000 kbps、音声が AAC ステレオである 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md): ビットレートが 4,500 kbps、音声が AAC ステレオである 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate High Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md): ビットレートが 500 kbps、音声が AAC ステレオである 1 つの MP4 ファイルを生成します。  
  
 [H264 Single Bitrate Low Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md): ビットレートが 56 kbps、音声が AAC ステレオである 1 つの MP4 ファイルを生成します。  
  
 Media Services のエンコーダーに関する情報については、[Encoding On-Demand Azure Media Services を使用したオンデマンド エンコード](./media-services-encode-asset.md)に関するページを参照してください。
