---
title: Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure DDoS Protection プランを作成し、有効にする
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure DDoS Protection プランを作成し、有効にする方法について説明します。
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 75d6c484a0f1d6325aaa7894d8902ff78cadbd74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99092502"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>クイックスタート: ARM テンプレートを使用して Azure DDoS Protection Standard を作成する

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、分散型サービス拒否 (DDoS) 攻撃に対する保護計画と仮想ネットワーク (VNet) を作成し、その VNet に対して保護計画を有効にする方法について説明します。 Azure DDoS Protection Standard プランでは、サブスクリプションの境界を超えて DDoS 保護が有効になった一連の仮想ネットワークが定義されます。 組織で 1 つの DDoS Protection プランを構成し、複数のサブスクリプションから同じプランに仮想ネットワークをリンクできます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans)からのものです。

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

このテンプレートでは、次の 2 つのリソースが定義されます。

- [Microsoft.Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

この例では、テンプレートで新しいリソース グループ、DDoS 保護プラン、VNet を作成します。

1. Azure にサインインしてテンプレートを開くには、 **[Azure へのデプロイ]** ボタンを選択します。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. 新しいリソース グループ、DDoS 保護プラン、VNet の名前を作成するための値を入力します。

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="DDoS クイックスタート テンプレート。":::

    - **サブスクリプション**:リソースがデプロイされる Azure サブスクリプションの名前。
    - **[リソース グループ]** :既存のリソース グループを選択するか、新しいリソース グループを作成します。
    - **[リージョン]** :リソース グループがデプロイされるリージョン (米国東部など)。
    - **[Ddos Protection Plan Name]\(DDoS 保護プランの名前\)** : 新しい DDoS 保護プランの名前。
    - **仮想ネットワーク名**: 新しい VNet の名前を作成します。
    - **[場所]** :リソースのデプロイ用リソース グループと同じリージョンを使用する関数。
    - **[Vnet Address Prefix]\(VNet のアドレス プレフィックス\)** : 既定値を使用するか、ご利用の VNet アドレスを入力します。
    - **[Subnet Prefix]\(サブネットのプレフィックス\)** : 既定値を使用するか、ご利用の VNet サブネットを入力します。
    - **[Ddos Protection Plan Enabled]\(DDoS 保護プランが有効\)** : 既定値は `true` です。DDoS 保護プランが有効になります。

1. **[確認と作成]** を選択します。
1. テンプレートの検証に成功したことを確認し、 **[作成]** を選択してデプロイを開始します。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure CLI または Azure PowerShell コマンドをコピーするには、 **[コピー]** ボタンを選択します。 **[使ってみる]** ボタンをクリックすると、コマンドを実行するための Azure Cloud Shell が開きます。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

出力結果として新しいリソースが表示されます。

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作業が完了したら、リソースを削除してください。 コマンドによって、リソース グループと含まれるすべてのリソースが削除されます。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>次のステップ

DDoS 保護プラン用にテレメトリを表示および構成する方法を学習するには、チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [DDoS 保護テレメトリの表示と構成](telemetry.md)
