---
title: Azure CLI のサンプル スクリプト - VHD を使用した VM の作成
description: Azure CLI のサンプル スクリプト - 仮想ハード ディスクを使用して VM を作成します。
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
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6f5e30b0d6a072f9a40ae57ebe325461cdfe5bb1
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479625"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>仮想ハード ディスクを使用した VM の作成

この例では、VHD を使用して仮想マシンを作成します。
ここではリソース グループ、ストレージ アカウント、コンテナーを作成し、VHD を作成したコンテナーにアップロードすることで VM を作成します。
ssh 公開キーをお使いの公開キーに置き換えることで、VM にアクセスできるようになります。

起動可能な VHD が必要です。 スクリプトは `~/sample.vhd` を探します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、可用性セット、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az storage account list](/cli/azure/storage/account) | ストレージ アカウントの一覧を表示します。 |
| [az storage account check-name](/cli/azure/storage/account) | ストレージ アカウントの名前が有効で、存在していないことを確認します。 |
| [az storage account keys list](/cli/azure/storage/account/keys) | ストレージ アカウントのキーの一覧を表示します。 |
| [az storage blob exists](/cli/azure/storage/blob) | BLOB が存在するかどうかを確認します。 |
| [az storage container create](/cli/azure/storage/container) | ストレージ アカウントにコンテナーを作成します。 |
| [az storage blob upload](/cli/azure/storage/blob) | VHD をアップロードすることで、コンテナーに BLOB を作成します。 |
| [az vm list](/cli/azure/vm) | `--query` チェックと併用して VM 名が使用中かどうかを確認します。 | 
| [az vm create](/cli/azure/vm/availability-set) | 仮想マシンを作成します。 |
| [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) | 作成した VM の IP アドレスを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。
