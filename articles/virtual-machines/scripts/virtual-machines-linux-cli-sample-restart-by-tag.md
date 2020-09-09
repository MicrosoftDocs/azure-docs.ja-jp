---
title: Azure CLI のサンプル スクリプト - VM の再起動
description: Azure CLI サンプル スクリプト - VM をタグと ID によって再起動する
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 03a1e44ee3bdaa168fb3eb17078bfecb1f45c443
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479491"
---
# <a name="restart-vms"></a>VM の再起動

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

このサンプルでは、いくつかの VM を取得して再起動する方法をいくつか紹介します。

1 つ目の方法では、リソース グループ内のすべての VM を再起動します。

```azurecli
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

2 つ目の方法では、`az resource list` を使用してタグ付きの VM を取得し、フィルターで VM リソースのみを選択して、それらの VM を再起動します。

```azurecli
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、「[Windows での Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli-windows)」をご覧ください。


## <a name="sample-script"></a>サンプル スクリプト

サンプルには 3 つのスクリプトがあります。
1 つ目は、仮想マシンをプロビジョニングします。
no-wait オプションを使用するため、コマンドは各 VM がプロビジョニングされるのを待たずに制御を戻します。
2 つ目は、VM が完全にプロビジョニングされるまで待機します。
3 つ目のスクリプトは、プロビジョニングされたすべての VM を再起動し、その後、タグ付きの VM だけを再起動します。

### <a name="provision-the-vms"></a>VM のプロビジョニング

このスクリプトは、リソース グループを作成し、再起動する 3 つの VM を作成します。
そのうち 2 つはタグ付きです。

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>待機

このスクリプトは、3 つの VM すべてがプロビジョニングされるか、そのうちの 1 つがプロビジョニングに失敗するまで、20 秒ごとにプロビジョニングの状態を確認します。

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>VM の再起動

このスクリプトは、リソース グループ内のすべての VM を再起動し、その後、タグ付きの VM だけを再起動します。

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli-interactive
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、可用性セット、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vm create](/cli/azure/vm/availability-set) | 仮想マシンを作成します。  |
| [az vm list](/cli/azure/vm) | `--query` と共に使用され、VM を再起動する前にプロビジョニングされるようにし、VM を再起動するために VM の ID を取得します。 |
| [az resource list](/cli/azure/vm) | `--query` と共に使用され、タグを使用して VM の ID を取得します。 |
| [az vm restart](/cli/azure/vm) | VM を再起動します。 |
| [az group delete](/cli/azure/vm/extension) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。
