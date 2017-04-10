---
title: "Azure Media Services ダイナミック パッケージの概要 | Microsoft Docs"
description: "このトピックでは、ダイナミック パッケージの概要について説明します。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 0d9e4f54-5daa-45c1-bfaa-cf09ca89b812
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: da455a350f61e17425cd308d0fdc8bb5294a0b76
ms.openlocfilehash: 574921fdecdadaa48c572685f7486d4e7b1d25f4


---
# <a name="dynamic-packaging"></a>動的パッケージ
## <a name="overview"></a>概要
Microsoft Azure Media Services を使用して、多数のメディア ソース ファイルの形式、メディア ストリーミング形式、およびコンテンツ保護形式をさまざまなクライアント テクノロジ (iOS、XBOX、Silverlight、Windows 8 など) に配信できます。 これらのクライアントは異なるプロトコルを認識します。たとえば、iOS は HTTP Live Streaming (HLS) V4 形式が必要で、Silverlight および Xbox はスムーズ ストリーミングが必要です。 MPEG DASH、HLS またはスムーズ ストリーミングを認識するクライアントに提供する、一連のアダプティブ ビットレート (マルチビット レート) MP4 (ISO Base Media 14496-12) ファイルまたは一連のアダプティブ ビットレート スムーズ ストリーミング ファイルがある場合、Media Services の動的パッケージを活用する必要があります。

動的パッケージを使用すれば、一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルを含むアセットを作成するだけで済みます。 そうすれば、マニフェストまたはフラグメント要求で指定された形式に基づき、オンデマンド ストリーミング サーバーによって、ユーザーが選択したプロトコルでストリームを受信するようになります。 その結果、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて、適切な応答を構築して返します。

次の図は、従来のエンコードおよび静的パッケージングのワークフローを示しています。

![静的なエンコード](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

次の図は、動的パッケージのワークフローを示しています。

![動的なエンコード](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


## <a name="common-scenario"></a>一般的なシナリオ
1. 入力ファイル (中間ファイル) をアセットにアップロードします。 たとえば、H.264、MP4、または WMV などです (サポートされている形式の一覧については、「 [Media Encoder Standard の形式とコーデック](media-services-media-encoder-standard-formats.md)」を参照してください)。
2. Mezzanine ファイルを H.264 MP4 アダプティブ ビットレート セットにエンコードします。
3. オンデマンド ロケーターを作成することによって、アダプティブ ビットレート MP4 セットを含むアセットを発行します。
4. コンテンツにアクセスしてストリーミングするストリーミング URL を構築します。

## <a name="preparing-assets-for-dynamic-streaming"></a>動的ストリーミング用のアセットの準備
動的ストリーミング用にアセットを準備するには、2 つのオプションがあります。

1. [マスター ファイルをアップロードします](media-services-dotnet-upload-files.md)。
2. [Media Encoder Standard エンコーダーを使用して、H.264 MP4 アダプティブ ビットレート セットを生成します](media-services-dotnet-encode-with-media-encoder-standard.md)。
3. [コンテンツをストリーミングします](media-services-deliver-content-overview.md)。

## <a name="a-idunsupportedformatsaformats-that-are-not-supported-by-dynamic-packaging"></a><a id="unsupported_formats"></a>動的パッケージでサポートされない形式
動的パッケージでは、次のソース ファイルの形式はサポートされません。

* ドルビー デジタル mp4 ファイル。
* ドルビー デジタル スムーズ ファイル。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO4-->


