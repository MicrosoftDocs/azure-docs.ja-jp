---
title: クイック スタート:複数のパブリック IP アドレスを使用した Azure Firewall の作成 - Resource Manager テンプレート
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、複数のパブリック IP アドレスを備えた Azure Firewall を作成する方法について説明します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 33d110f327a381241fd9dbf55996a6e3f9fd3a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92057979"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---arm-template"></a>クイック スタート:複数のパブリック IP アドレスを使用した Azure Firewall の作成 - ARM テンプレート

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、1 つのパブリック IP アドレス プレフィックスから、複数のパブリック IP アドレスがある Azure Firewall をデプロイします。 デプロイ後のファイアウォールには、2 つの Windows Server 2019 仮想マシンへの RDP 接続を許可する NAT 規則コレクション ルールが割り当てられます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

複数のパブリック IP アドレスを持つ Azure Firewall の詳細については、「[Azure PowerShell を使用して複数のパブリック IP アドレスを使用する Azure Firewall をデプロイする](deploy-multi-public-ip-powershell.md)」を参照してください。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>テンプレートを確認する

このテンプレートは、2 つのパブリック IP アドレスを持つ Azure Firewall を作成すると共に、Azure Firewall をサポートするために必要なリソースも作成します。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/fw-docs-qs)からのものです。

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPPrefix**](/azure/templates/microsoft.network/publicipprefixes)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

ARM テンプレートを Azure にデプロイします。

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 このテンプレートは、Azure Firewall、ネットワーク インフラストラクチャ、2 つの仮想マシンを作成します。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. ポータルの **[Create an Azure Firewall with multiple IP public addresses]\(複数のパブリック IP アドレスを持つ Azure Firewall の作成\)** ページで、次の値を入力または選択します。
   - サブスクリプション:既存のサブスクリプションから選択します 
   - リソース グループ: 既存のリソース グループから選択するか、 **[Create new]\(新規作成\)** を選択し、 **[OK]** を選択します。
   - 場所:場所を選択します。
   - 管理者ユーザー名: 管理者ユーザー アカウントのユーザー名を入力します 
   - 管理者パスワード: 管理者パスワードまたはキーを入力します

3. **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。 このデプロイの完了には、10 分以上かかる場合があります。

## <a name="validate-the-deployment"></a>デプロイの検証

デプロイされたリソースを Azure portal で確認します。 ファイアウォールのパブリック IP アドレスをメモしてください。  

リモート デスクトップ接続を使用して、ファイアウォールのパブリック IP アドレスに接続します。 接続に成功すれば、ファイアウォールの NAT 規則によってバックエンド サーバーへの接続が許可されていることの証明となります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ファイアウォールと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、ファイアウォールとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Azure portal を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する](tutorial-hybrid-portal.md)
