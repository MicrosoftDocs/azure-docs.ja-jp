---
title: CLI を使用して Azure 仮想マシンにタグを付ける方法
description: Azure CLI を使用して仮想マシンにタグを付ける方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 32d15730557c96362602b5e324254c76637ecb55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897444"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>CLI を使用して VM にタグを付ける方法

この記事では、Azure CLI を使用して VM にタグを付ける方法について説明します。 タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。 現在、Azure では、1 つのリソースまたはリソース グループにつき最大 50 個のタグがサポートされます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。 Azure [PowerShell](tag-powershell.md) を使用して仮想マシンにタグを付けることもできます。


`az vm show` を使用すると、タグを含め、指定した VM のすべてのプロパティを表示できます。

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Azure CLI を使用して新しい VM タグを追加するには、タグ パラメーター `--set` を指定して `azure vm update` コマンドを実行できます。

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

タグを削除するには、`azure vm update` コマンドで `--remove` パラメーターを使用できます。

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Azure CLI およびポータルを使用してリソースにタグを適用したので、使用量の詳細を確認して、課金ポータルにタグを表示してみましょう。

### <a name="next-steps"></a>次のステップ

- Azure リソースへのタグ付けについて詳しくは、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」と「[タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)」をご覧ください。
- Azure リソースの使用を管理するときにタグを役立てる方法については、「[Azure の課金内容の確認」](../cost-management-billing/understand/review-individual-bill.md)を参照してください。
