---
title: カスタマー マネージド キーを使用した保存時の暗号化
description: Azure コンテナー レジストリの保存時の暗号化、および Azure Key Vault に格納されているカスタマー マネージド キーを使用して Premium レジストリを暗号化する方法について説明します。
ms.topic: article
ms.date: 05/01/2020
ms.custom: ''
ms.openlocfilehash: 67fb58d0e11709b3d801a81f15d856e9b3db922b
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225888"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>カスタマー マネージド キーを使用してレジストリを暗号化する

イメージや他の成果物を Azure コンテナー レジストリに格納すると、保存時のレジストリの内容は Azure によって[サービス マネージド キー](../security/fundamentals/encryption-models.md)を使用して自動的に暗号化されます。 Azure Key Vault 内で作成して管理するキーを使用すると、既定の暗号化を追加の暗号化レイヤーで補完することができます。 この記事では、Azure CLI と Azure portal を使用する手順について説明します。

カスタマー マネージド キーを使用するサーバー側暗号化は、[Azure Key Vault](../key-vault/general/overview.md) との統合によってサポートされます。 独自の暗号化キーを作成してキー コンテナーに格納したり、Azure Key Vault の API を使ってキーを生成したりできます。 Azure Key Vault を使用してキー使用法を監査することもできます。

この機能は、**Premium** コンテナー レジストリ サービス レベルで使用できます。 レジストリ サービスのレベルと制限については、[Azure Container Registry のサービス レベル](container-registry-skus.md)に関する記事を参照してください。


## <a name="things-to-know"></a>注意事項

