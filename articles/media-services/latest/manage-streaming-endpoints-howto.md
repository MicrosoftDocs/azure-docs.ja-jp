---
title: Azure Media Services v3 でストリーミング エンドポイントを管理する
description: この記事では、Azure Media Services v3 を使用してストリーミング エンドポイントを管理する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 8d3835d78ec55f120f6b9439aea9c29434e6bd47
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289479"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Media Services v3 でストリーミング エンドポイントを管理する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services アカウントの作成時に、**既定**の[ストリーミング エンドポイント](streaming-endpoint-concept.md)が**停止**状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、[ダイナミック パッケージ](dynamic-packaging-overview.md)と[動的暗号化](content-protection-overview.md)を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。

この記事では、さまざまな手法を利用し、ストリーミング エンドポイントで [start](/rest/api/media/streamingendpoints/start) コマンドを実行する方法について説明します。 
 
> [!NOTE]
> ストリーミング エンドポイントが実行状態の場合のみ課金されます。
    
## <a name="prerequisites"></a>前提条件

レビュー: 

* [Media Services の概念](concepts-overview.md)
* [ストリーミング エンドポイントの概念](streaming-endpoint-concept.md)
* [ダイナミック パッケージ](dynamic-packaging-overview.md)

## <a name="use-rest"></a>REST の使用

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

詳細については、次を参照してください。 

* 「[StreamingEndpoint - 開始](/rest/api/media/streamingendpoints/start)」参照ドキュメント。
* ストリーミング エンドポイントの開始は非同期操作です。 

    長時間実行される操作を監視する方法については、「[長時間にわたって実行される操作](media-services-apis-overview.md)」を参照してください。
* こちらの [Postman コレクション](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json)には、ストリーミング エンドポイントを開始する方法など、さまざまな REST 操作の例が含まれています。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用 
 
1. [Azure portal](https://portal.azure.com/) にサインインします。
1. Azure Media Services アカウントに移動します。
1. 左側のペインで、 **[ストリーミング エンドポイント]** を選択します。
1. 開始するストリーミング エンドポイントを選択し、 **[開始]** を選択します。

## <a name="use-the-azure-cli"></a>Azure CLI の使用

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

詳細については、「[az ams streaming-endpoint start](/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start)」を参照してください。

## <a name="use-sdks"></a>SDK の使用

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

完全な [Java コード サンプル](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)を参照してください。

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

完全な [.NET コード サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112)を参照してください。

---

## <a name="next-steps"></a>次のステップ

* [Media Services v3 OpenAPI の仕様 (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [ストリーミング エンドポイント操作](/rest/api/media/streamingendpoints)
