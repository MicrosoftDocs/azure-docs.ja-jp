---
title: クイック スタート:Resource Manager テンプレートを使用して Windows VM を作成する
description: このクイックスタートでは、Resource Manager テンプレートを使用して Windows 仮想マシンを作成する方法について説明します
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 06/04/2020
ms.author: cynthn
ms.custom: subject-armqs
ms.openlocfilehash: b2856c51ccc31b57ea567c5d0940cd260797f770
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560754"
---
# <a name="quickstart-create-a-windows-virtual-machine-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Windows 仮想マシンを作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure に Windows 仮想マシン (VM) をデプロイする方法を示します。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)からのものです。

:::code language="json" source="~/quickstart-templates/101-vm-simple-windows/azuredeploy.json":::


このテンプレートでは、いくつかのリソースが定義されています。

- [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/Microsoft.Network/virtualNetworks/subnets): サブネットを作成します。
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): ストレージ アカウントを作成します。
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/Microsoft.Network/publicIPAddresses): パブリック IP アドレスを作成します。
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/Microsoft.Network/networkSecurityGroups): ネットワーク セキュリティ グループを作成します。
- [**Microsoft.Network/virtualNetworks**](/azure/templates/Microsoft.Network/virtualNetworks): 仮想ネットワークを作成します。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/Microsoft.Network/networkInterfaces): NIC を作成します。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/Microsoft.Compute/virtualMachines): 仮想マシンを作成します。



## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートを使用すると、キー コンテナーとシークレットが作成されます。

    [![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-vm-simple-windows%2fazuredeploy.json)

1. 次の値を選択または入力します。 既定値がある場合には、既定値を使用します。

    - **サブスクリプション**: Azure サブスクリプションを選択します。
    - **[リソース グループ]** : ドロップダウンから既存のリソース グループを選択するか、または **[新規作成]** を選択してリソース グループの一意の名前を入力してから、 **[OK]** をクリックします。
    - **場所**: 場所を選択します。  たとえば **[米国中部]** です。
    - **管理者ユーザー名**: *azureuser* のように、ユーザー名を指定します。
    - **管理者パスワード**: 管理者アカウントに使用するパスワードを指定します。 パスワードは 12 文字以上で、[定義された複雑さの要件](faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。
    - **DNS ラベル プレフィックス**: DNS ラベルの一部として使用する一意の識別子を入力します。
    - **Windows OS バージョン**: VM 上で実行する Windows のバージョンを選択します。
    - **VM サイズ**: VM に使用する [サイズ](../sizes.md)を選択します。
    - **場所**: 既定値は、それが既に存在する場合、リソース グループと同じ場所になります。
1. **[Review + create]\(レビュー + 作成\)** を選択します。 検証が完了したら、 **[作成]** を選択して VM を作成し、デプロイします。


テンプレートをデプロイするには Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

作成した VM およびその他のリソースは、Azure portal を使用して確認できます。 デプロイが完了したら、 **[リソース グループに移動]** を選択すると、VM とその他のリソースが表示されます。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったリソース グループは削除してください。リソース グループを削除すると、そのリソース グループ内の VM とすべてのリソースが削除されます。 

1. **[リソース グループ]** を選択します。
1. リソース グループのページで、 **[削除]** を選択します。
1. プロンプトが表示されたら、リソース グループ名を入力して **[削除]** を選択します。


## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用してシンプルな仮想マシンをデプロイしました。 Azure 仮想マシンの詳細については、Linux VM のチュートリアルを参照してください。


> [!div class="nextstepaction"]
> [Azure Windows 仮想マシンのチュートリアル](./tutorial-manage-vm.md)