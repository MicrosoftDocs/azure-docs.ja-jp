---
title: Key Vault リファレンス - Azure App Service | Microsoft Docs
description: Azure App Service と Azure Functions での Key Vault 参照の概念と設定に関するガイドです
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 9c7f920c6b66995d53ef742a9faf574286a51d69
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390440"
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

    > [!NOTE]
    > Key Vault 参照では現在、[ネットワーク制限](../key-vault/key-vault-overview-vnet-service-endpoints.md)があるキー コンテナーに格納されているシークレットを解決できません。

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

## <a name="troubleshooting-key-vault-references"></a>Key Vault 参照のトラブルシューティング

参照が正しく解決されない場合は、代わりに参照値が使用されます。 つまり、アプリケーションの設定では、値の構文が `@Microsoft.KeyVault(...)` である環境変数が作成されます。 この場合に、アプリケーションで特定の構造のシークレットが想定されていると、エラーがスローされることがあります。

最も一般的な原因は、[Key Vault アクセス ポリシー](#granting-your-app-access-to-key-vault)が正しく構成されていないことです。 ただし、シークレットが既に存在していないか、参照自体の構文エラーが原因である可能性もあります。

構文が正しい場合は、組み込みの検出機能を使用して現在の解決状態を確認して、エラーの他の原因を確認できます。

### <a name="using-the-detector-for-app-service"></a>App Service の検出機能の使用

1. ポータルで、アプリに移動します。
2. **[Diagnose and solve prolems]\(問題の診断と解決\)** を選択します。
3. **[Availability and Performance]\(可用性とパフォーマンス\)** を選択し、 **[Web app down]\(Web アプリのダウン\)** を選択します。
4. **[Key Vault Application Settings Diagnostics]\(Key Vault のアプリケーション設定の診断\)** を見つけて、 **[詳細情報]** をクリックします。


### <a name="using-the-detector-for-azure-functions"></a>Azure Functions の検出機能の使用

1. ポータルで、アプリに移動します。
2. **[プラットフォーム機能]** に移動します。
3. **[Diagnose and solve prolems]\(問題の診断と解決\)** を選択します。
4. **[Availability and Performance]\(可用性とパフォーマンス\)** を選択し、 **[Function app down or reporting errors]\(関数アプリのダウンまたはエラーの報告\)** を選択します。
5. **[Key Vault Application Settings Diagnostics]\(Key Vault のアプリケーション設定の診断\)** をクリックします。