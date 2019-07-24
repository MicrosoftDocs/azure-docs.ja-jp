---
title: Azure CLI のサンプル - ホスト ベースの自動スケールを有効にする | Microsoft Docs
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
ms.openlocfilehash: c843bed96ebd69f0fe96f8dab292d50ef025e750
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657352"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Azure CLI を使用して仮想マシン スケール セットを自動的にスケールする
このスクリプトでは、Ubuntu を実行する仮想マシン スケール セットを作成し、ホスト ベースのメトリックを使用して、CPU 負荷の変化に合わせて自動的にスケールします。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.sh "Automatically scale a virtual machine scale set")]

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
| [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings) | 自動スケール ルールを作成し、仮想マシン スケール セットに適用します。 |
| [az group delete](/cli/azure/ad/group) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン スケール セット用の Azure CLI サンプル スクリプトは、[Azure 仮想マシン スケール セットのドキュメント](../cli-samples.md)にあります。
