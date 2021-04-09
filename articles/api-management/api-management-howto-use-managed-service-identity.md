---
title: Azure API Management でマネージド ID を使用する | Microsoft Docs
description: Azure portal、PowerShell、および Resource Manager テンプレートを使用して、API Management でシステム割り当て ID とユーザー割り当て ID を作成する方法について説明します。
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 98237efae89e7d88dd23cb7e8fc9f7e9f05bca70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521545"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Azure API Management でマネージド ID を使用する

この記事では、Azure API Management インスタンスのマネージド ID を作成する方法と、その他のリソースにアクセスする方法について説明します。 Azure Active Directory (Azure AD) によって生成されたマネージド ID によって、API Management インスタンスは、Azure AD で保護された他のリソース (Azure Key Vault など) に簡単かつ安全にアクセスすることができます。 この ID は Azure によって管理されるため、シークレットをプロビジョニングしたりローテーションしたりする必要はありません。 マネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

API Management インスタンスには、次の 2 種類の ID を与えることができます。

- *システム割り当て ID* はサービスに関連付けられ、サービスが削除されると削除されます。 サービスでは、システム割り当て ID を 1 つだけ設定できます。
- *ユーザー割り当て ID* は、サービスに割り当てることができるスタンドアロン Azure リソースです。 サービスでは、複数のユーザー割り当て ID を設定できます。

## <a name="create-a-system-assigned-managed-identity"></a>システム割り当てマネージド ID を作成する

### <a name="azure-portal"></a>Azure portal

Azure portal でマネージド ID を設定するには、まず API Management インスタンスを作成し、その後、この機能を有効にします。

1. ポータルを使って通常の方法で API 管理インスタンスを作成します。 ポータルでそれに移動します。
2. **[マネージド ID]** を選択します。
3. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に切り替えます。 **[保存]** を選択します。

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="システム割り当てマネージド ID を有効化するための選択項目" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次の手順では、Azure PowerShell を使用して、API Management インスタンスを作成し、それに対して ID を割り当てる方法について説明します。 

1. 必要に応じて、[Azure PowerShell ガイド](/powershell/azure/install-az-ps)の手順を使用して、Azure PowerShell をインストールします。 その後、`Connect-AzAccount` を実行して、Azure との接続を作成します。

2. 次のコードを使用して、インスタンスを作成します。 API Management インスタンスで Azure PowerShell を使用する方法の他の例については、「[API Management 用の Azure PowerShell サンプル](powershell-samples.md)」を参照してください。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. 既存のインスタンスを更新して、ID を作成する:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

ID を持った API Management インスタンスは、リソース定義に次のプロパティを含めることによって作成できます。

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

このプロパティは、API Management インスタンスの ID を作成して管理するよう Azure に命令するものです。

たとえば、Azure Resource Manager テンプレート全体は次のようになります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

インスタンスが作成されると、次の追加のプロパティが設定されます。

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`tenantId` プロパティは、ID が属している Azure AD テナントを識別します。 `principalId` プロパティは、インスタンスの新しい ID の一意識別子です。 Azure AD 内では、サービス プリンシパルの名前は、お使いの API Management インスタンスに指定したものと同じになります。

> [!NOTE]
> API Management インスタンスには、システム割り当て ID とユーザー割り当て ID の両方を同時に設定することができます。 この場合、`type` プロパティは `SystemAssigned,UserAssigned` になります。

## <a name="supported-scenarios-using-system-assigned-identity"></a>システム割り当て ID を使用したサポートされるシナリオ

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>API Management インスタンスのカスタム TLS/SSL 証明書を Azure Key Vault から取得する
API Management インスタンスのシステム割り当て ID を使用して、Azure Key Vault に格納されているカスタム TLS/SSL 証明書を取得できます。 その後、これらの証明書を API Management インスタンスのカスタム ドメインに割り当てることができます。 以下の考慮事項に留意してください。

- シークレットのコンテンツ タイプは *application/x-pkcs12* である必要があります。
- シークレットが含まれている Key Vault 証明書のシークレット エンドポイントを使用します。

> [!Important]
> 証明書のオブジェクト バージョンの指定がない場合は、より新しいバージョンの証明書が Key Vault にアップロードされた後、4 時間以内にそれが API Management によって自動的に取得されます。

次の例では、次の手順を含む Azure Resource Manager テンプレートを示します。

1. マネージド ID を使用して API Management インスタンスを作成します。
2. Azure Key Vault インスタンスのアクセス ポリシーを更新し、そこからシークレットを取得することを API Management インスタンスに許可します。
3. Key Vault インスタンスからの証明書を使用してカスタム ドメイン名を設定して API Management インスタンスを更新します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>API Management ID を使用してバックエンドに対する認証を行う

