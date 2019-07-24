---
title: Azure Resource Manager テンプレートでの Key Vault シークレット | Microsoft Docs
description: デプロイメント時にパラメーターとして Key Vault からシークレットを渡す方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 93b92a8a3b8aacd1f665725643314858fe92ad3c
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233770"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す

パラメーター ファイルに安全な値 (パスワードなど) を直接置く代わりに、デプロイ時に、[Azure Key Vault](../key-vault/key-vault-whatis.md) から値を取得できます。 値を取得するには、キー コンテナーとパラメーター ファイル内のシークレットを参照します。 参照するのは Key Vault ID だけであるため、値が公開されることはありません。 キー コンテナーは、デプロイ先のリソース グループとは異なるサブスクリプションに存在していてもかまいません。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-key-vaults-and-secrets"></a>キー コンテナーとシークレットをデプロイする

Key Vault とシークレットを作成する方法については、次をご覧ください。

- [CLI を使用したシークレットの設定と取得](../key-vault/quick-create-cli.md)
- [PowerShell を使用したシークレットの設定と取得](../key-vault/quick-create-powershell.md)
- [ポータルを使用したシークレットの設定と取得](../key-vault/quick-create-portal.md)
- [.NET を使用したシークレットの設定と取得](../key-vault/quick-create-net.md)
- [Node.js を使用したシークレットの設定と取得](../key-vault/quick-create-node.md)

Key Vault と Resource Manager テンプレート デプロイの統合には、他にも考慮事項と要件がいくつかあります。

- `enabledForTemplateDeployment` はキー コンテナーのプロパティです。 Resource Manager のデプロイからこのキー コンテナー内のシークレットにアクセスするには、`enabledForTemplateDeployment` を `true` にする必要があります。 
- 自分がキー コンテナーを所有していないときにシークレットを追加するには、所有者がキー コンテナー セキュリティ ポリシー設定を更新する必要があります。

Azure CLI と Azure PowerShell の次のサンプルでこの方法を確認できます。

```azurecli
# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list
```

```azurepowershell
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
```

## <a name="grant-access-to-the-secrets"></a>シークレットへのアクセスを許可する

テンプレートをデプロイするユーザーは、リソース グループとキー コンテナーなど、キー コンテナーを含むスコープに対して `Microsoft.KeyVault/vaults/deploy/action` アクセス許可を持っている必要があります。 このアクセスは、[所有者](../role-based-access-control/built-in-roles.md#owner)ロールと[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロールが許可します。 キー コンテナーを作成すると所有者になるので、そのアクセス許可持つことになります。 キー コンテナーが別のサブスクリプション以下にある場合、キー コンテナーの所有者がアクセス権を付与する必要があります。

次の手順は、最小限のアクセス許可を持つロールを作成する方法と、ユーザーを割り当てる方法を示します

1. カスタム ロール定義の JSON ファイルを作成します。

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    "00000000-0000-0000-0000-000000000000" は、テンプレートをデプロイする必要があるユーザーのサブスクリプション ID に置き換えます。

2. JSON ファイルを使用して新しいロールを作成します。

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    `New-AzRoleAssignment` サンプルでは、カスタム ロールをリソース グループ レベルのユーザーに割り当てます。  

[Managed Applications](../managed-applications/overview.md) のテンプレートで Key Vault を使用する場合は、**アプライアンス リソース プロバイダー** サービス プリンシパルにアクセス許可を付与する必要があります。 詳細については、「[Access Key Vault secret when deploying Azure Managed Applications](../managed-applications/key-vault-access.md)」(Azure Managed Applications のデプロイ時に Key Vault シークレットにアクセスする) を参照してください。

## <a name="reference-secrets-with-static-id"></a>固定 ID でのシークレットの参照

この手法では、テンプレートではなく、パラメーター ファイルでキー コンテナーを参照します。 次の図は、パラメーター ファイルがシークレットを参照し、その値をテンプレートに渡すしくみを示しています。

![Resource Manager キー コンテナー統合の固定 ID の図](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[チュートリアル:Resource Manager テンプレートのデプロイで Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)」では、この手法が利用されます。 このチュートリアルでは、管理者パスワードを含む仮想マシンをデプロイします。 パスワード パラメーターは、セキュリティで保護された文字列に設定されます。

![Resource Manager キー コンテナー統合の固定 ID のテンプレート ファイル](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-template-file.png)

ここで、前のテンプレートのためにパラメーター ファイルを作成します。 このパラメーター ファイルで、テンプレート内のパラメーターの名前に一致するパラメーターを指定します。 パラメーター値のために、キー コンテナーのシークレットを参照します。 シークレットを参照するには、Key Vault のリソース識別子とシークレットの名前を渡します。

![Resource Manager キー コンテナー統合の固定 ID のパラメーター ファイル](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-parameter-file.png)

現在のバージョン以外のバージョンのシークレットを使用する必要がある場合は、`secretVersion` プロパティを使用します。

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

テンプレートをデプロイし、パラメーター ファイルを渡します。

Azure CLI では、次を使用します。

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

PowerShell では、次を使用します。

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>動的 ID でのシークレットの参照

前のセクションでは、Key Vault シークレットの静的リソース ID をパラメーターから渡す方法について説明しました。 しかし参照すべき Key Vault シークレットがデプロイごとに変わる状況も考えられます。 または、パラメーター ファイルに参照パラメーターを作成するのではなく、テンプレートでパラメーター値を渡すこともできます。 いずれの場合でも、リンクされたテンプレートを使用して、キー コンテナー シークレットのリソース ID を動的に生成することができます。

パラメーター ファイルではテンプレート式が使用できないので、パラメーター ファイルでリソース ID を動的に生成することはできません。 

親テンプレートに、そのリンクされたテンプレートを追加し、動的に生成されたリソース ID をパラメーターに格納して渡します。 次の図は、リンクされたテンプレート内のパラメーターがシークレットを参照するしくみを示しています。

![動的 ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

[次のテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id)は、動的に key vault ID を作成し、パラメーターとして渡します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

上記のテンプレートをデプロイし、パラメーターの値を指定します。 GitHub のサンプル テンプレートを使用できますが、環境に合わせたパラメーター値を指定する必要があります。

Azure CLI では、次を使用します。

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

PowerShell では、次を使用します。

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>次の手順

- キー コンテナーの一般的な情報については、「[Azure Key Vault とは](../key-vault/key-vault-overview.md)」をご覧ください。
- キー シークレットの詳細な参照例については、 [Key Vault の例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)を参照してください。
