---
title: "Azure CLI のサンプル スクリプト - ACS Windows Kubernetes クラスターの作成 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - ACS Windows Kubernetes クラスターの作成"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 9ca289817b54c39c59271f35a0af26bad2811da6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---

# <a name="create-an-azure-container-service-kubernetes-windows-cluster"></a>Kubernetes for Windows を実行する Azure Container Service クラスターを作成する

このサンプルでは、Kubernetes for Windows ベースのコンテナーを実行する Azure Container Service クラスターが作成されます。

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type kubernetes \
  --resource-group myResourceGroup \
  --name myK8SCluster \
  --generate-ssh-keys \
  --admin-username azureuser \
  --admin-password Password12 \
  --windows
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
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#create) | ACS クラスターが作成されます。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の Azure Container Service の CLI サンプル スクリプトは、[Azure Container Service のドキュメント](../cli-samples.md)のページにあります。

