---
title: Azure CLI のサンプル スクリプト - ACS クラスターのスケーリング | Microsoft Docs
description: Azure CLI のサンプル スクリプト - ACS クラスターのスケーリング
services: container-service
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: acs, azure-container-service
keywords: Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 199a2f09df57878d6523cfed6873001f190e14f5
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52992236"
---
# <a name="deprecated-scale-an-azure-container-service-cluster"></a>(非推奨) Azure Container Service クラスターのスケーリング

[!INCLUDE [ACS deprecation](../../../../includes/container-service-kubernetes-deprecation.md)]

このサンプルでは、Azure Container Service クラスターがスケーリングされます。 

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

```azurecli
az acs scale --resource-group myResourceGroup --name myK8SCluster --new-agent-count 5
```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az acs scale](/cli/azure/acs#az-acs-scale) | ACS クラスターをスケーリングします。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の Azure Container Service の CLI サンプル スクリプトは、[Azure Container Service のドキュメント](../cli-samples.md)のページにあります。

