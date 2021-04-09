---
title: クイック スタート:可用性ゾーンを使用して Azure Firewall を作成する - Resource Manager テンプレート
description: このクイックスタートでは、テンプレートを使用して Azure Firewall をデプロイします。 仮想ネットワークには、3 つのサブネットを含む 1 つの VNet があります。 ジャンプ ボックスおよびサーバーとして、2 つの Windows Server 仮想マシンをデプロイします。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 478f3454a728871040cdbbf9f817394cffe6b82f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660255"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---arm-template"></a>クイック スタート:可用性ゾーンを使用して Azure Firewall をデプロイする - ARM テンプレート

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、3 つの可用性ゾーンに Azure Firewall をデプロイします。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

このテンプレートでは、ファイアウォールを使用したテスト ネットワーク環境を作成します。 ネットワークには、次の 3 つのサブネットを含む 1 つの仮想ネットワーク (VNet) があります。*AzureFirewallSubnet*、*ServersSubnet*、*JumpboxSubnet*。 *ServersSubnet* と *JumpboxSubnet* には、それぞれ 1 つの 2 コア Windows Server 仮想マシンがあります。

ファイアウォールは、*AzureFirewallSubnet* サブネット内にあり、`www.microsoft.com` へのアクセスを許可する単一のルールを含むアプリケーション ルール コレクションを備えています。

ユーザー定義のルートは、ファイアウォール規則が適用されるファイアウォールを経由する *ServersSubnet* サブネットからのネットワーク トラフィックを指します。

Azure Firewall の詳細については、[Azure portal を使用した Azure Firewall のデプロイと構成](tutorial-firewall-deploy-portal.md)に関するページを参照してください。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>テンプレートを確認する

このテンプレートは、可用性ゾーンを使用して Azure Firewall を作成すると共に、Azure Firewall をサポートするために必要なリソースも作成します。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox)からのものです。

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

ARM テンプレートを Azure にデプロイします。

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 このテンプレートは、Azure Firewall、ネットワーク インフラストラクチャ、2 つの仮想マシンを作成します。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. ポータルの **[Create a sandbox setup of Azure Firewall with Zones]\(ゾーンを使用した Azure Firewall のサンドボックス設定の作成\)** ページで、次の値を入力または選択します。
   - **[リソース グループ]** : **[新規作成]** を選択して、リソース グループの名前を入力し、 **[OK]** を選択します。 
   - **仮想ネットワーク名**: 新しい VNet の名前を入力します。
   - **管理ユーザー名**: 管理者ユーザー アカウントのユーザー名を入力します。
   - **管理パスワード**: 管理者パスワードを入力します。

3. 使用条件を読み、 **[上記の使用条件に同意する]** をオンにして、 **[購入]** を選択します。 このデプロイの完了には、10 分以上かかる場合があります。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

ファイアウォールを使用して作成されたリソースを探索します。

テンプレートにあるファイアウォールの JSON の構文とプロパティに関する詳細については、[Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)に関する記事を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースが必要なくなった場合は、PowerShell コマンド `Remove-AzResourceGroup` を実行して、リソース グループ、ファイアウォール、およびすべての関連リソースを削除できます。 *MyResourceGroup* という名前のリソース グループを削除するには、次を実行します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

引き続きファイアウォール監視チュートリアルに進む場合は、リソース グループとファイアウォールを削除しないでください。 

## <a name="next-steps"></a>次のステップ

次に、Azure Firewall のログを監視することができます。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Firewall のログを監視する](./firewall-diagnostics.md)