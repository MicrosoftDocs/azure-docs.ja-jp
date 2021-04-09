---
title: 2 つの仮想ネットワークのピアリング - Azure CLI のスクリプト サンプル
description: このスクリプト サンプルでは、Azure CLI のスクリプト サンプルを使用して、Azure ネットワークを通じて同じリージョンに 2 つの仮想ネットワークを作成して接続します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 902d6643420ff6bdafcf52278f3a1c0df28647ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217537"
---
# <a name="peer-two-virtual-networks-with-an-azure-cli-script-sample"></a>Azure CLI のスクリプト サンプルを使用した 2 つの仮想ネットワークのピアリング

このスクリプト サンプルでは、Azure ネットワークを使用して同じリージョンに 2 つの仮想ネットワークを作成して接続します。 スクリプトを実行すると、2 つの仮想ネットワーク間のピアリングが作成されます。

Azure [Cloud Shell](https://shell.azure.com/bash) から、またはローカルの Azure CLI インストールからスクリプトを実行することができます。 ローカルで CLI を使用する場合、このスクリプトでは、バージョン 2.0.28 以降を実行する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成する必要もあります。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、およびすべての関連リソースを削除します。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、およびすべての関連リソースを作成します。 以下の表の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](/cli/azure/network/vnet) | Azure 仮想ネットワークとサブネットを作成します。 |
| [az network vnet peering create](/cli/azure/network/vnet/peering) | 2 つの仮想ネットワーク間のピアリングを作成します。  |
| [az group delete](/cli/azure/vm/extension) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

他の仮想ネットワーク CLI スクリプトのサンプルについては、[仮想ネットワーク CLI のサンプル](../cli-samples.md)に関するページを参照してください。