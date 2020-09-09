---
title: Azure CLI サンプル スクリプト - 内部 NSG と外部 NSG が設定された 2 つの VM の作成
description: Azure CLI サンプル スクリプト - 内部 NSG と外部 NSG が設定された 2 つの VM の作成
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0d0e3dd53c592c991e342240b9c973e1677969d6
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498380"
---
# <a name="secure-network-traffic-between-virtual-machines"></a>仮想マシン間のネットワーク トラフィックのセキュリティ保護

このスクリプトでは、2 つの仮想マシンを作成し、両マシンへの受信トラフィックをセキュリティで保護します。 1 つ目の仮想マシンはインターネット上でアクセス可能にし、ポート 3389 とポート 80 のトラフィックを許可するようにネットワーク セキュリティ グループ (NSG) を構成します。 2 つ目の仮想マシンはインターネット上でアクセスできないようにし、1 つ目の仮想マシンからのトラフィックのみを許可するように NSG を構成します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](/cli/azure/network/vnet) | Azure 仮想ネットワークとサブネットを作成します。 |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet) | サブネットを作成します。 |
| [az vm create](/cli/azure/vm) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびNSG に接続します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az network nsg rule update](/cli/azure/network/nsg/rule) | NSG 規則を更新します。 このサンプルでは、フロントエンド サブネットからのトラフィックのみを通すようにバックエンド規則を更新します。 |
| [az network nsg rule list](/cli/azure/network/nsg/rule) | ネットワーク セキュリティ グループ規則に関する情報を返します。 このサンプルでは、後でスクリプト内で使用するために規則名を変数に格納しています。 |
| [az group delete](/cli/azure/vm/extension) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)のページにあります。
