---
title: Azure Linux 仮想マシンにタグを付ける方法 | Microsoft Docs
description: Resource Manager デプロイ モデルを使用して Azure で作成した Azure Linux 仮想マシンのタグ付けについて説明します。
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 290105b4e5e3ac3337b0be1b7d437601223bdf68
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708745"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Azure で Linux 仮想マシンにタグを付ける方法
この記事では、Azure で Resource Manager デプロイ モデルを通して Linux 仮想マシンにタグを付けるさまざまな方法について説明します。 タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。 現在、Azure では、1 つのリソースまたはリソース グループにつき最大 15 個のタグを付けることができます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。 タグは、Resource Manager デプロイ モデル経由で作成されたリソースでのみサポートされます。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Azure CLI を使用してタグを付ける

開始するには、最新の [Azure CLI](/cli/azure/install-azure-cli) がインストールされ、[az login](/cli/azure/reference-index#az-login) を使用して Azure アカウントにログインしている必要があります。

このコマンドを使用すると、タグを含め、指定した仮想マシンのすべてのプロパティを表示できます。

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Azure CLI を使用して新しい VM タグを追加するには、タグ パラメーター **--set** を指定して `azure vm update` コマンドを実行できます。

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

タグを削除するには、`azure vm update` コマンドで **--remove** パラメーターを使用できます。

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

ここでは、Azure CLI およびポータルを使用してリソースにタグを適用しました。次は、課金ポータルの使用量の詳細でタグを確認してみましょう。

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>次の手順
* Azure リソースの詳細については、「[Azure Resource Manager の概要][Azure Resource Manager Overview]」and [Using Tags to organize your Azure Resources][Using Tags to organize your Azure Resources]を参照してください。
* Azure リソースの使用を管理するときにタグを役立てる方法については、「[Azure の課金内容の確認][Understanding your Azure Bill]」and [Gain insights into your Microsoft Azure resource consumption][Gain insights into your Microsoft Azure resource consumption]を参照してください。

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
