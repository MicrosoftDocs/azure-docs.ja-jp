---
title: "Azure CLI スクリプト サンプル - クラスタリングを使用する Premium Azure Redis Cache の作成 | Microsoft Docs"
description: "Azure CLI スクリプト サンプル - クラスタリングを使用する Premium レベル Azure Redis Cache の作成"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 87d0fe4c3eaa8f7b75343a36a069ecdac8241d74
ms.contentlocale: ja-jp
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-premium-azure-redis-cache-with-clustering"></a>クラスタリングを使用する Premium Azure Redis Cache の作成

このシナリオでは、クラスタリングが有効でシャードが 2 つある、6 GB の Premium レベル Azure Redis Cache を作成する方法を説明します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループと、クラスタリングが有効な Premium レベルの Redis Cache を作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az redis create](https://docs.microsoft.com/cli/azure/redis#create) | Redis Cache インスタンスを作成します。 |


## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

Azure Redis Cache のその他の CLI スクリプト サンプルは、[Azure Redis Cache のドキュメント](../cli-samples.md)内にあります。
