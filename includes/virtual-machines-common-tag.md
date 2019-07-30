---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: ccc2b574ea054a1b0ecf32a1e59691050fb66fcf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181115"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>テンプレートを使用して仮想マシンにタグを付ける
まず、テンプレートを使用してタグを付けてみましょう。 [このテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags)は、次のリソースにタグを付けます。Compute (仮想マシン)、Storage (ストレージ アカウント)、Network (パブリック IP アドレス、Virtual Network、ネットワーク インターフェイス)。 このテンプレートは Windows VM のものですが、Linux VM 用に調整できます。

[テンプレート リンク](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags)から **[Deploy to Azure (Azure へのデプロイ)]** ボタンをクリックします。 [Azure Portal](https://portal.azure.com/) が開きます。ポータルで、このテンプレートをデプロイできます。

![タグを使用した単純なデプロイ](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

このテンプレートには次のタグが含まれています。*Department*、*Application*、*Created By*。 別のタグ名を使いたい場合は、テンプレートにタグを追加したり、タグを編集したりすることができます。

![テンプレートの Azure タグ](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

上の例のように、タグはキーと値のペアとして定義され、コロン (:) で区切られています。 タグは次の形式で定義する必要があります。

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

選択したタグの編集を完了したら、テンプレート ファイルを保存します。

次に、 **[パラメーターの編集]** セクションで、タグの値を入力します。

![Azure ポータルでタグを編集する](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

**[作成]** をクリックして、タグの値が指定されたこのテンプレートをデプロイします。

## <a name="tagging-through-the-portal"></a>ポータルからタグを付ける
タグを使用してリソースを作成したら、ポータルでタグの表示、追加、および削除を行うことができます。

タグを表示するには、タグ アイコンを選択します。

![Azure ポータルのタグ アイコン](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

ポータルで新しいタグを追加するには、キーと値のペアを定義して保存します。

![Azure ポータルで新しいタグを追加する](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

リソースのタグ一覧に新しいタグが表示されます。

![Azure ポータルで保存された新しいタグ](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

