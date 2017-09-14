---
title: "Azure CLI のサンプル スクリプト - Azure Redis Cache の削除 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Azure Redis Cache の削除"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: f959823b3a7c5b0262f693ecad1e6efc4eec4f35
ms.contentlocale: ja-jp
ms.lasthandoff: 04/15/2017

---

# <a name="delete-an-azure-redis-cache"></a>Azure Redis Cache の削除

このシナリオでは、Azure Redis Cache を削除する方法について説明します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して Azure Redis Cache インスタンスを削除します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis#delete) | Redis Cache インスタンスを削除します。 |


## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Redis Cache の CLI サンプル スクリプトは、[Azure Redis Cache のドキュメント](../cli-samples.md)のページにあります。