システム割り当て ID を使用して、[authentication-managed-identity](api-management-authentication-policies.md#ManagedIdentity) ポリシーを通じて、バックエンドに対する認証を行うことができます。

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>システム割り当てマネージド ID を使用して、IP ファイアウォールの背後にある Azure リソースに接続する


API Management は、次のリソースに対する信頼された Microsoft サービスです。 これを使用すると、サービスは、ファイアウォールの背後にある次のリソースに接続できるようになります。 適切な Azure ロールをそのリソース インスタンスの[システム割り当てマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に明示的に割り当てると、そのインスタンスのアクセス スコープは、マネージド ID に割り当てられた Azure ロールに対応します。


|Azure サービス | Link|
|---|---|
|Azure Storage | [Trusted-access-to-azure-storage](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Azure Service Bus | [Trusted-access-to-azure-service-bus](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Azure Event Hub | [Trused-access-to-azure-event-hub](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

> [!NOTE]
> API Management インスタンスは、最大 10 個のユーザー割り当てマネージド ID に関連付けることができます。

### <a name="azure-portal"></a>Azure portal

このポータルでマネージド ID を設定するには、まず API Management インスタンスを作成し、その後、この機能を有効にします。

1. ポータルを使って通常の方法で API 管理インスタンスを作成します。 ポータルでそれに移動します。
2. **[マネージド ID]** を選択します。
3. **[ユーザー割り当て済み]** タブで、 **[追加]** を選択します。
4. 先ほど作成した ID を検索して選択します。 **[追加]** を選択します。

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="ユーザー割り当てマネージド ID を有効化するための選択項目" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次の手順では、Azure PowerShell を使用して、API Management インスタンスを作成し、それに対して ID を割り当てる方法について説明します。 

1. 必要に応じて、[Azure PowerShell ガイド](/powershell/azure/install-az-ps)の手順を使用して、Azure PowerShell をインストールします。 その後、`Connect-AzAccount` を実行して、Azure との接続を作成します。

2. 次のコードを使用して、インスタンスを作成します。 API Management インスタンスで Azure PowerShell を使用する方法の他の例については、「[API Management 用の Azure PowerShell サンプル](powershell-samples.md)」を参照してください。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. 既存のサービスを更新してサービスに ID を割り当てる:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

ID を持った API Management インスタンスは、リソース定義に次のプロパティを含めることによって作成できます。

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

ユーザー割り当ての種類を追加すると、インスタンスに対して指定されたユーザー割り当て ID を使用するように Azure に指示されます。

たとえば、Azure Resource Manager テンプレート全体は次のようになります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
         "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

サービスが作成されると、次の追加のプロパティが設定されます。

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`principalId` プロパティは、Azure AD の管理に使用される ID の一意識別子です。 `clientId` プロパティは、ランタイム呼び出し中に使用される ID を指定するために使用される、アプリケーションの新しい ID の一意識別子です。

> [!NOTE]
> API Management インスタンスには、システム割り当て ID とユーザー割り当て ID の両方を同時に設定することができます。 この場合、`type` プロパティは `SystemAssigned,UserAssigned` になります。

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を使用したサポートされるシナリオ

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>API Management インスタンスのカスタム TLS/SSL 証明書を Azure Key Vault から取得する
ユーザーが割り当てた任意の ID を使用して、API Management インスタンスと KeyVault 間で信頼を確立できます。 その後、この信頼を使用して、Azure Key Vault に格納されているカスタム TLS/SSL 証明書を取得できます。 その後、これらの証明書を API Management インスタンスのカスタム ドメインに割り当てることができます。 

以下の考慮事項に留意してください。

- シークレットのコンテンツ タイプは *application/x-pkcs12* である必要があります。
- シークレットが含まれている Key Vault 証明書のシークレット エンドポイントを使用します。

> [!Important]
> 証明書のオブジェクト バージョンの指定がない場合は、より新しいバージョンの証明書が Key Vault にアップロードされた後、4 時間以内にそれが API Management によって自動的に取得されます。

完全なテンプレートについては、[ユーザー割り当て ID を使用した KeyVault ベースの SSL を使用する API Management](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json) に関するページをご覧ください。

このテンプレートでは、以下をデプロイします。

* Azure API Management
* Azure ユーザー割り当てマネージド ID
* SSL/TLS 証明書を格納するための Azure KeyVault

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>ユーザー割り当て ID を使用してバックエンドに対する認証を行う

ユーザー割り当て ID を使用して、[authentication-managed-identity](api-management-authentication-policies.md#ManagedIdentity) ポリシーを通じて、バックエンドに対する認証を行うことができます。

## <a name="remove-an-identity"></a><a name="remove"></a>ID を削除する

ポータルまたは Azure Resource Manager テンプレートを使用して、作成したのと同じ方法で機能を無効にすることで、システム割り当て ID を削除できます。 ユーザー割り当て ID は個別に削除することはできません。 すべての ID を削除するには、ID の種類を `"None"` に設定します。

この方法でシステム割り当て ID を削除すると、それは Azure AD からも削除されます。 API Management インスタンスが削除されると、システム割り当て ID も Azure AD から自動的に削除されます。

Azure Resource Manager テンプレートを使用してすべての ID を削除するには、次のセクションを更新します。

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> API Management インスタンスが Key Vault からのカスタム SSL 証明書を使用して構成されているときに、マネージド ID を無効にしようとすると、その要求は失敗します。
>
> Azure Key Vault 証明書からインラインでエンコードされた証明書に切り替えて自分のブロックを解除した後、マネージド ID を無効にします。 詳細については、「[カスタム ドメイン名を構成する](configure-custom-domain.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

詳細については、Azure リソースのマネージド ID について学びます。

* [Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure リソース マネージャーのテンプレート](https://github.com/Azure/azure-quickstart-templates)
* [ポリシーでのマネージド ID による認証](./api-management-authentication-policies.md#ManagedIdentity)
