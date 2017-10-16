---
title: "Resource Manager テンプレートでの Key Vault シークレット | Microsoft Docs"
description: "デプロイメント時にパラメーターとして Key Vault からシークレットを渡す方法について説明します。"
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 1ca72599e67e79d42a3d430dbb13e89ea7265334
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2017
---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>デプロイ時に Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す

デプロイ時に、セキュリティで保護された値 (パスワードなど) をパラメーターとして渡す必要がある場合は、[Azure Key Vault](../key-vault/key-vault-whatis.md) からその値を取得できます。 値を取得するには、キー コンテナーとパラメーター ファイル内のシークレットを参照します。 参照するのは Key Vault ID だけであるため、値が公開されることはありません。 リソースをデプロイするたびに、シークレットの値を手動で入力する必要はありません。 キー コンテナーは、デプロイ先のリソース グループとは異なるサブスクリプションに存在していてもかまいません。 キー コンテナーを参照するときに、サブスクリプション ID を含めます。

Key vault を作成するときの設定、 *enabledForTemplateDeployment*プロパティを*true*です。 この値を true に設定して配置時にリソース マネージャーのテンプレートからアクセスを許可します。  

## <a name="deploy-a-key-vault-and-secret"></a>Key Vault とシークレットのデプロイ

Key vault とシークレットを作成するには、Azure CLI または PowerShell を使用します。 Key vault がテンプレートのデプロイを有効になっていることを確認します。 

Azure CLI では、次を使用します。

```azurecli
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create --name $vaultname --resource-group examplegroup --location 'South Central US' --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

PowerShell では、次を使用します。

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault -VaultName $vaultname -ResourceGroupName examplegroup -Location "South Central US" -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>シークレットへのアクセスの有効化

新しいキー コンテナーと既存のキー コンテナーのどちらを使用する場合も、テンプレートをデプロイするユーザーがシークレットにアクセスできることを確認してください。 シークレットを参照するテンプレートをデプロイするユーザーには、キー コンテナーに対する `Microsoft.KeyVault/vaults/deploy/action` アクセス許可が必要です。 このアクセスは、[所有者](../active-directory/role-based-access-built-in-roles.md#owner)ロールと[共同作成者](../active-directory/role-based-access-built-in-roles.md#contributor)ロールが許可します。 このアクセス許可を付与する[カスタム ロール](../active-directory/role-based-access-control-custom-roles.md)を作成し、そのロールにユーザーを追加することもできます。 ロールにユーザーを追加する方法の詳細については、次を参照してください。 [Azure Active Directory での管理者ロールにユーザーを割り当てる](../active-directory/active-directory-users-assign-role-azure-portal.md)です。


## <a name="reference-a-secret-with-static-id"></a>静的な ID とシークレットを参照します。

Key vault のシークレットを受け取るテンプレートは、その他のテンプレートに似ています。 これはため**テンプレートではありません、パラメーター ファイルのキー コンテナーを参照します。** たとえば、次のテンプレートは、管理者のパスワードを含む SQL データベースを展開します。 パスワード パラメーターは、セキュリティで保護された文字列に設定されます。 しかし、テンプレートがどこからその値のものを指定していません。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "type": "string"
        },
        "administratorLoginPassword": {
            "type": "securestring"
        },
        "collation": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        },
        "edition": {
            "type": "string"
        },
        "requestedServiceObjectiveId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "maxSizeBytes": {
            "type": "string"
        },
        "serverName": {
            "type": "string"
        },
        "sampleName": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "name": "[parameters('serverName')]",
            "properties": {
                "administratorLogin": "[parameters('administratorLogin')]",
                "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                "version": "12.0"
            },
            "resources": [
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "[parameters('databaseName')]",
                    "properties": {
                        "collation": "[parameters('collation')]",
                        "edition": "[parameters('edition')]",
                        "maxSizeBytes": "[parameters('maxSizeBytes')]",
                        "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]",
                        "sampleName": "[parameters('sampleName')]"
                    },
                    "type": "databases"
                },
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "AllowAllWindowsAzureIps",
                    "properties": {
                        "endIpAddress": "0.0.0.0",
                        "startIpAddress": "0.0.0.0"
                    },
                    "type": "firewallrules"
                }
            ],
            "type": "Microsoft.Sql/servers"
        }
    ]
}
```

ここで、前のテンプレートのパラメーター ファイルを作成します。 パラメーター ファイルでは、テンプレート内のパラメーターの名前に一致するパラメーターを指定します。 パラメーター値には、key vault からシークレットを参照します。 シークレットを参照するには、Key Vault のリソース識別子とシークレットの名前を渡します。 次の例では、Key Vault シークレットが既に存在しているという前提で、リソース ID に静的な値を指定します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "value": "exampleadmin"
        },
        "administratorLoginPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/{guid}/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/{vault-name}"
              },
              "secretName": "examplesecret"
            }
        },
        "collation": {
            "value": "SQL_Latin1_General_CP1_CI_AS"
        },
        "databaseName": {
            "value": "exampledb"
        },
        "edition": {
            "value": "Standard"
        },
        "location": {
            "value": "southcentralus"
        },
        "maxSizeBytes": {
            "value": "268435456000"
        },
        "requestedServiceObjectiveId": {
            "value": "455330e1-00cd-488b-b5fa-177c226f28b7"
        },
        "sampleName": {
            "value": ""
        },
        "serverName": {
            "value": "exampleserver"
        }
    }
}
```

## <a name="reference-a-secret-with-dynamic-id"></a>動的な ID とシークレットを参照します。

前のセクションでは、Key Vault シークレットの静的リソース ID を渡す方法を紹介しました。 しかし参照すべき Key Vault シークレットがデプロイごとに変わる状況も考えられます。 その場合、パラメーター ファイルでリソース ID をハードコーディングすることはできません。 パラメーター ファイルではテンプレート式が使用できないので、パラメーター ファイルでリソース ID を動的に生成することはできません。

Key Vault シークレットのリソース ID を動的に生成するには、そのシークレットを必要とするリソースを、入れ子になったテンプレートに移す必要があります。 マスター テンプレートに、その入れ子になったテンプレートを追加し、動的に生成されたリソース ID をパラメーターに格納して渡します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>次のステップ
* Key Vault の全般的な情報については、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」をご覧ください。
* キー シークレットの詳細な参照例については、 [Key Vault の例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)を参照してください。

