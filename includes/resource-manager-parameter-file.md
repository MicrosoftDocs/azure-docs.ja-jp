デプロイ中にパラメーター ファイルを使用してパラメーター値を渡す場合は、次の例に示すような形式の JSON ファイルを作成する必要があります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

パラメーター ファイルのサイズは、64 KB 以下である必要があります。

パラメーターに機密性の高い値(パスワードなど) を提供する必要がある場合は、その値を Key Vault に追加します。 前の例で示したように、デプロイ中に Key Vault を取得します。 詳細については、「 [デプロイメント時にセキュリティで保護された値を渡す](../articles/azure-resource-manager/resource-manager-keyvault-parameter.md)」を参照してください。 



<!--HONumber=Nov16_HO3-->


