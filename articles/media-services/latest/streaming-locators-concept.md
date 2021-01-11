---
title: Azure Media Services のストリーミング ロケーター | Microsoft Docs
description: この記事では、ストリーミング ロケーターとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 68df5256949db44b2b347002570c64d1aa0d55ea
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297962"
---
# <a name="streaming-locators"></a>ストリーミング ロケーター

出力アセット内のビデオをクライアントが再生できるようにするには、[ストリーミング ロケーター](/rest/api/media/streaminglocators)を作成し、ストリーミング URL をビルドする必要があります。 URL を作成するには、ストリーミング エンドポイントのホスト名とストリーミング ロケーター パスを連結する必要があります。 .NET のサンプルについては、「[Get a Streaming Locator](stream-files-tutorial-with-api.md#get-a-streaming-locator)」(ストリーミング ロケーターを取得する) を参照してください。

**ストリーミング ロケーター** を作成するプロセスは発行と呼ばれます。 既定では、**ストリーミング ロケーター** は API 呼び出しを行うとすぐに有効になり、省略可能な開始時刻と終了時刻を構成しない限り、削除されるまで存続します。 

**ストリーミング ロケーター**を作成するときに、**アセット**名と**ストリーミング ポリシー**名を指定する必要があります。 詳細については、次のトピックを参照してください。

* [アセット](assets-concept.md)
* [ストリーミング ポリシー](streaming-policy-concept.md)
* [コンテンツ キー ポリシー](content-key-policy-concept.md)

ストリーミング ロケーターで開始時刻と終了時刻を指定することもできます。これにより、ユーザーはこれらの時刻の間 (例: 2019 年 5 月 1 日から 2019 年 5 月 5 日の間) のコンテンツのみを再生できます。  

## <a name="considerations"></a>考慮事項

* **ストリーミング ロケーター**は更新できません。 
* Datetime 型の**ストリーミング ロケーター**のプロパティは、常に UTC 形式です。
* お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 詳しくは、[クォータと制限](limits-quotas-constraints.md)に関するトピックを参照してください。

## <a name="create-streaming-locators"></a>ストリーミング ロケーターの作成  

### <a name="not-encrypted"></a>暗号化なし

平文 (暗号化されていない) でファイルをストリーミングする場合は、定義済みの平文のストリーミング ポリシーを 'Predefined_ClearStreamingOnly' に設定します (.NET では、PredefinedStreamingPolicy.ClearStreamingOnly 列挙型を使用できます)。

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

### <a name="encrypted"></a>Encrypted 

コンテンツを CENC 暗号化を使用してコンテンツを暗号化する必要がある場合は、ポリシーを 'Predefined_MultiDrmCencStreaming' に設定します。 Widevine 暗号化は DASH ストリームに適用され、PlayReady は Smooth に適用されます。 このキーは、構成された DRM ライセンスに基づいて再生クライアントに配信されます。

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

また、CBCS (FairPlay) でも HLS ストリームを暗号化する場合は、'Predefined_MultiDrmStreaming' を使用します。

> [!NOTE]
> Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="associate-filters-with-streaming-locators"></a>フィルターをストリーミング ロケーターに関連付ける

「[フィルターをストリーミング ロケーターに関連付ける](filters-concept.md#associating-filters-with-streaming-locator)」を参照してください。

## <a name="filter-order-page-streaming-locator-entities"></a>ストリーミング ロケーター エンティティのフィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="list-streaming-locators-by-asset-name"></a>アセット名別のストリーミング ロケーターの一覧

関連するアセット名に基づくストリーミング ロケーターを取得するには、次の操作を使用します。

|Language|API|
|---|---|
|REST|[liststreaminglocators](/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>関連項目

* [アセット](assets-concept.md)
* [ストリーミング ポリシー](streaming-policy-concept.md)
* [コンテンツ キー ポリシー](content-key-policy-concept.md)
* [チュートリアル:.NET を使用してビデオをアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>次のステップ

[ストリーミング ロケーターを作成して URL を構築する方法](create-streaming-locator-build-url.md)
