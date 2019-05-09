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
ms.date: 12/17/2018
ms.author: juliako
ms.openlocfilehash: 32b9664d12d6fe3a44329665c730dbc8709430f2
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650843"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Media Services REST API を使用したフィルターの作成

コンテンツ (ストリーミング ライブ イベントまたはビデオ オン デマンド) を顧客に配信する場合、クライアントは既定値のアセット マニフェスト ファイルに記載されているものよりも高い柔軟性を必要とする場合があります。 Azure Media Services では、アカウント フィルターと、コンテンツの資産フィルターを定義することができます。 詳細については、「 [フィルターと動的マニフェスト](filters-dynamic-manifest-overview.md)」を参照してください。

このトピックでは、ビデオ オン デマンド アセットにフィルターを定義し、REST API を使用して[アカウント フィルター](https://docs.microsoft.com/rest/api/media/accountfilters)と[アセット フィルター](https://docs.microsoft.com/rest/api/media/assetfilters)を作成する方法を示しています。 

## <a name="prerequisites"></a>前提条件 

このトピックで説明する手順を完了するには以下を行う必要があります。

- [フィルターと動的マニフェスト](filters-dynamic-manifest-overview.md)を確認します。
- [Azure Media Services REST API 呼び出し用の Postman を構成する](media-rest-apis-with-postman.md)

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

詳細については、「 [作成または更新](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate)」を参照してください。 [フィルターに関する JSON の例](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter)も参照してください。

## <a name="create-asset-filters"></a>資産フィルターの作成  

ダウンロードした "Media Services v3" Postman コレクションで、**[アセット]**->**[アセット フィルターの作成または更新]** を選択します。

**PUT** の HTTP 要求メソッドは、以下のようになります。

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

**本文**タブを選択し、[先ほど定義した](#define-a-filter) json コードを貼り付けます。

**[送信]** を選択します。 

アセット フィルターが作成されました。

アセット フィルターを作成または更新する方法の詳細については、[作成または更新](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate)を参照してください。 [フィルターに関する JSON の例](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter)も参照してください。 

## <a name="next-steps"></a>次の手順

[ビデオのストリーム配信](stream-files-tutorial-with-rest.md) 
