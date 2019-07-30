---
title: Azure Media Services REST API を使用したフィルターの作成 | Microsoft Docs
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 76e6e1595cb8bf49dbbc82c3cae5de80ea718aeb
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786445"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Media Services REST API を使用したフィルターの作成

コンテンツ (ストリーミング ライブ イベントまたはビデオ オン デマンド) を顧客に配信する場合、クライアントは既定値のアセット マニフェスト ファイルに記載されているものよりも高い柔軟性を必要とする場合があります。 Azure Media Services では、アカウント フィルターと、コンテンツの資産フィルターを定義することができます。 

この機能と、この機能が使用されているシナリオの詳細については、[動的マニフェスト](filters-dynamic-manifest-overview.md)と[フィルター](filters-concept.md)に関する記事を参照してください。

このトピックでは、ビデオ オン デマンド アセットにフィルターを定義し、REST API を使用して[アカウント フィルター](https://docs.microsoft.com/rest/api/media/accountfilters)と[アセット フィルター](https://docs.microsoft.com/rest/api/media/assetfilters)を作成する方法を示しています。 

> [!NOTE]
> [presentationTimeRange](filters-concept.md#presentationtimerange) を必ず確認してください。

## <a name="prerequisites"></a>前提条件 

このトピックで説明する手順を完了するには以下を行う必要があります。

- [フィルターと動的マニフェスト](filters-dynamic-manifest-overview.md)を確認します。
- [Azure Media Services REST API を呼び出すように Postman を構成します](media-rest-apis-with-postman.md)。

    [Azure AD トークンの取得](media-rest-apis-with-postman.md#get-azure-ad-token)に関するトピックの最後の手順を必ず実行してください。 

## <a name="define-a-filter"></a>フィルターの定義  

次に、マニフェストに追加されるトラック選択条件を定義する**要求本文**の例を示します。 このフィルターには、EC-3 のオーディオ トラックと、0 から 1,000,000 の範囲のビットレートのビデオ トラックが含まれます。

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>アカウント フィルターの作成

ダウンロードした Postman のコレクションで、**アカウント フィルター**->**作成または更新、アカウント フィルター**を選択します。

**PUT** の HTTP 要求メソッドは、以下のようになります。

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

**本文**タブを選択し、[先ほど定義した](#define-a-filter) json コードを貼り付けます。

**[送信]** を選択します。 

フィルターが作成されました。

詳細については、「 [作成または更新](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate)」を参照してください。 [フィルターに関する JSON の例](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter)も参照してください。

## <a name="create-asset-filters"></a>資産フィルターの作成  

ダウンロードした "Media Services v3" Postman コレクションで、 **[アセット]** -> **[アセット フィルターの作成または更新]** を選択します。

**PUT** の HTTP 要求メソッドは、以下のようになります。

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

**本文**タブを選択し、[先ほど定義した](#define-a-filter) json コードを貼り付けます。

**[送信]** を選択します。 

アセット フィルターが作成されました。

アセット フィルターを作成または更新する方法の詳細については、[作成または更新](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate)を参照してください。 [フィルターに関する JSON の例](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)も参照してください。 

## <a name="associate-filters-with-streaming-locator"></a>フィルターをストリーミング ロケーターに関連付ける

資産またはアカウント フィルターの一覧を指定できます。これはストリーミング ロケーターに適用されます。 [ダイナミック パッケージャー (ストリーミング エンドポイント)](dynamic-packaging-overview.md) では、クライアントで URL に指定されるフィルターと共にこのフィルターの一覧が適用されます。 この組み合わせによって、URL 内のフィルターとストリーミング ロケーターに指定されたフィルターに基づく[動的マニフェスト](filters-dynamic-manifest-overview.md)が生成されます。 フィルターを適用したいものの URL でフィルター名を公開したくない場合は、この機能を使用することをお勧めします。

REST を使用してフィルターを作成してストリーミング ロケーターに関連付けるには、[Streaming Locators - Create](https://docs.microsoft.com/rest/api/media/streaminglocators/create) API を使用し、[要求本文](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)で `properties.filters` を指定します。
                                
## <a name="stream-using-filters"></a>フィルターを使用するストリーム

フィルターを定義すると、クライアントからストリーミング URL で使用できるようになります。 フィルターは、アダプティブ ビットレート ストリーミング プロトコル(Apple HTTP Live Streaming (HLS)、MPEG DASH、Smooth Streaming) に適用できます。

次の表に、フィルターを含んだ URL の例をいくつか示します。

|Protocol|例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|スムーズ ストリーミング|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>次の手順

[ビデオのストリーム配信](stream-files-tutorial-with-rest.md) 
