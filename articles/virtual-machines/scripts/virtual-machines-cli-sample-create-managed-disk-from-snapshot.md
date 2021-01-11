---
title: スナップショットからマネージド ディスクを作成する (Linux) - CLI のサンプル
description: Azure CLI サンプル スクリプト - スナップショットからマネージド ディスクを作成する
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 7c5e9e16b81c0befb6524a284d63fb2e106bff7d
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322726"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli-linux"></a>CLI でスナップショットからマネージド ディスクを作成する (Linux)

このスクリプトでは、スナップショットからマネージド ディスクを作成します。 このスクリプトを使用して、OS またはデータ ディスクのスナップショットから仮想マシンを復元します。 OS およびデータのマネージド ディスクをそれぞれのスナップショットから作成してから、マネージド ディスクを接続することで新しい仮想マシンを作成します。 スナップショットから作成されたデータ ディスクを接続することで既存の VM のデータ ディスクを復元することもできます。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを使ってスナップショットからマネージド ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | スナップショットの名前とリソース グループのプロパティを使用して、そのスナップショットのすべてのプロパティを取得します。 Id プロパティはマネージド ディスクを作成するために使用されます。  |
| [az disk create](/cli/azure/disk) | 管理対象スナップショット Id を使用してマネージド ディスクを作成します |

## <a name="next-steps"></a>次のステップ

[マネージド ディスクを OS ディスクとして接続することで仮想マシンを作成する](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシンとマネージド ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。
