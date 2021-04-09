---
title: Azure Media Services v3 のクラウドとリージョン
description: この記事では、リージョンのエンドポイントとコードに使用される URL について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 74f30ebb766ea34603c0a30455837c47222967c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954329"
---
# <a name="regional-code-names-and-endpoints"></a>リージョンのコード名とエンドポイント

### <a name="region-code-name"></a>リージョン コード名

**location** パラメーターがコマンドまたは要求で使用される場合は、**location** 値としてリージョンのコード名を指定する必要があります。 お使いのアカウントが存在し、呼び出しがルーティングされるリージョンのコード名を取得するには、次の行を Azure CLI で実行できます。

```azurecli-interactive
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

| サービス | エンドポイント |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| 認証 | `https://login.microsoftonline.com/` |
| トークン対象ユーザー | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| サービス | エンドポイント |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| 認証 | `https://login.microsoftonline.us/` |
| トークン対象ユーザー | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Germany

> [!NOTE]
> Azure Germany のエンドポイントは、ドイツのソブリン クラウドにのみ適用されます。

| サービス | エンドポイント |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| 認証 | `https://login.microsoftonline.de/` |
| トークン対象ユーザー | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| サービス | エンドポイント |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| 認証 | `https://login.chinacloudapi.cn/` |
| トークン対象ユーザー |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>関連項目

* [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)
* [リージョンのコード名とエンドポイント](azure-regions-code-names.md)
* [Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>次のステップ

[Media Services v3 の概要](media-services-overview.md)
