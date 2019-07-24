---
title: Azure CLI のサンプル - スナップショットを別のリージョンのストレージ アカウントにコピーする | Microsoft Docs
description: Azure CLI サンプル スクリプト - 同じまたは別のリージョンのストレージ アカウントに、スナップショットを VHD としてエクスポート/コピーします。
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
ms.custom: mvc,seodec18
ms.openlocfilehash: 2ca70eae4a7ab14be9eba82324d41f9e5a24bcff
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727674"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>CLI を使用して別のリージョンのストレージ アカウントにスナップショットをエクスポート/コピーする

このスクリプトでは、管理対象のスナップショットを別のリージョンのストレージ アカウントにエクスポートします。 最初にスナップショットの SAS URI を生成し、それを使用して別のリージョンのストレージ アカウントにコピーします。 このスクリプトを使用して、別のリージョンでマネージド ディスクのバックアップを維持し、ディザスター リカバリーに備えます。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して管理されているスナップショットの SAS URI を生成し、SAS URI を使用してスナップショットをストレージ アカウントにコピーします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | 基になる VHD ファイルのストレージ アカウントへのコピー、またはそのオンプレミスへのダウンロードに使用する、読み取り専用の SAS を生成します  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | BLOB を、あるストレージ アカウントから別のストレージ アカウントに非同期的にコピーします |

## <a name="next-steps"></a>次の手順

[VHD からマネージド ディスクを作成する](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[マネージド ディスクから仮想マシンを作成する](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の仮想マシンとマネージド ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。
