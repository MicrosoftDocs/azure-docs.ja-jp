---
title: Azure Linux 仮想マシンにタグを付ける方法
description: Resource Manager デプロイ モデルを使用して Azure で作成した Azure Linux 仮想マシンのタグ付けについて説明します。
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.custom: devx-track-azurecli
ms.openlocfilehash: a830f6eb604bb1173a7a552ad5f81c936f02d5fd
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500523"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Azure で Linux 仮想マシンにタグを付ける方法
この記事では、Azure で Resource Manager デプロイ モデルを通して Linux 仮想マシンにタグを付けるさまざまな方法について説明します。 タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。 現在、Azure では、1 つのリソースまたはリソース グループにつき最大 50 個のタグがサポートされます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。 タグは、Resource Manager デプロイ モデル経由で作成されたリソースでのみサポートされます。

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

## <a name="next-steps"></a>次のステップ
* Azure リソースへのタグ付けについて詳しくは、「[Azure Resource Manager の概要][Azure Resource Manager Overview]」と「[タグを使用した Azure リソースの整理][Using Tags to organize your Azure Resources]」をご覧ください。
* タグが Azure リソースの使用の管理にどのように役立つかを確認するには、[Azure の課金内容の確認][Understanding your Azure Bill]に関するページと「[Microsoft Azure リソースの消費を把握する][Gain insights into your Microsoft Azure resource consumption]」を参照してください。

[Azure CLI environment]: ../../azure-resource-manager/management/manage-resources-cli.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
