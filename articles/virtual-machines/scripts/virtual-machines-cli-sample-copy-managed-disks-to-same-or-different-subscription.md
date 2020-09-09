---
title: マネージド ディスクをサブスクリプションにコピーする - CLI サンプル
description: Azure CLI サンプル スクリプト - マネージド ディスクを同じまたは別のサブスクリプションにコピーまたは移動する
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ed593b37de556d5b62cfec6b726322d12f4d8104
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051821"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>CLI を使用した同じまたは別のサブスクリプションへのマネージド ディスクのコピー

このスクリプトは、同じサブスクリプションまたは同じリージョン内の別のサブスクリプションに、マネージド ディスクをコピーします。 コピーは、サブスクリプションが同じ Azure AD テナントに含まれている場合にのみ機能します。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、コピー元のマネージド ディスクの ID を使ってコピー先のサブスクリプションに新しいマネージド ディスクを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [az disk show](/cli/azure/disk) | マネージド ディスクの名前とリソース グループのプロパティを使用して、そのマネージド ディスクのすべてのプロパティを取得します。 ID プロパティを使用して、別のサブスクリプションにそのマネージド ディスクをコピーします。  |
| [az disk create](/cli/azure/disk) | 親マネージド ディスクの名前と ID を使用して別のサブスクリプションに新しいマネージド ディスクを作成することで、マネージド ディスクをコピーします。  |

## <a name="next-steps"></a>次のステップ

[マネージド ディスクから仮想マシンを作成する](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシンとマネージド ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。
