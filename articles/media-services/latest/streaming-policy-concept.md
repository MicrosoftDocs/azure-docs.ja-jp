---
title: Azure Media Services のストリーミング ポリシー | Microsoft Docs
description: この記事では、ストリーミング ポリシーとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: a813c77e81e51bfe13e75ed6c8d0e24b4d0fa645
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392923"
---
# <a name="streaming-policies"></a>ストリーミング ポリシー

Azure Media Services v3 では、[ストリーミング ポリシー](https://docs.microsoft.com/rest/api/media/streamingpolicies)を使用して、[ストリーミング ロケーター](streaming-locators-concept.md)のためのストリーミング プロトコルと暗号化オプションを定義できます。 Media Services v3 には、試用または運用環境で直接使用できるように、いくつかの定義済みのストリーミングポリシーが用意されています。 

現在利用できる定義済みのストリーミング ポリシー:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

次の "デシジョン ツリー" を使用して、自分のシナリオに適した定義済みのストリーミング ポリシーを選択できます。

> [!IMPORTANT]
> * Datetime 型の**ストリーミング ポリシー**のプロパティは、常に UTC 形式です。
> * お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 詳細については、「[クォータと制限](limits-quotas-constraints.md)」をご覧ください。

## <a name="decision-tree"></a>デシジョン ツリー

画像をクリックすると、フル サイズで表示されます。  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

コンテンツを暗号化する場合、[コンテンツ キー ポリシー](content-key-policy-concept.md)を作成する必要がありますが、**コンテンツ キー ポリシー**はクリアなストリーミングまたはダウンロードには必要ありません。 

特別な要件がある (例: 異なるプロトコルを指定したい、カスタム キー配信サービスを使用する必要がある、クリアなオーディオ トラックを使用する必要がある) 場合は、カスタム ストリーミング ポリシーを[作成](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)できます。 

## <a name="get-a-streaming-policy-definition"></a>ストリーミング ポリシー定義を取得する  

ストリーミング ポリシーの定義を表示する場合は、[Get](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) を使用し、ポリシー名を指定します。 例:

### <a name="rest"></a>REST

要求:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

応答:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
* [AES-128 動的暗号化とキー配信サービスの使用](protect-with-aes128.md)
* [DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)
