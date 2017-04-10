---
title: "Azure Media Services .NET SDK を使用したフィルターの作成"
description: "このトピックでは、クライアントがストリームの特定のセクションをストリームする際に使用できるフィルターを作成する方法について説明します。 Media Services では、動的マニフェストを作成してこの選択型ストリーミングをアーカイブします。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/18/2016
ms.author: juliako;cenkdin
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 07239a392438afd9807ce69fba657c27e853a219
ms.lasthandoff: 02/16/2017


---
# <a name="creating-filters-with-azure-media-services-net-sdk"></a>Azure Media Services .NET SDK を使用したフィルターの作成
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST ()](media-services-rest-dynamic-manifest.md)
> 
> 

Media Services のリリース 2.11 以降では、資産にフィルターを定義できます。 これらのフィルターは、ビデオの 1 つのセクションのみの再生や (ビデオ全体を再生するのではなく)、顧客のデバイスが処理できるオーディオ サブセットとビデオ演奏のみの再生 (資産に関連付けられているすべての演奏ではなく) などを顧客が選択できるようにする、サーバー側のルールです。 この資産のフィルター処理は**動的マニフェスト**によって実行されます。これは、指定したフィルターに基づいてビデオをストリームする必要がある顧客のリクエストに応じて作成されます。

フィルターと動的マニフェストに関連する詳細については、「 [動的マニフェストの概要](media-services-dynamic-manifest-overview.md)」をご覧ください。

このトピックでは、Media Services .NET SDK を使用してフィルターを作成、更新、削除する方法を説明します。 

フィルターを更新する場合、ストリーミング エンドポイントでルールを更新するのに最大 2 分かかることに注意してください。 このフィルターを使用してコンテンツが処理された場合 (また、プロキシと CDN にキャッシュされている場合)、このフィルターを更新するとプレイヤーでエラーが発生します。 フィルターを更新した後にキャッシュをクリアすることをお勧めします。 このオプションが利用できない場合は、別のフィルターを使用することを検討してください。 

## <a name="types-used-to-create-filters"></a>フィルターの作成に使用する種類
次の種類の REST API を使用してフィルターを作成します。 

* **IStreamingFilter**。  この種類は次の REST API [Filter](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**。 この種類は次の REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**。 この種類は次の REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** と **IFilterTrackPropertyCondition**。 これらの種類は次の REST API [FilterTrackSelect と FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>グローバル フィルターの作成、更新、読み取り、削除
次のコードは .NET を使用し、資産フィルターを作成、更新、読み取り、削除する方法を示しています。

    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


## <a name="createupdatereaddelete-asset-filters"></a>資産フィルターの作成、更新、読み取り、削除
次のコードは .NET を使用し、資産フィルターを作成、更新、読み取り、削除する方法を示しています。

    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();




## <a name="build-streaming-urls-that-use-filters"></a>フィルターを使用するストリーミング URL の構築
資産の発行と配信方法については、「 [顧客へのコンテンツの配信に関する概要](media-services-deliver-content-overview.md)」をご覧ください。

次の例では、ストリーミング URL にフィルターを追加する方法を示します。

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[動的マニフェストの概要](media-services-dynamic-manifest-overview.md)


