---
title: Azure Resource Manager テンプレートを使用して Azure Attestation 証明書を作成する
description: Azure Resource Manager テンプレートを使用して Azure Attestation 証明書を作成する方法について説明します。
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 08/31/2020
ms.openlocfilehash: a73294e41f27a15d70ab95b3aa7b100cab5320b4
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237117"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して Azure Attestation プロバイダーを作成する

[Microsoft Azure Attestation](overview.md) は、信頼できる実行環境 (TEE) を証明するためのソリューションです。 このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) をデプロイして Microsoft Azure Attestation ポリシーを作成する過程を中心に取り上げます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)


## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-attestation-provider-create)からのものです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "name": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "tags": {
      "type": "Object"
    },
    "policySigningCertificates": {
      "type": "string"
    }
  },
  "variables": {
    "PolicySigningCertificates": {
      "PolicySigningCertificates": {
        "keys": [
          {
            "kty": "RSA",
            "use": "sig",
            "x5c": [ "[parameters('policySigningCertificates')]" ]
          }
        ]
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('name')]",
      "location": "[parameters('location')]",
      "type": "Microsoft.Attestation/attestationProviders",
      "tags": "[parameters('tags')]",
      "Properties": "[if(empty(parameters('policySigningCertificates')), json('{}'), variables('PolicySigningCertificates'))]"
    }
  ]
}
```

テンプレート内に定義されている Azure リソース:
- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次の画像を選択します。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

2. 次の値を選択または入力します。

    指定がない場合、既定値を使用して構成証明プロバイダーを作成してください。

    * **name**:Azure Attestation プロバイダーの名前を選択します。
    * **location**:場所を選択します。 たとえば **[米国中部]** です。
    * **tags**: 場所を選択します。 たとえば **[米国中部]** です。

3. **[購入]** を選択します。 構成証明リソースが正常にデプロイされると、通知が表示されます。

テンプレートをデプロイするには Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

Azure portal を使用して構成証明リソースをチェックできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure Attestation に関する他の記事は、このクイックスタートに基づいて作成されています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。

不要になったら、リソース グループを削除してください。リソース グループを削除すれば、Attestation リソースが削除されます。 Azure CLI または Azure PowerShell を使用してリソース グループを削除するには、次を実行します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用して構成証明リソースを作成し、デプロイを検証しました。 Azure Attestation の詳細については、[Azure Attestation の概要](overview.md)に関するページを参照してください。
