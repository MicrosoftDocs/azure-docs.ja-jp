---
title: Azure CLI サンプル スクリプト - CLI を使用してマネージド ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する | Microsoft Docs
description: Azure CLI サンプル スクリプト - CLI を使用してマネージド ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する
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
ms.openlocfilehash: d52e710cadbc37b8ad6c1de61f75df3b96704daa
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243909"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>CLI を使用してマネージド ディスクのスナップショットを同じまたは別のサブスクリプションにコピーする

このスクリプトは、同じまたは別のサブスクリプションに、マネージド ディスクのスナップショットをコピーします。 次のシナリオでこのスクリプトを使用します。

1. Premium ストレージ (Premium_LRS) のスナップショットを Standard ストレージ (Standard_LRS または Standard_ZRS) に移行してコストを削減する。
1. 信頼性が高い ZRS ストレージを利用するために、ローカル冗長ストレージ (Premium_LRS、Standard_LRS) からゾーンの冗長ストレージ (Standard_ZRS) にスナップショットを移行する。
1. 長期間の保存のために、スナップショットを同じリージョン内の別のサブスクリプションに移動する。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、コピー元スナップショットの ID を使って、コピー先のサブスクリプションにスナップショットを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | スナップショットの名前とリソース グループのプロパティを使用して、そのスナップショットのすべてのプロパティを取得します。 ID プロパティを使用して、別のサブスクリプションにそのスナップショットをコピーします。  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | 親スナップショットの名前と ID を使用して別のサブスクリプションにスナップショットを作成することで、スナップショットをコピーします。  |

## <a name="next-steps"></a>次の手順

[スナップショットから仮想マシンを作成する](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の仮想マシンとマネージド ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。
