---
title: "Azure 仮想マシン スケール セットでの管理ディスクの使用 | Microsoft Docs"
description: "仮想マシン スケール セットで管理ディスクを使用する理由と方法について説明します。"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/21/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 4ec20a30f423d4b30e953f873100be90167f987b
ms.lasthandoff: 03/29/2017


---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Azure VM スケール セットと管理ディスク

Azure [仮想マシン スケール セット](/azure/virtual-machine-scale-sets/)で、管理ディスクを使用する仮想マシンがサポートされるようになりました。 スケール セットで管理ディスクを使用すると、次のような利点があります。

* スケール セットの VM の OS ディスクを格納するストレージ アカウントを事前に作成し、管理する必要がなくなります。

* スケール セットに管理データ ディスクを接続できます。

* 管理ディスクを使用すると、プラットフォーム イメージに基づく場合は 1,000 台の VM、カスタム イメージに基づく場合は 100 台の VM 分の容量をスケール セットで使用できます。

## <a name="get-started"></a>作業開始

管理ディスク スケール セットを使用する簡単な方法は、Azure Portal からスケール セットをデプロイすることです。 詳細については、 [こちらの記事](./virtual-machine-scale-sets-portal-create.md)を参照してください。 もう 1 つの簡単な方法として、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) を使用してスケール セットをデプロイします。 次の例は、それぞれ 50 GB と 100 GB のデータ ディスクを使用する 10 台の VM を含む、Ubuntu ベースのスケール セットを作成する方法を示しています。

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

また、[Azure Quickstart Templates GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)で `vmss` が含まれたフォルダーを探し、スケール セットをデプロイする作成済みのテンプレートの例を参照することもできます。 管理ディスクを既に使用しているテンプレートを確認するには、[こちらの一覧](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)をご覧ください。

## <a name="api-versions"></a>API のバージョン

管理ディスクを使用するスケール セットの一般公開されている最新の API バージョンは `2016-04-30-preview` です。 非管理対象ディスクを使用するスケール セットは、管理ディスクをサポートする新しい API バージョンでも、引き続き現在と同様に機能します。 ただし、非管理対象ディスクを使用するスケール セットでは、これらの新しい API バージョンでも管理ディスクの利点は得られません。

## <a name="next-steps"></a>次のステップ

管理ディスクの詳細については、[こちらの記事](../storage/storage-managed-disks-overview.md)をご覧ください。

Resource Manager テンプレートを変換して、管理ディスクを使用するスケール セットをプロビジョニングする方法については、[こちらの記事](./virtual-machine-scale-sets-convert-template-to-md.md)をご覧ください。 Resource Manager テンプレートの変更は、Azure REST API にも同様に適用されます。

スケール セットで管理データ ディスクを使用する方法の詳細については、[こちらの記事](./virtual-machine-scale-sets-attached-disks.md)をご覧ください。

大規模なスケール セットの使用を開始する場合は、[こちらの記事](./virtual-machine-scale-sets-placement-groups.md)をご覧ください。



