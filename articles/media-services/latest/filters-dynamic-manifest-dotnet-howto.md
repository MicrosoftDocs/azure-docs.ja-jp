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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 3517a9c0aabf9e8ec029405f14461626d32335a7
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992277"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Media Services .NET SDK を使用してフィルターを作成する

コンテンツを顧客に配信 (ライブ イベントやビデオ オン デマンドをストリーム配信) する際、資産の既定のマニフェスト ファイルに記述された内容だけではクライアントのニーズに柔軟に対応できない場合があります。 Azure Media Services では、アカウント フィルターと、コンテンツの資産フィルターを定義することができます。 詳細については、「 [フィルターと動的マニフェスト](filters-dynamic-manifest-overview.md)」を参照してください。

このトピックでは、Media Services .NET SDK を使用してビデオ オン デマンド資産にフィルターを定義し、[アカウント フィルター](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet)と[資産 フィルター](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet)を作成する方法について説明します。 

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

## <a name="create-asset-filters"></a>資産フィルターの作成

次のコードは、.NET を使用して、[上記で定義](#define-a-filter)した全トラックの選択を含む資産フィルターを作成する方法を示しています。 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="next-steps"></a>次の手順

[ビデオのストリーム配信](stream-files-tutorial-with-api.md) 


