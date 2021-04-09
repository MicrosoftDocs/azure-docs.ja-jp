---
title: クイック スタート:Resource Manager テンプレートを使用して仮想ネットワークを作成する
titleSuffix: Azure Virtual Network
description: Resource Manager テンプレートを使用して、Azure 仮想ネットワークを作成する方法について説明します。
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: bc0ac1a6e882f4197828bf79c7989c16b2eb16f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217670"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>クイック スタート:仮想ネットワークを作成する - Resource Manager テンプレート

このクイックスタートでは、Azure Resource Manager テンプレートを使用して、2 つのサブネットから成る仮想ネットワークを作成する方法について説明します。 仮想ネットワークは、Azure 内のプライベート ネットワークの基本的な構成要素です。 それにより、VM などの Azure リソースは、相互に、またはインターネットと、安全に通信することができます。


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

このクイック スタートは、[Azure portal](quick-create-portal.md)、[Azure PowerShell](quick-create-powershell.md)、または [Azure CLI](quick-create-cli.md) を使用して完了することもできます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)からのものです

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

このテンプレートには、次の Azure リソースが定義されています。
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): Azure 仮想ネットワークを作成します。
-  [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualnetworks/subnets): サブネットを作成します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

Azure への Resource Manager テンプレートのデプロイ:

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 このテンプレートによって、2 つのサブネットから成る仮想ネットワークが作成されます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. ポータルの **[Create a Virtual Network with two Subnets]\(2 つのサブネットから成る仮想ネットワークの作成\)** ページで、次の値を入力または選択します。
   - **[リソース グループ]** : **[新規作成]** を選択して、リソース グループの名前を入力し、 **[OK]** を選択します。
   - **仮想ネットワーク名**: 新しい仮想ネットワークの名前を入力します。
3. **[確認と作成]** を選択し、次に **[作成]** を選択します。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

仮想ネットワークを使用して作成されたリソースを探索します。

テンプレートにある仮想ネットワークの JSON の構文とプロパティに関する詳細については、[Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks) に関する記事を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

仮想ネットワークと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、仮想ネットワークとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>次のステップ
このクイックスタートでは、2 つのサブネットをから成る Azure 仮想ネットワークをデプロイしました。 Azure 仮想ネットワークの詳細については、仮想ネットワークのチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [ネットワーク トラフィックをフィルター処理する](tutorial-filter-network-traffic.md)