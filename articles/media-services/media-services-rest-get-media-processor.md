---
title: "メディア プロセッサを作成する方法 | Microsoft Docs"
description: "メディア プロセッサ コンポーネントを作成し、Azure Media Services 用にメディア コンテンツのエンコード、形式の変換、暗号化、または復号化を行う方法について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: d1d05b46591fe4b72c92c59d357681c8e1cdb336
ms.openlocfilehash: c208660fc1439ca831ada6c9bb348dbc3eadc18c


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
| Azure Media Encoder |非推奨 | |
| Storage Decryption |非推奨 | |
| Azure Media Packager |非推奨 | |
| Azure Media Encryptor |非推奨 | |

## <a name="get-mediaprocessor"></a>MediaProcessor の取得
> [!NOTE]
> Media Services REST API を使用する場合は、次のことに考慮します。
> 
> Media Services でエンティティにアクセスするときは、HTTP 要求で特定のヘッダー フィールドと値を設定する必要があります。 詳細については、「 [Media Services REST API の概要](media-services-rest-how-to-use.md)」をご覧ください。
> 
> https://media.windows.net に正常に接続されると、別の Media Services の URI を指定する 301 リダイレクトを受け取ります。 「 [Media Services REST API を使用して Media Services アカウントに接続する](media-services-rest-connect-programmatically.md)」で説明するとおり、続けて新しい URI を呼び出す必要があります。 
> 
> 

次の REST 呼び出しは、メディア プロセッサ インスタンスを名前 (ここでは **Media Encoder Standard**) で取得する方法を示しています。 

要求:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.11
    Host: media.windows.net

応答:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>次のステップ
これで、メディア プロセッサ インスタンスを取得する方法がわかりました。次は、[資産のエンコード方法](media-services-rest-get-started.md)に関するトピックに進んでください。このトピックでは、Media Encoder Standard を使用して資産をエンコードする方法を説明します。




<!--HONumber=Feb17_HO3-->


