---
title: Azure Media Services v3 を使用できるクラウドとリージョン | Microsoft Docs
description: この記事では、Azure Media Services v3 を使用できる Azure クラウドとリージョンについて説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 7b2691f543cf38a56eefb1e8521169aeccbf3221
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409281"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Azure Media Services v3 が存在するクラウドとリージョン

Azure Media Services v3 は、Azure Resource Manager マニフェストを通して、グローバル Azure、Azure Government、Azure Germany、Azure China 21Vianet で使用できます。 ただし、すべての Azure クラウドで Media Services のすべての機能を使用できるわけではありません。 このドキュメントでは、Media Services v3 の主要コンポーネントの使用可能性を示します。

## <a name="feature-availability-in-azure-clouds"></a>Azure クラウドで使用できる機能

| 機能|グローバル Azure リージョン | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | 使用可能 | 使用できません。 | 使用できません。 | 使用できません。 |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  使用可能 | 使用できません。 | 使用できません。 | 使用できません。 |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  使用可能 | 使用できません。 | 使用できません。 | 使用できません。 |
| [StandardEncoderPreset](encoding-concept.md) | 使用可能 | 使用可能 | 使用可能 | 使用可能 |
| [LiveEvents](live-streaming-overview.md) | 使用可能 | 使用可能 | 使用可能 | 使用可能 |
| [StreamingEndpoints](streaming-endpoint-concept.md) | 使用可能 | 使用可能 | 使用可能 | 使用可能 |

## <a name="regionsgeographieslocations"></a>リージョン/地域/場所

[Azure Media Services サービスがデプロイされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>リージョン コード名 

**location** パラメーターを指定する必要がある場合は、**location** 値としてリージョンのコード名を指定する必要があります。 お使いのアカウントが存在し、呼び出しがルーティングされるリージョンのコード名を取得するには、次の行を [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) で実行できます

```bash
az account list-locations
```

上記の行を実行すると、すべての Azure リージョンの一覧が取得されます。 探している *displayName* がある Azure リージョンに移動し、その *name* 値を **location** パラメーターで使用します。

たとえば、Azure リージョンの米国西部 2 の場合 (下に示されています)、"westus2" を **location** パラメーターで使用します。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>エンドポイント  

異なる国の Azure クラウドから Media Services アカウントに接続する際に、次のエンドポイントを知っておくことが重要です。

### <a name="global-azure"></a>グローバル Azure

|エンドポイント ||
| --- | --- | 
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` | 
| トークン対象ユーザー | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|エンドポイント||
| --- | --- | 
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` | 
| トークン対象ユーザー | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Germany

| エンドポイント ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| トークン対象ユーザー | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|エンドポイント||
| --- | --- | 
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| トークン対象ユーザー |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>関連項目

* [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)
* [Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>次の手順

[Media Services v3 の概要](media-services-overview.md)
