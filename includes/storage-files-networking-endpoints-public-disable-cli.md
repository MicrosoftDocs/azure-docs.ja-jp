---
title: ファイルを含める
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 195caa6f8d7c00c741741fbf80a77bd7fe5579b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465052"
---
次の CLI コマンドを使用すると、ストレージ アカウントのパブリック エンドポイントに対するすべてのトラフィックが拒否されます。 このコマンドでは、`-bypass` パラメーターが `AzureServices` に設定されていることに注意してください。 これにより、Azure File Sync などの信頼できるファーストパーティのサービスが、パブリック エンドポイント経由でストレージ アカウントにアクセスできるようになります。

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```