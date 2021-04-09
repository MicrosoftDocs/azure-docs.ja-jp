---
title: クイック スタート:Azure Firewall Manager を使用して仮想ハブをセキュリティで保護する - Resource Manager テンプレート
description: このクイックスタートでは、Azure Firewall Manager を使用して仮想ハブをセキュリティで保護する方法を学習します。
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 050dfa737ad04127f8c72158fbecb2e8e70e05c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89075343"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>クイック スタート:Azure Firewall Manager を使用して仮想ハブのセキュリティを保護する - Resource Manager テンプレート

このクイック スタートでは、Azure Resource Manager テンプレート (Resource Manager テンプレート) を使用し、Azure Firewall Manager を使用して仮想ハブをセキュリティで保護します。 展開されたファイアウォールには、`www.microsoft.com` への接続を許可するアプリケーション ルールがあります。 ファイアウォールをテストするために、2 つの Windows Server 2019 仮想マシンが展開されています。 ワークロード サーバーへの接続には、1 台のジャンプ サーバーが使用されます。 ワークロード サーバーからは、`www.microsoft.com` にのみ接続できます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Azure Firewall Manager の詳細については、[Azure Firewall Manager の概要](overview.md)に関するページを参照してください。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>テンプレートを確認する

このテンプレートによって、Azure Firewall Manager を使用してセキュリティ保護付き仮想ハブと、そのシナリオをサポートするために必要なリソースが作成されます。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/fwm-docs-qs/)からのものです。

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

ARM テンプレートを Azure にデプロイします。

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 このテンプレートによって、Azure Firewall、仮想 WAN と仮想ハブ、ネットワーク インフラストラクチャ、2 つの仮想マシンが作成されます。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. ポータルの **[セキュリティ保護付き仮想ハブ]** ページで、次の値を入力または選択します。
   - サブスクリプション:既存のサブスクリプションから選択します 
   - リソース グループ: 既存のリソース グループから選択するか、 **[Create new]\(新規作成\)** を選択し、 **[OK]** を選択します。
   - 場所:場所を選択します。
   - 管理者ユーザー名: 管理者ユーザー アカウントのユーザー名を入力します 
   - 管理者パスワード: 管理者パスワードまたはキーを入力します

3. **[確認および作成]** を選択し、次に **[作成]** を選択します。 このデプロイの完了には、10 分以上かかる場合があります。

## <a name="validate-the-deployment"></a>デプロイの検証

今度は、ファイアウォール規則をテストして、予期したとおりに動作することを確認します。

1. Azure portal から、**Workload-Srv** 仮想マシンのネットワーク設定を確認し、プライベート IP アドレスをメモします。
2. リモート デスクトップを **Jump-Srv** 仮想マシンに接続し、サインインします。 そこから、**Workload-Srv** のプライベート IP アドレスへのリモート デスクトップ接続を開きます。

3. Internet Explorer を開き、 `www.microsoft.com` を参照します。
4. Internet Explorer のセキュリティ アラートで、 **[OK]**  >  **[閉じる]** の順に選択します。

   Microsoft のホーム ページが表示されるはずです。

5. `www.google.com` を参照します。

   ファイアウォールによってブロックされます。

これで、ファイアウォール ルールが動作していることを確認できました。

- 1 つの許可された FQDN は参照できますが、それ以外は参照できません。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ファイアウォールと共に作成したリソースが不要になった場合は、リソース グループを削除します。 これにより、ファイアウォールとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [セキュリティ パートナー プロバイダーについて](trusted-security-partners.md)
