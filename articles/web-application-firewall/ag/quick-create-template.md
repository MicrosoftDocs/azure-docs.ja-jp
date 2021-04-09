---
title: クイック スタート:Application Gateway に Azure WAF v2 を作成する - Azure Resource Manager テンプレート
titleSuffix: Azure Application Gateway
description: Azure Resource Manager クイック スタート テンプレート (ARM テンプレート) を使用して、Azure Application Gateway に Web アプリケーション ファイアウォール v2 を作成する方法について説明します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: victorh
ms.custom: subject-armqs
ms.openlocfilehash: 4c5d5c4cafee9402e2afd7ddc047fd3b68e84540
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92091323"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway-using-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Application Gateway に Azure WAF v2 を作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Application Gateway に Azure Web アプリケーション ファイアウォール v2 を作成します。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>テンプレートを確認する

このテンプレートは、Azure Application Gateway に 単純な Web アプリケーション ファイアウォール v2 を作成します。 これには、パブリック IP フロントエンド IP アドレス、HTTP 設定、ポート 80 の基本リスナーのルール、バックエンド プールが含まれます。 IP アドレスの一致の種類に基づいてバックエンド プールへのトラフィックをブロックするために、カスタム ルールを持つ WAF ポリシーが作成されます。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/ag-docs-wafv2/)からのものです。

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json":::

このテンプレートには、次の複数の Azure リソースが定義されています。

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): アプリケーション ゲートウェイ用に 1 つと仮想マシン用に 2 つ。
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : 仮想マシン用に 2 つ。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : 仮想マシン用に 2 つ。
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : IIS と Web ページの構成用。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

ARM テンプレートを Azure にデプロイします。

1. **[Azure に配置する]** を選択して Azure にサインインし、テンプレートを開きます。 このテンプレートにより、アプリケーション ゲートウェイ、ネットワーク インフラストラクチャ、IIS を実行しているバックエンドプールに 2 つの仮想マシンが作成されます。

   [![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. リソース グループを選択または作成します。
3. **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。 このデプロイの完了には、10 分以上かかる場合があります。

## <a name="validate-the-deployment"></a>デプロイの検証

アプリケーション ゲートウェイの作成に IIS は必要はありませんが、これは Azure がアプリケーション ゲートウェイに WAF v2 を正常に作成したかどうかを確認するためにバックエンド サーバーにインストールされます。

IIS を使用してアプリケーション ゲートウェイをテストします。

1. アプリケーション ゲートウェイのパブリック IP アドレスをその **[概要]** ページで見つけます。![アプリケーション ゲートウェイのパブリック IP アドレスを記録する](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) または、 **[すべてのリソース]** を選択し、検索ボックスに「*myAGPublicIPAddress*」と入力してから、検索結果でそれを選択することができます。 Azure によって、 **[概要]** ページにパブリック IP アドレスが表示されます。
2. パブリック IP アドレスをコピーし、お使いのブラウザーのアドレス バーに貼り付けて、その IP アドレスを開きます。
3. 応答を確認します。 **403 Forbidden** (403 禁止) 応答により、WAF が正常に作成されたこと、およびバックエンド プールへの接続をブロックしていることが確認されます。
4. カスタム ルールを **[トラフィックを許可する]** に変更します。
  リソース グループ名 (your resource group name) を置き換えて、次の Azure PowerShell スクリプトを実行します。
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   ブラウザーを何度か更新すると、myVM1 と myVM2 の両方への接続が表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、リソース グループを削除します。 これによりアプリケーション ゲートウェイとすべての関連リソースが削除されます。

リソース グループを削除するには、`Remove-AzResourceGroup` コマンドレットを呼び出します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:Azure portal を使用して Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する](application-gateway-web-application-firewall-portal.md)