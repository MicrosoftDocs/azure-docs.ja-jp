---
title: Azure Media Services .NET SDK を使用したフィルターの作成
description: このトピックでは、クライアントがストリームの特定のセクションをストリームする際に使用できるフィルターを作成する方法について説明します。 Media Services では、動的マニフェストを作成してこの選択型ストリーミングをアーカイブします。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 2bcb8762b94347f4409507fb89a18cb6c9d0dacd
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494306"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Media Services .NET SDK を使用してフィルターを作成する

コンテンツを顧客に配信 (ライブ イベントやビデオ オン デマンドをストリーム配信) する際、アセットの既定のマニフェスト ファイルに記述された内容だけではクライアントのニーズに柔軟に対応できない場合があります。 Azure Media Services では、アカウント フィルターと、コンテンツのアセットフィルターを定義することができます。 

この機能と、この機能が使用されているシナリオの詳細については、[動的マニフェスト](filters-dynamic-manifest-overview.md)と[フィルター](filters-concept.md)に関する記事を参照してください。

このトピックでは、Media Services .NET SDK を使用してビデオ オン デマンド アセットにフィルターを定義し、[アカウント フィルター](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet)と[アセット フィルター](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet)を作成する方法について説明します。 

> [!NOTE]
> [presentationTimeRange](filters-concept.md#presentationtimerange) を必ず確認してください。

## <a name="prerequisites"></a>前提条件 

- [フィルターと動的マニフェスト](filters-dynamic-manifest-overview.md)を確認します。
- [Media Services アカウントを作成する](create-account-cli-how-to.md) リソース グループ名と Media Services アカウント名を覚えておいてください。 
- [API へのアクセス](access-api-cli-how-to.md)に必要な情報を取得する
- [.NET SDK の使用を始める](stream-files-tutorial-with-api.md#start_using_dotnet)方法については、[Azure Media Services を使用してビデオのアップロード、エンコード、ストリーム配信を行う方法](stream-files-tutorial-with-api.md)に関するページを参照してください。

## <a name="define-a-filter"></a>フィルターの定義  

.NET では、[FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) クラスと [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) クラスを使用してトラックの選択を構成します。 

次のコードでは、EC-3 であるオーディオ トラックと、0-1000000 の範囲でビットレートであるビデオ トラックを含むフィルターが定義されています。

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>アカウント フィルターの作成

次のコードは、.NET を使用して、[上記で定義](#define-a-filter)した全トラックの選択を含むアカウント フィルターを作成する方法を示しています。 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>アセットフィルターの作成

次のコードは、.NET を使用して、[上記で定義](#define-a-filter)した全トラックの選択を含むアセットフィルターを作成する方法を示しています。 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>フィルターをストリーミング ロケーターに関連付ける

アセットまたはアカウント フィルターの一覧を指定できます。これはストリーミング ロケーターに適用されます。 [ダイナミック パッケージャー (ストリーミング エンドポイント)](dynamic-packaging-overview.md) では、クライアントで URL に指定されるフィルターと共にこのフィルターの一覧が適用されます。 この組み合わせによって、URL 内のフィルターとストリーミング ロケーターに指定されたフィルターに基づく[動的マニフェスト](filters-dynamic-manifest-overview.md)が生成されます。 フィルターを適用したいものの URL でフィルター名を公開したくない場合は、この機能を使用することをお勧めします。

次の C# コードは、ストリーミング ロケーターを作成し、`StreamingLocator.Filters` を指定する方法を示します。 これは、フィルター名の `IList<string>` を受け取るオプションのプロパティです。

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>フィルターを使用するストリーム

フィルターを定義すると、クライアントからストリーミング URL で使用できるようになります。 フィルターは、アダプティブ ビットレート ストリーミング プロトコル(Apple HTTP Live Streaming (HLS)、MPEG DASH、Smooth Streaming) に適用できます。

次の表に、フィルターを含んだ URL の例をいくつか示します。

|Protocol|例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|スムーズ ストリーミング|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>次の手順

[ビデオのストリーム配信](stream-files-tutorial-with-api.md) 


