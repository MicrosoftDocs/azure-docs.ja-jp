---
title: 'クイックスタート: Azure ファイアウォールとファイアウォール ポリシーを作成する - Resource Manager テンプレート'
description: このクイックスタートでは、Azure ファイアウォールとファイアウォール ポリシーをデプロイします。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: 9cc263d311bd550a92a0c8f14ab5ce86d72e9ee3
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633633"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>クイックスタート: Azure ファイアウォールとファイアウォール ポリシーを作成する - ARM テンプレート

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure ファイアウォールとファイアウォール ポリシーを作成します。 ファイアウォール ポリシーには、`www.microsoft.com` への接続を許可するアプリケーション ルールと、**WindowsUpdate** FQDN タグを使用して Windows Update への接続を許可するルールを含めます。 ネットワーク ルールで、タイム サーバー (13.86.101.172) への UDP 接続を許可します。

また、ルールでは、IP グループを使用して **ソース** IP アドレスを定義します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Firewall Manager については、「[Azure Firewall Manager とは](overview.md)」を参照してください。

Azure Firewall については、「[Azure Firewall とは](../firewall/overview.md)」を参照してください。

IP グループについては、「[Azure Firewall での IP グループ](../firewall/ip-groups.md)」を参照してください。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>テンプレートを確認する

このテンプレートによって、ハブ仮想ネットワークと、そのシナリオをサポートするために必要なリソースが作成されます。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/)からのものです。

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

ARM テンプレートを Azure にデプロイします。

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 このテンプレートによって、Azure Firewall、仮想 WAN と仮想ハブ、ネットワーク インフラストラクチャ、2 つの仮想マシンが作成されます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. ポータルの **[Create a Firewall and FirewallPolicy with Rules and Ipgroups]\(ルールと IP グループを使用してファイアウォールとファイアウォール ポリシーを作成する\)** ページで、次の値を入力または選択します。
   - サブスクリプション:既存のサブスクリプションから選択します。
   - リソース グループ: 既存のリソース グループから選択するか、 **[Create new]\(新規作成\)** を選択し、 **[OK]** を選択します。
   - [リージョン] :リージョンを選択します。
   - [ファイアウォール名]: ファイアウォールの名前を入力します。

3. **[確認および作成]** を選択し、次に **[作成]** を選択します。 このデプロイの完了には、10 分以上かかる場合があります。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

デプロイが完了すると、次のようなリソースが表示されます。

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="デプロイされるリソース":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ファイアウォールと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、ファイアウォールとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Firewall Manager ポリシーの概要](policy-overview.md)
