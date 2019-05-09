---
title: Azure CLI サンプル スクリプト - マネージド ディスクを同じまたは別のサブスクリプションにコピー (移動) する| Microsoft Docs
description: Azure CLI サンプル スクリプト - マネージド ディスクを同じまたは別のサブスクリプションにコピー (移動) する
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
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
ms.openlocfilehash: aea21d09aa37eb2f0902df6245e9baebfcbb37fd
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55698567"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>CLI を使用した同じまたは別のサブスクリプションへのマネージド ディスクのコピー

このスクリプトは、同じサブスクリプションまたは同じリージョン内の別のサブスクリプションに、マネージド ディスクをコピーします。 コピーは、サブスクリプションが同じ AAD テナントに含まれている場合にのみ機能します。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、コピー元のマネージド ディスクの ID を使ってコピー先のサブスクリプションに新しいマネージド ディスクを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | マネージド ディスクの名前とリソース グループのプロパティを使用して、そのマネージド ディスクのすべてのプロパティを取得します。 ID プロパティを使用して、別のサブスクリプションにそのマネージド ディスクをコピーします。  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | 親マネージド ディスクの名前と ID を使用して別のサブスクリプションに新しいマネージド ディスクを作成することで、マネージド ディスクをコピーします。  |

## <a name="next-steps"></a>次の手順

[マネージド ディスクから仮想マシンを作成する](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の仮想マシンとマネージド ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。
