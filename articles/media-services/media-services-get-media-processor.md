---
title: "メディア プロセッサを作成する方法 | Microsoft Docs"
description: "メディア プロセッサ コンポーネントを作成し、Azure Media Services 用にメディア コンテンツのエンコード、形式の変換、暗号化、または復号化を行う方法について説明します。 コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cc3c3bff6179b634b82884dfcb3f8f8395f6bb89


---
# <a name="how-to-get-a-media-processor-instance"></a>方法: メディア プロセッサ インスタンスを取得する
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST ()](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Overview
メディア プロセッサは、Media Services のコンポーネントとして、メディア コンテンツのエンコード、形式変換、暗号化、復号化など、特定の処理タスクを担います。 通常、メディア コンテンツのエンコード、暗号化、形式変換を行うタスクの作成時にメディア プロセッサを作成します。

次の表は、利用可能なメディア プロセッサの名前と説明の一覧です。

| メディア プロセッサ名 | Description | 詳細情報 |
| --- | --- | --- |
| メディア エンコーダー スタンダード |オンデマンド エンコードのための標準機能を備えています。 |[Azure オンデマンド メディア エンコーダーの概要と比較](media-services-encode-asset.md) |
| メディア エンコーダー Premium ワークフロー |メディア エンコーダー Premium ワークフローを使用してエンコード タスクを実行できます。 |[Azure オンデマンド メディア エンコーダーの概要と比較](media-services-encode-asset.md) |
| Azure Media Indexer |メディア ファイルとコンテンツを検索可能にすると共に、クローズド キャプション トラックの生成を可能にします。 |[Azure Media Indexer](media-services-index-content.md) |
| Azure Media Hyperlapse (プレビュー) |ビデオ安定化を使用して、ビデオの "凸凹" を取り除いて滑らかにすることができます。 コンテンツをすばやく使用可能なクリップにすることもできます。 |[Azure Media Hyperlapse](media-services-hyperlapse-content.md) |
| Azure Media Encoder |償却対象 | |
| Storage Decryption |償却対象 | |
| Azure Media Packager |償却対象 | |
| Azure Media Encryptor |償却対象 | |

## <a name="get-media-processor"></a>メディア プロセッサの取得
次のメソッドは、メディア プロセッサ インスタンスを取得する方法を示しています。 このコード例では、[Media Services にプログラムから接続する方法](media-services-dotnet-connect-programmatically.md)に関するページで説明しているように、モジュール レベルの変数 **_context** を使用してサーバー コンテキストを参照しています。

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




<!--HONumber=Nov16_HO3-->