* 現在カスタマー マネージド キーを有効にできるのは、レジストリを作成するときだけです。
* レジストリでカスタマー マネージド キーを有効にした後、それを無効にすることはできません。
* 現在、カスタマー マネージド キーで暗号化されたレジストリでは、[コンテンツの信頼](container-registry-content-trust.md)はサポートされていません。
* カスタマー マネージド キーで暗号化されたレジストリでは、現在、[ACR タスク](container-registry-tasks-overview.md)に対する実行ログは 24 時間だけ保持されます。 それより長くログを保持する必要がある場合は、[タスク実行ログのエクスポートと保存](container-registry-tasks-logs.md#alternative-log-storage)に関するガイダンスを参照してください。

## <a name="prerequisites"></a>前提条件

この記事の Azure CLI の手順を使用するには、Azure CLI バージョン 2.2.0 以降が必要です。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="enable-customer-managed-key---cli"></a>カスタマー マネージド キーを有効にする - CLI

### <a name="create-a-resource-group"></a>リソース グループを作成する

必要な場合は、[az group create][az-group-create] コマンドを実行して、キー コンテナー、コンテナー レジストリ、その他の必要なリソースを作成するためのリソース グループを作成します。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>ユーザー割り当てマネージド ID を作成する

[az identity create](../active-directory/managed-identities-azure-resources/overview.md) コマンドを使用して、ユーザー割り当てによる [Azure リソース用マネージド ID][az-identity-create] を作成します。 この ID は、Key Vault サービスにアクセスするためにレジストリによって使用されます。

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

コマンドの出力で、`id` と `principalId` の値を記録しておきます。 これらの値は、後の手順でキー コンテナーへのレジストリ アクセスを構成するために必要です。

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

利便性を考えて、これらの値を環境変数に格納します。

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Key Vault を作成します

[az keyvault create][az-keyvault-create] を使用して、レジストリ暗号化用のカスタマー マネージド キーを格納するためのキー コンテナーを作成します。

キーまたはキー コンテナーを誤って削除してデータが失われないようにするには、次の設定を有効にする必要があります: **論理的な削除**と**消去保護**。 次の例には、これらの設定のパラメーターが含まれています。

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを追加する

ID でキー コンテナーにアクセスできるように、キー コンテナーのポリシーを構成します。 次の [az keyvault set-policy][az-keyvault-set-policy] コマンドでは、前に作成して環境変数に格納したマネージ ID のプリンシパル ID を渡します。 キーのアクセス許可を **get**、**unwrapKey**、**wrapKey** に設定します。  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

### <a name="create-key-and-get-key-id"></a>キーを作成してキー ID を取得する

[az keyvault key create][az-keyvault-key-create] コマンドを実行して、キー コンテナーにキーを作成します。

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

コマンドの出力で、キーの ID `kid` を記録しておきます。 この ID は、次のステップで使用します。

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
利便性を考えて、この値を環境変数に格納します。

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>カスタマー マネージド キーを使用してレジストリを作成する

[az acr create][az-acr-create] コマンドを実行して Premium サービス レベルでレジストリを作成し、カスタマー マネージド キーを有効にします。 前に環境変数に格納したマネージ ID のプリンシパル ID とキー ID を渡します。

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>暗号化の状態を表示する

カスタマー マネージド キーによるレジストリ暗号化が有効になっているかどうかを確認するには、[az acr encryption show][az-acr-encryption-show] コマンドを実行します。

```azurecli
az acr encryption show --name <registry-name>
```

出力は次のようになります。

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789..."
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>カスタマー マネージド キーを有効にする - ポータル

### <a name="create-a-managed-identity"></a>マネージド ID の作成

Azure portal で、ユーザー割り当てによる [Azure リソース用マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を作成します。 手順については、「[ユーザー割り当て ID を作成する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)」を参照してください。

後の手順で ID の名前を使用します。

![Azure portal でユーザー割り当てマネージド ID を作成する](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Key Vault を作成します

キー コンテナーを作成する手順については、「[クイックスタート: Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../key-vault/secrets/quick-create-portal.md)」をご覧ください。

カスタマー マネージド キー用のキー コンテナーを作成するときに、 **[基本]** タブで、次の保護設定を有効にします。**論理的な削除**と**消去保護**。 これらの設定は、キーまたはキー コンテナーを誤って削除することでデータが失われないようにするのに役立ちます。

![Azure portal でキー コンテナーを作成する](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>キー コンテナーのアクセス ポリシーを追加する

ID でキー コンテナーにアクセスできるように、キー コンテナーのポリシーを構成します。

1. お使いのキー コンテナーに移動します。
1. **[設定]**  >  **[アクセス ポリシー] > [+ アクセス ポリシーの追加]** を選択します。
1. **[キーのアクセス許可]** を選択し、 **[取得]** 、 **[キーの折り返しを解除]** 、および **[キーを折り返す]** を選択します。
1. **[プリンシパルの選択]** を選択し、ユーザー割り当てマネージド ID のリソース名を選択します。  
1. **[追加]** を選択し、 **[保存]** を選択します。

![キー コンテナーのアクセス ポリシーを作成する](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>キーを作成する

1. お使いのキー コンテナーに移動します。
1. **[設定]**  >  **[キー]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、キーの一意の名前を入力します。
1. それ以外については既定値をそのまま使用し、 **[作成]** を選択します。
1. 作成した後、キーを選択し、現在のキーのバージョンを記録しておきます。

### <a name="create-azure-container-registry"></a>Azure Container Registry の作成

1. **[リソースの作成]**  >  **[コンテナー]**  >  **[コンテナー レジストリ]** の順に選択します。
1. **[基本]** タブで、リソース グループを選択または作成し、レジストリ名を入力します。 **[SKU]** で **[Premium]** を選択します。
1. **[暗号化]** タブの **[顧客が管理するキー]** で、 **[有効]** を選択します。
1. **[ID]** で、作成したマネージド ID を選択します。
1. **[暗号化]** で、 **[Key Vault から選ぶ]** を選択します。
1. **[Azure Key Vault からのキーの選択]** ウィンドウで、前のセクションで作成したキー コンテナー、キー、およびバージョンを選択します。
1. **[暗号化]** タブで、 **[確認と作成]** を選択します。
1. **[作成]** を選択して、レジストリ インスタンスをデプロイします。

![Azure portal でコンテナー レジストリを作成する](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

ポータルでレジストリの暗号化状態を確認するには、お使いのレジストリに移動します。 **[設定]** で、 **[暗号化]** を選択します。

## <a name="enable-customer-managed-key---template"></a>カスタマー マネージド キーを有効にする - テンプレート

Resource Manager テンプレートを使用して、レジストリを作成し、カスタマー マネージド キーで暗号化を有効にすることもできます。

次のテンプレートでは、新しいコンテナー レジストリとユーザー割り当てのマネージド ID が作成されます。 次の内容を新しいファイルにコピーし、`CMKtemplate.json` などのファイル名を使用して保存します。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

前のセクションの手順に従って、次のリソースを作成します。

* キー コンテナー、名前で識別されます
* キー コンテナー キー、キー ID で識別されます

次の [az group deployment create][az-group-deployment-create] コマンドを実行し、上のテンプレート ファイルを使用してレジストリを作成します。 要求されたら、新しいレジストリの名前とマネージド ID の名前、および作成したキー コンテナーの名前とキー ID を指定します。

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>暗号化の状態を表示する

レジストリ暗号化の状態を表示するには、[az acr encryption show][az-acr-encryption-show] コマンドを実行します。

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>レジストリを使用する

レジストリでカスタマー マネージド キーを有効にした後は、カスタマー マネージド キーで暗号化されていないレジストリで実行するのと同じレジストリ操作を実行できます。 たとえば、レジストリを使用して認証を行ったり、Docker イメージをプッシュしたりできます。 「[イメージのプッシュとプル](container-registry-get-started-docker-cli.md)」のコマンドの例をご覧ください。

## <a name="rotate-key"></a>キーをローテーションする

コンプライアンス ポリシーに応じて、レジストリ暗号化に使用されるカスタマー マネージド キーをローテーションします。 新しいキーを作成するか、キーのバージョンを更新してから、そのキーを使用してデータを暗号化するようにレジストリを更新します。 これらの手順は、Azure CLI または Azure portal を使用して行うこともできます。

キーをローテーションする場合、通常はレジストリの作成時に使用したものと同じ ID を指定します。 必要に応じて、キー アクセス用に新しいユーザー割り当て ID を構成するか、レジストリのシステム割り当て ID を有効にして指定します。

> [!NOTE]
> キー アクセス用に構成する ID に対して、必要な[キー コンテナーのアクセス ポリシー](#add-key-vault-access-policy)が設定されていることを確認してください。

### <a name="azure-cli"></a>Azure CLI

[az keyvault key][az-keyvault-key] コマンドを使用して、キー コンテナー キーを作成または管理します。 たとえば、新しいキーのバージョンまたは新しいキーを作成するには、[az keyvault key create][az-keyvault-key-create] コマンドを実行します。

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>

# Create new key
az keyvault key create \
  –-name <new-key-name> \
  --vault-name <key-vault-name>
```

次に、[az acr encryption rotate-key][az-acr-encryption-rotate-key] コマンドを実行して、新しいキー ID と構成する ID を渡します。

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

### <a name="portal"></a>ポータル

レジストリの**暗号化**設定を使用して、カスタマー マネージド キーに使用されるキーのバージョン、キー、キー コンテナー、または ID 設定を更新します。

たとえば、新しいキー バージョンを生成して構成するには、次のようにします。

1. ポータルで、レジストリに移動します。
1. **[設定]** で、 **[暗号化]**  >  **[キーの変更]** を選択します。
1. **[キーの選択]** を選択します。

    ![Azure portal でのキーのローテーション](./media/container-registry-customer-managed-keys/rotate-key.png)
1. **[Azure Key Vault からのキーの選択]** ウィンドウで、以前に構成したキー コンテナーとキーを選択し、 **[バージョン]** で **[新規作成]** を選択します。
1. **[キーの作成]** ウィンドウで、 **[生成]** を選択し、 **[作成]** を選択します。
1. キーの選択を完了し、 **[保存]** を選択します。

## <a name="revoke-key"></a>キーを取り消す

カスタマー マネージド暗号化キーを取り消すには、キー コンテナーのアクセス ポリシーを変更するか、キーを削除します。 たとえば、レジストリによって使用されるマネージド ID のアクセス ポリシーを変更するには、[az keyvault delete-policy][az-keyvault-delete-policy] コマンドを使用します。

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

キーを取り消すと、レジストリは暗号化キーにアクセスできないため、すべてのレジストリ データへのアクセスが実質的にブロックされます。 キーへのアクセスを有効にするか、削除したキーを復元すると、レジストリによってキーが取得されるので、暗号化されたレジストリ データに再びアクセスできるようになります。

## <a name="advanced-scenarios"></a>高度なシナリオ

### <a name="system-assigned-identity"></a>システム割り当て ID

暗号化キー用のキー コンテナーにアクセスするために、レジストリのシステム割り当てマネージド ID を構成できます。 Azure リソース用の各種マネージド ID の基本点な事柄については、[概要](../active-directory/managed-identities-azure-resources/overview.md)を参照してください。

ポータルでレジストリのシステム割り当て ID を有効にするには、次のようにします。

1. ポータルで、レジストリに移動します。
1. **[設定]**  >   **[ID]** を選択します。
1. **[システム割り当て]** で、 **[状態]** を **[オン]** に設定します。 **[保存]** を選択します。
1. ID の **[オブジェクト ID]** をコピーします。

キー コンテナーに ID のアクセス権を付与するには、次のようにします。

1. お使いのキー コンテナーに移動します。
1. **[設定]**  >  **[アクセス ポリシー] > [+ アクセス ポリシーの追加]** を選択します。
1. **[キーのアクセス許可]** を選択し、 **[取得]** 、 **[キーの折り返しを解除]** 、および **[キーを折り返す]** を選択します。
1. **[プリンシパルの選択]** を選択し、システム割り当てマネージド ID のオブジェクト ID、またはレジストリの名前を検索します。  
1. **[追加]** を選択し、 **[保存]** を選択します。

ID を使用するようにレジストリの暗号化設定を更新するには、次のようにします。

1. ポータルで、レジストリに移動します。
1. **[設定]** で、 **[暗号化]**  >  **[キーの変更]** を選択します。
1. **[ID]** で、 **[システム割り当て]** を選択し、 **[保存]** を選択します。

### <a name="key-vault-firewall"></a>Key Vault ファイアウォール

Key Vault ファイアウォールがある仮想ネットワークに Azure Key Vault がデプロイされている場合は、次の手順を実行します。

1. レジストリのシステム割り当て ID を使用するようにレジストリ暗号化を構成します。 前のセクションを参照してください。
2. [信頼されたサービス](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)によるアクセスを許可するようにキー コンテナーを構成します。

詳しい手順については、「[Azure Key Vault のファイアウォールと仮想ネットワークを構成する](../key-vault/general/network-security.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure での保存時の暗号化](../security/fundamentals/encryption-atrest.md)についてさらに学習します。
* アクセス ポリシーと、[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/general/secure-your-key-vault.md)方法についてさらに学習します。


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
