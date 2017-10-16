---
title: ".NET 用 Azure Media Services SDK を使用してメディア プロセッサを作成する方法 | Microsoft Docs"
description: "メディア プロセッサ コンポーネントを作成し、Azure Media Services 用にメディア コンテンツのエンコード、形式の変換、暗号化、または復号化を行う方法について説明します。 コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.openlocfilehash: c2cbe41b71afa8acc184f9d7f4cfe94686de783e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-a-media-processor-instance"></a>方法: メディア プロセッサ インスタンスを取得する
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST ()](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Overview
メディア プロセッサは、Media Services のコンポーネントとして、メディア コンテンツのエンコード、形式変換、暗号化、復号化など、特定の処理タスクを担います。 通常、メディア コンテンツのエンコード、暗号化、形式変換を行うタスクの作成時にメディア プロセッサを作成します。

## <a name="azure-media-processors"></a>Azure メディア プロセッサ 

次のトピックは、メディア プロセッサの一覧です。

* [Encoding メディア プロセッサ](scenarios-and-availability.md#encoding-media-processors)
* [Analytics メディア プロセッサ](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>メディア プロセッサの取得

次のメソッドは、メディア プロセッサ インスタンスを取得する方法を示しています。 このコード例では、[Media Services にプログラムから接続する方法](media-services-use-aad-auth-to-access-ams-api.md)に関するページで説明しているように、モジュール レベルの変数 **_context** を使用してサーバー コンテキストを参照しています。

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次のステップ
これで、メディア プロセッサ インスタンスを取得する方法がわかりました。次は、[資産のエンコード方法](media-services-dotnet-encode-with-media-encoder-standard.md)に関するトピックに進んでください。このトピックでは、Media Encoder Standard を使用して資産をエンコードする方法を説明します。

