---
title: ARM テンプレートを使用した Azure キー コンテナーとコンテナーのアクセス ポリシーの作成
description: この記事では、Azure Resource Manager テンプレートを使用して、Azure キー コンテナーとコンテナーのアクセス ポリシーを作成する方法について説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/14/2021
ms.author: mbaldwin
ms.openlocfilehash: e489efd335d2a46a0e55d4ceb9bdee809ae6c39d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595245"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Resource Manager テンプレートを使用して Azure キー コンテナーとコンテナーのアクセス ポリシーを作成する方法

[Azure Key Vault](../general/overview.md) は、キー、パスワード、証明書などのシークレット向けに Secure Store を提供するクラウド サービスです。 この記事では、Azure Resource Manager テンプレート (ARM テンプレート) をデプロイしてキー コンテナーを作成するプロセスを説明します。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のことが条件となります。

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。


## <a name="create-a-key-vault-resource-manager-template"></a>Key Vault Resource Manager テンプレートを作成する

次のテンプレートは、キー コンテナーを作成する基本的な方法を示したものです。 一部の値は、テンプレート内で指定されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Key Vault テンプレート設定の詳細については、[Key Vault ARM テンプレート リファレンス](/azure/templates/microsoft.keyvault/vaults)に関するページを参照してください。

> [!IMPORTANT]
> テンプレートが再デプロイされると、キー コンテナー内の既存のアクセス ポリシーがオーバーライドされます。 キー コンテナーへのアクセスが失われないように、`accessPolicies` プロパティに既存のアクセス ポリシーを設定することをお勧めします。 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Key Vault Resource Manager テンプレートにアクセス ポリシーを追加する

キー コンテナー テンプレート全体を再デプロイすることなく、既存のキー コンテナーにアクセス ポリシーをデプロイできます。 次のテンプレートは、アクセス ポリシーを作成する基本的な方法を示したものです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": "[parameters('certificatePermissions')]"
            }
          }
        ]
      }
    }
  ]
}

```

Key Vault テンプレート設定の詳細については、[Key Vault ARM テンプレート リファレンス](/azure/templates/microsoft.keyvault/vaults/accesspolicies)に関するページを参照してください。

## <a name="more-key-vault-resource-manager-templates"></a>その他の Key Vault Resource Manager テンプレート

Key Vault オブジェクトで使用できる Resource Manager テンプレートが他にもあります。

| シークレット | [キー] | 証明書 |
|--|--|--|
|<ul><li>[クイックスタート](../secrets/quick-create-template.md)<li>[参照](/azure/templates/microsoft.keyvault/vaults/secrets)|該当なし|該当なし|

その他の Key Vault テンプレートについては、次を参照してください。[Key Vault Resource Manager リファレンス](/azure/templates/microsoft.keyvault/allversions)。

## <a name="deploy-the-templates"></a>テンプレートの配備

Azure portal を使うと、次に示すように、 **[エディターで独自のテンプレートを作成する]** オプションを使って上記のテンプレートをデプロイできます。次のページに説明があります:「[カスタム テンプレートからリソースをデプロイする](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)」。

また、上記のテンプレートをファイルに保存し、次のコマンドを使用することもできます。[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) と [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

後続のクイックスタートおよびチュートリアルで引き続き作業する予定がある場合は、これらのリソースをそのまま残しておくことができます。 リソースが不要になった場合は、リソース グループを削除します。 グループを削除すると、キー コンテナーと関連リソースも削除されます。 Azure CLI または Azure PowerShell を使ってリソース グループを削除するには、次の手順を実行します。

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

## <a name="resources"></a>リソース

- [Azure Key Vault の概要](../general/overview.md)を確認します。
- [Azure リソース マネージャー](../../azure-resource-manager/management/overview.md)の詳細を確認します。
- 「[Azure Key Vault のセキュリティの概要](security-overview.md)」を確認する

## <a name="next-steps"></a>次の手順

- [キー コンテナーへのアクセスをセキュリティで保護する](secure-your-key-vault.md)
- [キー コンテナーに対する認証](authentication.md)
- [Azure Key Vault 開発者ガイド](developers-guide.md)
