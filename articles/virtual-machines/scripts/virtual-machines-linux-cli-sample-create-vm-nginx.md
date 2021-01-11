---
title: Azure CLI のサンプル スクリプト - NGINX がインストールされた Linux VM の作成
description: Azure CLI のサンプル スクリプト - NGINX がインストールされた Linux VM の作成
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 00bbc74a78be93dd7167625fa98aa7f9500c1be2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502071"
---
# <a name="create-a-vm-with-nginx"></a>NGINX がインストールされた VM の作成

このスクリプトでは、Azure 仮想マシンを作成し、Azure 仮想マシンのカスタム スクリプト拡張機能を使用して NGINX をインストールします。 このスクリプトを実行すると、仮想マシンのパブリック IP アドレス上のデモ Web サイトにアクセスできるようになります。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "Quick Create VM")]

## <a name="custom-script-extension"></a>カスタム スクリプト拡張機能

このサンプルのカスタム スクリプト拡張機能では、次のスクリプトを仮想マシンにコピーします。 その後、このスクリプトを実行して NGINX Web サーバーをインストールおよび構成します。

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vm create](/cli/azure/vm) | 仮想マシンを作成します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az vm open-port](/cli/azure/network/nsg/rule) | 受信トラフィックを許可するネットワーク セキュリティ グループ規則を作成します。 このサンプルでは、HTTP トラフィック用にポート 80 を開きます。 |
| [azure vm extension set](/cli/azure/vm/extension) | VM に仮想マシン拡張機能を追加して実行します。 このサンプルでは、カスタム スクリプト拡張機能を使用して NGINX をインストールします。|
| [az group delete](/cli/azure/vm/extension) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。
