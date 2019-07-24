---
title: Azure CLI サンプル - カスタム VM イメージを使用する | Microsoft Docs
description: Azure CLI のサンプル
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 637a2e48ca95b5d1a456f9a48f7c8e9008b1d14d
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55697744"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-the-azure-cli"></a>Azure CLI を使用してカスタム VM イメージから仮想マシン スケール セットを作成する
このスクリプトでは、カスタム VM イメージを VM インスタンスのソースとして使用する仮想マシン スケール セットを作成します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.sh "Create a virtual machine scale set with a custom VM image")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
次のコマンドを実行して、リソース グループ、スケール セット、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン スケール セット、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/ad/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vmss create](/cli/azure/vmss) | 仮想マシン スケール セットを作成し、仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループに接続します。 複数の VM インスタンスにトラフィックを分散するために、ロード バランサーも作成されます。 このコマンドでは、使用する VM イメージと管理者の資格情報も指定します。  |
| [az group delete](/cli/azure/ad/group) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン スケール セット用の Azure CLI サンプル スクリプトは、[Azure 仮想マシン スケール セットのドキュメント](../cli-samples.md)にあります。
