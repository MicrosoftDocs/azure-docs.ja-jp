---
title: Azure Media Services のストリーミング ポリシー | Microsoft Docs
description: この記事では、ストリーミング ポリシーとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746746"
---
# <a name="streaming-policies"></a>ストリーミング ポリシー

Azure Media Services v3 では、[ストリーミング ポリシー](https://docs.microsoft.com/rest/api/media/streamingpolicies)を使用して、[ストリーミング ロケーター](streaming-locators-concept.md)のためのストリーミング プロトコルと暗号化オプションを定義できます。 定義済みのストリーミング ポリシーまたは作成済みのカスタム ポリシーのいずれかを使用できます。 現在利用できる定義済みのストリーミング ポリシーは次のとおりです。'Predefined_DownloadOnly'、'Predefined_ClearStreamingOnly'、'Predefined_DownloadAndClearStreaming'、'Predefined_ClearKey'、'Predefined_MultiDrmCencStreaming' および 'Predefined_MultiDrmStreaming'

> [!IMPORTANT]
> * Datetime 型の**ストリーミング ポリシー**のプロパティは、常に UTC 形式です。
> * お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 

## <a name="examples"></a>例

### <a name="not-encrypted"></a>暗号化なし

平文 (暗号化されていない) でファイルをストリーミングする場合は、定義済みの平文のストリーミング ポリシーを 'Predefined_ClearStreamingOnly' に設定します (.NET では、PredefinedStreamingPolicy.ClearStreamingOnly を使用できます)。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>暗号化 

エンベロープと CENC 暗号化を使用してコンテンツを暗号化する必要がある場合は、ポリシーを 'Predefined_MultiDrmCencStreaming' に設定してください。 このポリシーは、2 つのコンテンツ キー (エンベロープおよび CENC) を生成してロケーターに設定する必要があることを示しています。 このため、エンベロープ、PlayReady および Widevine の暗号化が適用されます (キーは構成済みの DRM ライセンスに基づいて再生クライアントに配信されます)。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

また、CBCS (FairPlay) でもストリームを暗号化する場合は、'Predefined_MultiDrmStreaming' を使用します。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
* [AES-128 動的暗号化とキー配信サービスの使用](protect-with-aes128.md)
* [DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)
