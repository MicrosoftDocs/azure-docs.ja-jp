---
title: Azure CLI サンプル スクリプト - マネージド ディスクを同じまたは別のサブスクリプションにコピー (移動) する| Microsoft Docs
description: Azure CLI サンプル スクリプト - マネージド ディスクを同じまたは別のサブスクリプションにコピー (移動) する
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: abb051e9646d547907384ed06413439845a29d5e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249550"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>CLI を使用した同じまたは別のサブスクリプションへのマネージド ディスクのコピー

このスクリプトは、同じサブスクリプションまたは同じリージョン内の別のサブスクリプションに、マネージド ディスクをコピーします。 コピーは、サブスクリプションが同じ AAD テナントに含まれている場合にのみ機能します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、コピー元のマネージド ディスクの ID を使ってコピー先のサブスクリプションに新しいマネージド ディスクを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | マネージド ディスクの名前とリソース グループのプロパティを使用して、そのマネージド ディスクのすべてのプロパティを取得します。 ID プロパティを使用して、別のサブスクリプションにそのマネージド ディスクをコピーします。  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | 親マネージド ディスクの名前と ID を使用して別のサブスクリプションに新しいマネージド ディスクを作成することで、マネージド ディスクをコピーします。  |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の仮想マシンとマネージド ディスクの CLI サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
