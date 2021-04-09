---
title: テンプレートを使用して VM にタグを付ける方法
description: テンプレートを使用して仮想マシンにタグを付ける方法について説明します。
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: e7dd75a025b76773a0bf1e3b4f752b5a77db6786
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897376"
---
# <a name="tagging-a-vm-using-a-template"></a>テンプレートを使用して VM にタグを付ける

この記事では、Resource Manager テンプレートを使用して Azure の VM にタグを付ける方法について説明します。 タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。 現在、Azure では、1 つのリソースまたはリソース グループにつき最大 50 個のタグがサポートされます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。

[このテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) では、Compute (仮想マシン)、Storage (ストレージ アカウント)、および Network (パブリック IP アドレス、Virtual Network、およびネットワーク インターフェイス) というリソースにタグを設定します。 このテンプレートは Windows VM のものですが、Linux VM 用に調整できます。

[テンプレート リンク](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags)から **[Deploy to Azure (Azure へのデプロイ)]** ボタンをクリックします。 [Azure Portal](https://portal.azure.com/) が開きます。ポータルで、このテンプレートをデプロイできます。

![タグを使用した単純なデプロイ](./media/tag/deploy-to-azure-tags.png)

このテンプレートには、*Department*、*Application*、および *Created By* というタグが含まれています。 別のタグ名を使いたい場合は、テンプレートにタグを追加したり、タグを編集したりすることができます。

![テンプレートの Azure タグ](./media/tag/azure-tags-in-a-template.png)

上の例のように、タグはキーと値のペアとして定義され、コロン (:) で区切られています。 タグは次の形式で定義する必要があります。

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

選択したタグの編集を完了したら、テンプレート ファイルを保存します。

次に、 **[パラメーターの編集]** セクションで、タグの値を入力します。

![Azure ポータルでタグを編集する](./media/tag/edit-tags-in-azure-portal.png)

**[作成]** をクリックして、タグの値が指定されたこのテンプレートをデプロイします。

### <a name="next-steps"></a>次のステップ

- Azure リソースへのタグ付けについて詳しくは、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」と「[タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)」をご覧ください。
- Azure リソースの使用を管理するときにタグを役立てる方法については、「[Azure の課金内容の確認」](../cost-management-billing/understand/review-individual-bill.md)を参照してください。
