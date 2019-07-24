---
title: Azure CLI のサンプル スクリプト - Azure Cache for Redis を削除する | Microsoft Docs
description: Azure CLI のサンプル スクリプト - Azure Cache for Redis を削除する
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: d02d3196c2cbc130a2e88061df514b0bf681b1bf
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238013"
---
# <a name="delete-an-azure-cache-for-redis"></a>Azure Cache for Redis を削除する

このシナリオでは、Azure Cache for Redis を削除する方法について説明します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して Azure Cache for Redis インスタンスを削除します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis) | Azure Cache for Redis インスタンスを削除する |


## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

Azure Cache for Redis の他の CLI スクリプト サンプルは、[Azure Cache for Redis のドキュメント](../cli-samples.md)にあります。
