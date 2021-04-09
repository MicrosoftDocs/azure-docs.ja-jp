---
title: Key Vault のシークレットとテンプレート
description: デプロイメント時にパラメーターとして Key Vault からシークレットを渡す方法について説明します。
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 05749fe2e9179051c3183ea2e592cf7190ddb347
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889860"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す

お使いのテンプレートやパラメーター ファイルに安全な値 (パスワードなど) を直接入れる代わりに、デプロイ時に、[Azure Key Vault](../../key-vault/general/overview.md) から値を取得できます。 値を取得するには、キー コンテナーとパラメーター ファイル内のシークレットを参照します。 参照するのは Key Vault ID だけであるため、値が公開されることはありません。 キー コンテナーは、デプロイ先のリソース グループとは異なるサブスクリプションにあってもかまいません。

この記事では、テンプレート パラメーターとして機密性の高い値を渡すシナリオに焦点を当てます。 仮想マシンのプロパティをキー コンテナーの証明書の URL に設定するシナリオについては取り上げていません。 そのシナリオのクイックスタート テンプレートについては、[Azure Key Vault から証明書を仮想マシンにインストールする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)方法に関する記事を参照してください。

## <a name="deploy-key-vaults-and-secrets"></a>キー コンテナーとシークレットをデプロイする

テンプレートのデプロイ時にキー コンテナーにアクセスするには、キー コンテナーの `enabledForTemplateDeployment` を `true` に設定します。

既にキー コンテナーがある場合は、テンプレートのデプロイが許可されていることを確認してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

新しいキー コンテナーを作成してシークレットを追加するには、次のように使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

キー コンテナーの所有者には、シークレットを作成するアクセスが自動的に付与されています。 シークレットを操作しているユーザーが、そのキー コンテナーの所有者でない場合、次を使用してアクセス許可を付与します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

キー コンテナーの作成とシークレットの追加の詳細については、次を参照してください。

- [CLI を使用したシークレットの設定と取得](../../key-vault/secrets/quick-create-cli.md)
- [PowerShell を使用したシークレットの設定と取得](../../key-vault/secrets/quick-create-powershell.md)
- [ポータルを使用したシークレットの設定と取得](../../key-vault/secrets/quick-create-portal.md)
- [.NET を使用したシークレットの設定と取得](../../key-vault/secrets/quick-create-net.md)
- [Node.js を使用したシークレットの設定と取得](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>シークレットへのアクセスを許可する

テンプレートをデプロイするユーザーには、そのリソース グループとキー コンテナーのスコープで `Microsoft.KeyVault/vaults/deploy/action` のアクセス許可がある必要があります。 このアクセスは、[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールと[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロールが許可します。 キー コンテナーの作成者である場合は、その所有者であるため、そのアクセス許可を持っています。

次の手順は、最小限のアクセス許可を持つロールを作成する方法と、ユーザーを割り当てる方法を示します。

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
    "00000000-0000-0000-0000-000000000000" はサブスクリプション ID に置き換えます。

2. JSON ファイルを使用して新しいロールを作成します。

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    このサンプルでは、リソース グループ レベルでカスタム ロールを割り当てます。

[Managed Applications](../managed-applications/overview.md) のテンプレートで Key Vault を使用する場合は、**アプライアンス リソース プロバイダー** サービス プリンシパルにアクセス許可を付与する必要があります。 詳細については、「[Access Key Vault secret when deploying Azure Managed Applications](../managed-applications/key-vault-access.md)」(Azure Managed Applications のデプロイ時に Key Vault シークレットにアクセスする) を参照してください。

## <a name="reference-secrets-with-static-id"></a>固定 ID でのシークレットの参照

この手法では、テンプレートではなく、パラメーター ファイルでキー コンテナーを参照します。 次の図は、パラメーター ファイルがシークレットを参照し、その値をテンプレートに渡すしくみを示しています。

![Resource Manager キー コンテナー統合の固定 ID の図](./media/key-vault-parameter/statickeyvault.png)

[チュートリアル:Resource Manager テンプレートのデプロイで Azure Key Vault を統合する](./template-tutorial-use-key-vault.md)」では、この手法が利用されます。

次のテンプレートでは、管理者パスワードを含む SQL サーバーがデプロイされます。 パスワード パラメーターは、セキュリティで保護された文字列に設定されます。 しかし、テンプレートには値がどこから来ているかが指定されていません。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

ここで、前のテンプレートのためにパラメーター ファイルを作成します。 このパラメーター ファイルで、テンプレート内のパラメーターの名前に一致するパラメーターを指定します。 パラメーター値のために、キー コンテナーのシークレットを参照します。 シークレットを参照するには、Key Vault のリソース識別子とシークレットの名前を渡します。

次のパラメーター ファイルには、キー コンテナーのシークレットが既に存在している必要があり、そのリソース ID に対して静的な値を指定します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "adminLogin": {
        "value": "exampleadmin"
      },
      "adminPassword": {
        "reference": {
          "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
          },
          "secretName": "ExamplePassword"
        }
      },
      "sqlServerName": {
        "value": "<your-server-name>"
      }
  }
}
```

現在のバージョン以外のバージョンのシークレットを使用する必要がある場合は、`secretVersion` プロパティを使用します。

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

テンプレートをデプロイし、パラメーター ファイルを渡します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>動的 ID でのシークレットの参照

前のセクションでは、Key Vault シークレットの静的リソース ID をパラメーターから渡す方法について説明しました。 しかし参照すべき Key Vault シークレットがデプロイごとに変わる状況も考えられます。 または、パラメーター ファイルに参照パラメーターを作成するのではなく、テンプレートでパラメーター値を渡すこともできます。 いずれの場合でも、リンクされたテンプレートを使用して、キー コンテナー シークレットのリソース ID を動的に生成することができます。

パラメーター ファイルではテンプレート式が使用できないので、パラメーター ファイルでリソース ID を動的に生成することはできません。

親テンプレートに、その入れ子にされたテンプレートを追加し、動的に生成されたリソース ID をパラメーターに格納して渡します。 次の図は、リンクされたテンプレート内のパラメーターがシークレットを参照するしくみを示しています。

![動的 ID](./media/key-vault-parameter/dynamickeyvault.png)

次のテンプレートは、動的に key vault ID を作成し、パラメーターとして渡します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
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
  }
}
```

## <a name="next-steps"></a>次のステップ

- キー コンテナーの一般的な情報については、「[Azure Key Vault とは](../../key-vault/general/overview.md)」をご覧ください。
- キー シークレットの詳細な参照例については、 [Key Vault の例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)を参照してください。
- キー コンテナーからセキュリティで保護された値を渡すことに関する Microsoft Learn モジュールについては、「[高度な ARM テンプレート機能を使用して複雑なクラウド デプロイを管理する](/learn/modules/manage-deployments-advanced-arm-template-features/)」を参照してください。
