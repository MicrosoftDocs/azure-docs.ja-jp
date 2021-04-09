---
title: Azure DevTest Labs の共有イメージを使って VM を追加する | Microsoft Docs
description: Azure DevTest Labs 内の接続されている共有イメージ ギャラリーのイメージを使って仮想マシン (VM) を追加する方法について説明します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9421a1e21be9446b0e59328bd9a3730b57655274
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483858"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>接続されている共有イメージ ギャラリーのイメージを使って VM を追加する
Azure DevTest Labs では、共有イメージ ギャラリーをラボに接続し、ラボで作成する VM のベースとしてギャラリー内のイメージを使用することができます。 共有イメージ ギャラリーをラボに接続する方法については、[共有イメージ ギャラリーの構成](configure-shared-image-gallery.md)に関する記事を参照してください。 この記事では、接続されている共有イメージ ギャラリーのイメージをベースとして使用してラボに VM を追加する方法について説明します。 

## <a name="azure-portal"></a>Azure portal
このセクションでは、Azure portal を使用して、接続されている共有イメージ ギャラリーのイメージに基づいて VM をラボに追加する方法について説明します。 このセクションでは、Azure portal を使用して VM を作成するための詳細な手順については説明しません。 それらの詳細については、「[VM の作成 - Azure portal](devtest-lab-add-vm.md)」を参照してください。 ここでは、接続されているイメージ ギャラリーからイメージを選択し、使用するイメージのバージョンを選択する手順についてのみ説明します。 

ラボに VM を追加するときに、接続されている共有イメージ ギャラリーのイメージを基本イメージとして選択できます。 

![ベースの共有イメージを選択する](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

次に、**[Create lab resource]\(ラボ リソースの作成\)** ページの **[詳細設定]** タブで、基本イメージとして使用するイメージのバージョンを選択できます。

![イメージのバージョンを選択する](./media/add-vm-use-shared-image/select-version-shared-image.png)

VM の作成後に、別のバージョンのイメージを使用するように切り替えることができます。 

## <a name="resource-manager-template"></a>Resource Manager テンプレート
Azure Resource Manager テンプレートを使用して、共有イメージ ギャラリーのイメージで仮想マシンを作成する場合は、**[プロパティ]** セクションで **[sharedImageId]** の値を指定します。 次の例を参照してください。 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Resource Manager テンプレートの完全な例については、GitHub リポジトリにある[共有イメージ ギャラリーのイメージを使用した仮想マシンの作成](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage)のサンプルを参照してください。 

## <a name="rest-api"></a>REST API

1. まず、共有イメージ ギャラリーでイメージの ID を取得する必要があります。 1 つの方法は、次の GET コマンドを使用して、接続されているイメージ ギャラリー内のすべてのイメージを一覧表示するというものです。 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. `properties.SharedImageId` への以前の呼び出しで受け取った共有イメージの ID を渡して、仮想マシンで PUT メソッドを呼び出します。

## <a name="next-steps"></a>次のステップ
共有イメージ ギャラリーをラボに接続して構成する方法については、[共有イメージ ギャラリーの構成](configure-shared-image-gallery.md)に関する記事を参照してください。