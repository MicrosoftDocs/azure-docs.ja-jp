---
title: データの暗号化 - Azure CLI - Azure Database for MySQL
description: Azure CLI を使用して Azure Database for MySQL のデータ暗号化を設定し、管理する方法について説明します。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6d9abc67035b4581a028d8e59ef080b4f1ffa5b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96519044"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-cli"></a>Azure CLI を使用した Azure Database for MySQL のデータ暗号化

Azure CLI を使用して Azure Database for MySQL のデータ暗号化を設定し、管理する方法について説明します。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI の前提条件

* Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
* カスタマー マネージド キーで使用するキー コンテナーとキーを作成します。 また、キー コンテナーの消去防止と論理的な削除も有効にします。

  ```azurecli-interactive
  az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
  ```

* 作成された Azure Key Vault で、Azure Database for MySQL のデータ暗号化に使用するキーを作成します。

  ```azurecli-interactive
  az keyvault key create --name <key_name> -p software --vault-name <vault_name>
  ```

* 既存のキー コンテナーを使用するには、カスタママー マネージド キーとして使用すための次のプロパティが必要です。

  * [論理的な削除](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [消去保護](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```
  * データ保有日数を 90 日に設定
  ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --retention-days 90
    ```

* カスタマー マネージド キーとして使用するには、キーに次の属性が必要です。
  * 有効期限がない
  * 無効化されていない
  * **get**、**wrap**、**unwrap** の操作を実行する
  * **Recoverable** に設定された recoverylevel 属性 (これには保有期間を 90 日に設定して、論理的な削除を有効にする必要があります)
  * 消去保護の有効化

次のコマンドを使用して、キーの上記の属性を確認できます。

```azurecli-interactive
az keyvault key show --vault-name <key_vault_name> -n <key_name>
```

## <a name="set-the-right-permissions-for-key-operations"></a>キー操作に対する適切なアクセス許可を設定する

1. Azure Database for MySQL でマネージド ID を取得する方法は 2 つあります。

   ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>マネージド ID を持つ Azure Database for MySQL サーバーを新規作成する。

   ```azurecli-interactive
   az mysql server create --name -g <resource_group> --location <locations> --storage-size size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> -geo-redundant-backup <Enabled/Disabled>  --assign-identity
   ```

   ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>既存の Azure Database for MySQL サーバーを更新して、マネージド ID を取得する。

   ```azurecli-interactive
   az mysql server update --name  <server name>  -g <resource_group> --assign-identity
   ```

2. MySQL サーバーの名前である **[プリンシパル]** の **[キーのアクセス許可]** を設定します ( **[取得]** 、 **[ラップ]** 、 **[ラップ解除]** )。

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL のデータ暗号化を設定する

1. Azure Key Vault で作成されたキーを使用して、Azure Database for MySQL のデータ暗号化を有効にします。

    ```azurecli-interactive
    az mysql server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    キーの URL: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>復元サーバーまたはレプリカ サーバーでのデータ暗号化の使用

Key Vault に格納されている顧客のマネージド キーで Azure Database for MySQL が暗号化された後、新しく作成されたサーバーのコピーも暗号化されます。 この新しいコピーは、ローカルまたは geo 復元操作を使用するか、レプリカ (ローカル/リージョン間) 操作を使用して作成できます。 そのため、暗号化された MySQL サーバーの場合は、次の手順を使用して、暗号化済みの復元されたサーバーを作成できます。

### <a name="creating-a-restoredreplica-server"></a>復元/レプリカ サーバーの作成

* [復元サーバーの作成](howto-restore-server-cli.md) 
* [読み取りレプリカ サーバーの作成](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>サーバーが復元された後、復元されたサーバーのデータ暗号化を再検証する

*   レプリカ サーバーに ID を割り当てる
```azurecli-interactive
az mysql server update --name  <server name>  -g <resoure_group> --assign-identity
```

*   復元されたサーバーまたはレプリカ サーバーに使用されたはずの既存のキーを取得する

```azurecli-interactive
az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
```

*   復元されたサーバーまたはレプリカ サーバーの新しい ID に対するポリシーを設定する
  
```azurecli-interactive
az keyvault set-policy --name <keyvault> -g <resoure_group> --key-permissions get unwrapKey wrapKey --object-id <principl id of the server returned by the step 1>
```

* 暗号化キーを使用して復元されたサーバーまたはレプリカ サーバーを再検証する

```azurecli-interactive
az mysql server key create –name  <server name> -g <resource_group> --kid <key url>
```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-mysql"></a>Azure Database for MySQL で使用されているキーの追加機能

### <a name="get-the-key-used"></a>使用されているキーを取得する

```azurecli-interactive
az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
```

キーの URL: `https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>`

### <a name="list-the-key-used"></a>使用されているキーを一覧表示する

```azurecli-interactive
az mysql server key list --name  <server name>  -g <resource_group>
```

### <a name="drop-the-key-being-used"></a>使用されているキーを削除する

```azurecli-interactive
az mysql server key delete -g <resource_group> --kid <key url>
```

## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Azure Resource Manager テンプレートを使用したデータ暗号化の有効化

Azure portal とは別に、新規および既存のサーバー用の Azure Resource Manager テンプレートを使用して、Azure Database for MySQL サーバー上でデータ暗号化を有効にすることもできます。

### <a name="for-a-new-server"></a>新しいサーバーの場合

事前に作成したいずれかの Azure Resource Manager テンプレートを使用して、データ暗号化を有効にしてサーバーをプロビジョニングします。[データの暗号化を使用した例](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

この Azure Resource Manager テンプレートでは、Azure Database for MySQL サーバーを作成し、パラメーターとして渡された **KeyVault** および **Key** を使用して、サーバーのデータ暗号化を有効にします。

### <a name="for-an-existing-server"></a>既存のサーバーの場合

また、Azure Resource Manager テンプレートを使用して、既存の Azure Database for MySQL サーバー上でデータ暗号化を有効にすることもできます。

* プロパティ オブジェクトの `Uri` プロパティで以前にコピーした Azure Key Vault キーのリソース ID を渡します。

* *2020-01-01-preview* を API バージョンとして使用します。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>次のステップ

 データ暗号化の詳細については、「[カスタマー マネージド キーを使用した Azure Database for MySQL のデータの暗号化](concepts-data-encryption-mysql.md)」を参照してください。
