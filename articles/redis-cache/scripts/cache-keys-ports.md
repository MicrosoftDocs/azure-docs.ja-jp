---
title: "Azure CLI スクリプトの例 - Azure Redis Cache のホスト名、ポート、およびキーを取得する | Microsoft Docs"
description: "Azure CLI スクリプトの例 - Azure Redis Cache インスタンスのホスト名、ポート、およびキーを取得する"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: cd9adc784bceb0fff5e7c2bbee2be0950c51c8f6
ms.contentlocale: ja-jp
ms.lasthandoff: 04/15/2017

---

# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Azure Redis Cache のホスト名、ポート、およびキーを取得する

このシナリオでは、Azure Redis Cache インスタンスに接続するために使用するホスト名、ポート、およびキーを取得する方法について学習します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis Cache")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して Azure Redis Cache インスタンスのホスト名、キー、およびポートを取得します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az redis の表示](https://docs.microsoft.com/cli/azure/redis#show) | Azure Redis Cache インスタンスの詳細を取得。 |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#list-keys) | Azure Redis Cache インスタンスのアクセス キーを取得。 |


## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Redis Cache の CLI サンプル スクリプトは、[Azure Redis Cache のドキュメント](../cli-samples.md)のページにあります。
