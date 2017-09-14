---
title: "Azure CLI のサンプル スクリプト - Azure Redis Cache の詳細を取得 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Azure Redis Cache の詳細を取得"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 9f4eb32227bd8a68837eabd58b9d058bc4995d17
ms.contentlocale: ja-jp
ms.lasthandoff: 04/15/2017

---

# <a name="get-details-of-an-azure-redis-cache"></a>Azure Redis Cache の詳細を取得

このシナリオでは、プロビジョニング ステータスを含む、Azure Redis Cache インスタンスの詳細を取得する方法を説明します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して Azure Redis Cache インスタンスの詳細を取得します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az redis の表示](https://docs.microsoft.com/cli/azure/redis#show) | Azure Redis Cache インスタンスの詳細を取得します。 |


## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他 Azure Redis Cache の CLI サンプル スクリプトは、[Azure Redis Cache のドキュメント](../cli-samples.md)のページに掲載されています。
