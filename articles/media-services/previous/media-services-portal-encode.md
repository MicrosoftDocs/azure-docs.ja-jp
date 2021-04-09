---
title: Azure Portal での Media Encoder Standard を使用した資産のエンコード | Microsoft Docs
description: このチュートリアルでは、Azure Portal で Media Encoder Standard を使用して資産をエンコードする手順について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5f4bb3c9b23ffd68939f1088b1252c6e31c1dad7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010509"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Azure Portal での Media Encoder Standard を使用した資産のエンコード

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
> 
> 

Azure Media Services の代表的な用途の 1 つが、クライアントに対するアダプティブ ビットレート ストリーミング配信です。 Media Services は、Apple HTTP ライブ ストリーミング (HLS)、Microsoft Smooth Streaming、および Dynamic Adaptive Streaming over HTTP (DASH、MPEG-DASH とも呼ばれます) のアダプティブ ビットレート ストリーミング テクノロジをサポートしています。 アダプティブ ビットレート ストリーミング用にビデオを準備するには、最初にソース ビデオをマルチビットレートのファイルとしてエンコードします。 Media Encoder Standard を使用してビデオをエンコードできます。  

Media Services では、ダイナミック パッケージが使用できます。 ダイナミック パッケージを使用すると、HLS、Smooth Streaming、および MPEG-DASH で、マルチビットレート MP4 を配信できます。これらのストリーミング形式でパッケージを再作成する必要はありません。 ダイナミック パッケージを使用すると、単一のストレージ形式のファイルに対して保存と支払を行うことができます。 Media Services は、クライアントの要求に応じて適切な応答を作成して返します。

ダイナミック パッケージを利用するには、ソース ファイルを一連のマルチビットレート MP4 ファイルにエンコードする必要があります。 エンコードの手順は、この記事の後半で説明します。

メディア処理をスケーリングする方法については、[Azure Portal を使用したメディア処理のスケーリング](media-services-portal-scale-media-processing.md)に関するページを参照してください。

## <a name="encode-in-the-azure-portal"></a>Azure Portal でエンコードする

Media Encoder Standard を使用してコンテンツをエンコードするには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]**  >  **[アセット]** を参照してください。 エンコードするアセットを選択します。
3. **[エンコード]** を選択します。
4. **[アセットのエンコード]** ウィンドウで、**Media Encoder Standard** プロセッサとプリセットを選択します。 プリセットについては、[ビットレート ラダーの自動生成](media-services-autogen-bitrate-ladder-with-mes.md)に関するページと [Media Encoder Standard 用のタスク プリセット](media-services-mes-presets-overview.md)に関するページを参照してください。 入力ビデオに最適なプリセットを選択することが重要です。 たとえば、入力ビデオの解像度が 1920 &#215; 1080 ピクセルであるとわかっている場合は、**H264 Multiple Bitrate 1080p** のプリセットを使用します。 低解像度 (640 &#215; 360) のビデオの場合は、**H264 Multiple Bitrate 1080p** プリセットを使用しないでください。
   
   リソースを管理しやすくするために、出力アセットの名前とジョブの名前を編集することができます。
   
   ![Encode assets](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. **［作成］** を選択します

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次のステップ
* Azure Portal で[エンコード ジョブの進行状況を監視](media-services-portal-check-job-progress.md)します。  

