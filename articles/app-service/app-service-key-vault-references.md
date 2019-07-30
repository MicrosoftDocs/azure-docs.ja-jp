---
title: Key Vault リファレンス - Azure App Service | Microsoft Docs
description: Azure App Service と Azure Functions での Key Vault 参照の概念と設定に関するガイドです
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e7a049c8def0a5014aeb8a0e7a16aaa8def28009
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705696"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>App Service と Azure Functions の Key Vault 参照を使用する (プレビュー)

> [!NOTE] 
> Key Vault 参照は現在プレビューの段階です。

このトピックでは、コードを変更せず、App Service または Azure Functions アプリケーションの Azure Key Vault のシークレットを使用する方法を紹介します。 [Azure Key Vault](../key-vault/key-vault-overview.md) は、アクセス ポリシーと監査履歴を完全制御する、一元化されたシークレット管理を提供するサービスです。

## <a name="granting-your-app-access-to-key-vault"></a>Key Vault へのアクセス許可をアプリに付与する

Key Vault からシークレットを読み取るには、Key Vault を作成し、それにアクセスする許可をアプリに与える必要があります。

1. [Key Vault クイック スタート](../key-vault/quick-create-cli.md)に従い、Key Vault を作成してください。

1. [システム割り当てのマネージド ID](overview-managed-identity.md) を自分のアプリケーションのために作成します。

   > [!NOTE] 
   > Key Vault 参照では現在のところ、システム割り当てのマネージド ID のみをサポートしています。 ユーザー割り当て ID は使用できません。

1. 先に作成したアプリケーション ID に対して、[Key Vault でアクセス ポリシー](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies)を作成します。 このポリシーで "Get" シークレット アクセス許可を有効にします。 "承認されているアプリケーション" または `applicationId` 設定を構成しないでください。これは、マネージド ID との互換性がないためです。

    キー コンテナーのアプリケーション ID へのアクセス許可は 1 回限りの操作であり、すべての Azure サブスクリプションで同じです。 これを使用して、必要な数だけ証明書をデプロイできます。 

## <a name="reference-syntax"></a>参照構文

Key Vault 参照の形式は `@Microsoft.KeyVault({referenceString})` です。`{referenceString}` は次のいずれかのオプションで置換されます。

> [!div class="mx-tdBreakAll"]
> | 参照文字列                                                            | 説明                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** は、バージョン (例: https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931 ) を含む、Key Vault におけるシークレットのフル データプレーン URI になります。  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** は Key Vault リソースの名前になります。 **SecretName** はターゲット シークレットの名前になります。 **SecretVersion** は使用するシークレットのバージョンになります。 |

> [!NOTE] 
> 現在のプレビューでは、バージョンが必須です。 シークレットをローテーションするとき、アプリケーション構成でバージョンを更新する必要があります。

たとえば、完全な参照は次のようになります。

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

あるいは:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Key Vault からのソース アプリケーション設定

Key Vault 参照は [[アプリケーション設定]](configure-common.md#configure-app-settings) の値として使用できます。サイト構成ではなく、Key Vault でシークレットを保存できます。格納中のアプリケーション設定は暗号化されて保護されますが、シークレットの管理機能が必要な場合、Key Vault に進む必要があります。

アプリケーション設定に Key Vault 参照を使用するには、設定の値として参照を設定します。 アプリは通常どおり、そのキーを利用してシークレットを参照できます。 コードに変更を加える必要はありません。

> [!TIP]
> Key Vault 参照を使用するほとんどのアプリケーション設定は、環境ごとに別個の Key Vault を用意する必要があるため、スロット設定としてマークする必要があります。

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager デプロイ

Azure Resource Manager テンプレート経由でリソース デプロイを自動化するとき、この機能を動作させるには、場合によっては特定の順序で依存関係を並べる必要があります。 注意すべきことですが、サイト定義の `siteConfig` プロパティを使用するのではなく、アプリケーション設定を独自のリソースとして定義する必要があります。 これはサイトを最初に定義する必要があるからです。そうすることで、サイトと共にシステム割り当て ID が作成され、アクセス ポリシーで使用できます。

Function App の擬似テンプレートの例は次のようになります。

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> この例では、ソース管理デプロイはアプリケーション設定に依存します。 アプリ設定は非同期で更新されるため、通常、これは安全ではありません。 しかしながら、`WEBSITE_ENABLE_SYNC_UPDATE_SITE` アプリケーション設定を含めているため、同期で更新されます。 つまり、ソース管理デプロイは、アプリケーションが完全に更新されたときにのみ開始されます。
