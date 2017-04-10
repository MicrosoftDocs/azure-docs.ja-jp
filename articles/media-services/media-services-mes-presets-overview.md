---
title: "MES (Media Encoder Standard) 用のタスク プリセット | Microsoft Docs"
description: "このトピックでは、MES (Media Encoder Standard) 用のタスク プリセットの概要を説明します。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: e9018ffa74c2895560adbd4c0d2b83ee67e9eb1c
ms.lasthandoff: 03/03/2017


---

# <a name="task-presets-for-mes-media-encoder-standard"></a>MES (Media Encoder Standard) 用のタスク プリセット

**Media Encoder Standard** で定義されている一連のエンコード プリセットを使って、エンコード ジョブを作成できます。 Media Services でのストリーミング用にビデオをエンコードする場合は、"アダプティブ ストリーミング" プリセットを使うことをお勧めします。 このプリセットを指定すると、Media Encoder Standard は[ビットレート ラダーを自動生成](media-services-autogen-bitrate-ladder-with-mes.md)します。 

ただし、エンコード プリセットをカスタマイズする必要がある場合は、このセクションで定義されているエンコード プリセットのいずれかをカスタム構成のテンプレートとして使う必要があります。 これらのプリセット内の各要素の意味と各要素に有効な値の説明については、「[Media Encoder Standard スキーマ](media-services-mes-schema.md)」を参照してください。  
  
> [!NOTE]
>  4k エンコードのプリセットを使用する場合は、`S3` 予約ユニットの種類を取得する必要があります。 詳細については、「[Encoding の規模の設定方法](https://azure.microsoft.com/en-us/documentation/articles/media-services-portal-encoding-units)」を参照してください。  
  
Media Encoder Standard では、回転は既定で有効になっています。 ビデオがスマート フォンやその他のモバイル デバイスで縦モードで記録されている場合、これらのプリセットでは、ビデオはエンコードの実行前に既定で横モードに回転されます (この動作は、ビデオの回転を手動で操作する Azure Media Encoder とは異なります。[こちらのブログ](http://azure.microsoft.com/blog/2014/08/21/advanced-encoding-features-in-azure-media-encoder/)の「Video Rotation (ビデオの回転)」を参照してください)。  
  
使用可能なプリセット:  
  
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
  
 Media Services のエンコーダーに関する情報については、[Encoding On-Demand Azure Media Services を使用したオンデマンド エンコード](https://azure.microsoft.com/en-us/documentation/articles/media-services-encode-asset/)に関するページを参照してください。

