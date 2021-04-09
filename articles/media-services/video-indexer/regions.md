---
title: Video Indexer を利用できるリージョン - Azure
titleSuffix: Azure Media Services
description: この記事では、Azure Media Services Video Indexer を使用できる Azure リージョンについて説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530941"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Video Indexer が存在する Azure リージョン

Video Indexer API には、呼び出しがルーティングされる Azure リージョンを設定する必要がある **location** パラメーターがあります。 これは、[Video Indexer が利用できる Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)にする必要があります。

## <a name="locations"></a>場所

`location` パラメーターには、その値として Azure リージョンのコード名を指定する必要があります。 Video Indexer をプレビュー モードで使用する場合は、値として `"trial"` を指定する必要があります。 `trial` は、`location` パラメーターの既定値です。 それ以外の場合は、お使いのアカウントが存在し、呼び出しがルーティングされる Azure リージョンのコード名を取得するために、Azure portal を使用するか、[Azure CLI](/cli/azure) コマンドを実行できます。

### <a name="azure-portal"></a>Azure portal

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトにサインインします。
1. ページの右上隅にある **[ユーザー アカウント]** を選択します。
1. 右上隅で、アカウントの場所を見つけます。  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="場所":::
    
###  <a name="cli-command"></a>CLI コマンド

```azurecli-interactive
az account list-locations
```

上記の行を実行すると、すべての Azure リージョンの一覧が取得されます。 探している *displayName* がある Azure リージョンに移動し、その *name* 値を **location** パラメーターで使用します。

たとえば、Azure リージョンの米国西部 2 の場合 (下に示されています)、"westus2" を **location** パラメーターで使用します。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>次のステップ

- [API を使用して言語モデルをカスタマイズする](customize-language-model-with-api.md)
- [API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)
- [API を使用して人物モデルをカスタマイズする](customize-person-model-with-api.md)
