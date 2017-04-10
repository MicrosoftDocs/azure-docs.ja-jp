---
title: "Azure CLI のサンプル スクリプト - VM の再起動 | Microsoft Docs"
description: "Azure CLI サンプル スクリプト - VM をタグと ID によって再起動する"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: be1c613744d510e4ace636b47fdf730462a2ae07
ms.lasthandoff: 03/15/2017

---

# <a name="restart-vms"></a>VM の再起動

このサンプルでは、いくつかの VM を取得して再起動する方法をいくつか紹介します。

1 つ目の方法では、リソース グループ内のすべての VM を再起動します。

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

2 つ目の方法では、`az resouce list` を使用してタグ付きの VM を取得し、フィルターで VM リソースのみを選択して、それらの VM を再起動します。

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../virtual-machines-windows-cli-options.md)に関する記事を参照してください。


## <a name="sample-script"></a>サンプル スクリプト

サンプルには&3; つのスクリプトがあります。
1 つ目は、仮想マシンをプロビジョニングします。
no-wait オプションを使用するため、コマンドは各 VM がプロビジョニングされるのを待たずに制御を戻します。
2 つ目は、VM が完全にプロビジョニングされるまで待機します。
3 つ目のスクリプトは、プロビジョニングされたすべての VM を再起動し、その後、タグ付きの VM だけを再起動します。

### <a name="provision-the-vms"></a>VM のプロビジョニング

このスクリプトは、リソース グループを作成し、再起動する&3; つの VM を作成します。
そのうち&2; つはタグ付きです。

[!code-azurecli[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "VM をプロビジョニングする")]

### <a name="wait"></a>待機

このスクリプトは、3 つの VM すべてがプロビジョニングされるか、そのうちの 1 つがプロビジョニングに失敗するまで、20 秒ごとにプロビジョニングの状態を確認します。

[!code-azurecli[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "VM がプロビジョニングされるまで待機する")]

### <a name="restart-the-vms"></a>VM の再起動

このスクリプトは、リソース グループ内のすべての VM を再起動し、その後、タグ付きの VM だけを再起動します。

[!code-azurecli[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "タグによって VM を再起動する")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、可用性セット、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | 仮想マシンを作成します。  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | `--query` と共に使用され、VM を再起動する前にプロビジョニングされるようにし、VM を再起動するために VM の ID を取得します。 |
| [az resource list](https://docs.microsoft.com/cli/azure/vm#list) | `--query` と共に使用され、タグを使用して VM の ID を取得します。 |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#list) | VM を再起動します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。

