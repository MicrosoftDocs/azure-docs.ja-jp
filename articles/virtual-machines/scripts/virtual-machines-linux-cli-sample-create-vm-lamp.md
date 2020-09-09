---
title: Azure CLI のサンプル スクリプト - 負荷分散された仮想マシン スケール セットでの LAMP スタックのデプロイ
description: カスタム スクリプト拡張機能を使用して、LAMP スタックを Azure の負荷分散された仮想マシン スケール セットにデプロイします。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.author: cynthn
tags: azure-service-management
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.custom: mvc, devx-track-azurecli
ms.date: 04/05/2017
ms.openlocfilehash: e71b2f940c637a8b1375be71f9b6ca95fd76628e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501867"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>負荷分散された仮想マシン スケール セットでの LAMP スタックのデプロイ

この例では、仮想マシン スケール セットを作成し、カスタム スクリプトを実行する拡張機能を適用して、各仮想マシンのスケール セットに LAMP スタックをデプロイします。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>接続する

このコードを使用して、VM とスケール セットに接続する方法を表示します。

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、スケール セット、VM、およびすべての関連リソースを削除します。

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、可用性セット、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vmss create](/cli/azure/vmss) | 仮想マシンスケール セットを作成します。 |
| [az network lb rule create](/cli/azure/network/lb/rule) | 負荷分散エンドポイントを追加します。 |
| [az vmss extension set](/cli/azure/vmss/extension) | 仮想マシンのデプロイ時にカスタム スクリプトを実行する拡張機能を作成します。 |
| [az vmss update-instances](/cli/azure/vmss) | 拡張機能をスケール セットに適用する前にデプロイされた VM インスタンスで、カスタム スクリプトを実行します。 |
| [az vmss scale](/cli/azure/vmss) | VM インスタンスを追加してスケール セットをスケール アップします。 デプロイ時に、カスタム スクリプトでこれらが実行されます。 |
| [az network public-ip list](/cli/azure/network/public-ip) | サンプルで作成された VM の IP アドレスを取得します。 |
| [az network lb show](/cli/azure/network/lb) | ロード バランサーで使用されるフロントエンド ポートおよびバックエンド ポートを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。
