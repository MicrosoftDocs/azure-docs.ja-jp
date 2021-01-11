---
title: Azure CLI のサンプル スクリプト - オペレーティング システム ディスクのマウント
description: Azure CLI のサンプル スクリプト - オペレーティング システム ディスクのマウント
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
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 735b16ad054edfea7cf0ad2e1e5b9c76b664e6c3
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479638"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>VM オペレーティング システム ディスクのトラブルシューティング

このスクリプトでは、障害が発生したかまたは問題のある仮想マシンのオペレーティング システム ディスクを、2 つ目の仮想マシンにデータ ディスクとしてマウントします。 これは、ディスクの問題のトラブルシューティングやデータの復元に役立ちます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az vm show](/cli/azure/vm) | 仮想マシンの一覧を返します。 このサンプルでは、仮想マシンのオペレーティング システム ディスクを返すクエリ オプションを使用しています。 返された値は、変数名 "uri" に追加されます。 |
| [az vm delete](/cli/azure/vm) | 仮想マシンを削除します。 |
| [az vm create](/cli/azure/vm) | 仮想マシンを作成します。  |
| [az vm disk attach](/cli/azure/vm/disk) | ディスクを仮想マシンに接続します。 |
| [az vm list-ip-addresses](/cli/azure/vm) | 仮想マシンの IP アドレスを返します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。
