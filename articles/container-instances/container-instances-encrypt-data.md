---
title: デプロイ データの暗号化
description: コンテナー インスタンス リソースに永続化されたデータの暗号化と、カスタマー マネージド キーでデータを暗号化する方法について説明します
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 1b73ce5c994231a1c7b2f26ad702f2ad5880ba44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94686278"
---
# <a name="encrypt-deployment-data"></a>デプロイ データの暗号化

クラウドで Azure Container Instances (ACI) リソースを実行すると、ACI サービスにより、コンテナーに関連するデータが収集されて永続化されます。 ACI では、このデータはクラウドに永続化されるときに自動的に暗号化されます。 この暗号化によってデータは保護され、組織のセキュリティとコンプライアンスのコミットメントを満たすのに役立ちます。 ACI では、ユーザー独自のキーでこのデータを暗号化するオプションも用意されており、ACI のデプロイに関するデータをさらにきめ細かく制御できます。

## <a name="about-aci-data-encryption"></a>ACI のデータ暗号化について 

ACI のデータは、256 ビット AES 暗号化を使用して暗号化および暗号化解除されます。 ACI のすべてのデプロイで有効になっているため、この暗号化を利用するために、デプロイまたはコンテナーを変更する必要はありません。 これには、デプロイに関するメタデータ、環境変数、コンテナーに渡されるキー、コンテナーが停止した後でも見られるように保持されるログが含まれます。 暗号化によってコンテナー グループのパフォーマンスが影響を受けることはなく、暗号化に追加コストはかかりません。

## <a name="encryption-key-management"></a>暗号化キーの管理

Microsoft のマネージド キーを利用してコンテナー データを暗号化することも、独自のキーで暗号化を管理することもできます。 次の表では、これらのオプションを比較します。 

|    |    Microsoft のマネージド キー     |     カスタマー マネージド キー     |
|----|----|----|
|    **暗号化/暗号化解除の操作**    |    Azure    |    Azure    |
|    **キー記憶域**    |    Microsoft キー ストア    |    Azure Key Vault    |
|    **キーのローテーションの責任**    |    Microsoft    |    Customer    |
|    **キーへのアクセス**    |    Microsoft のみ    |    Microsoft、顧客    |

このドキュメントの残りの部分では、キー (カスタマー マネージド キー) を使用して ACI のデプロイ データを暗号化するために必要な手順について説明します。 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>カスタマー マネージド キーを使用してデータを暗号化する

### <a name="create-service-principal-for-aci"></a>ACI 用のサービス プリンシパルを作成する

最初のステップでは、[Azure テナント](../active-directory/develop/quickstart-create-new-tenant.md)に、Azure Container Instances サービスへのアクセス許可を付与するためのサービス プリンシパルが割り当てられていることを確認します。 

> [!IMPORTANT]
> 次のコマンドを実行してサービス プリンシパルを正常に作成するには、お使いのテナントでサービス プリンシパルを作成するためのアクセス許可があることを確認します。
>

次の CLI コマンドを実行すると、Azure 環境に ACI の SP がセットアップされます。

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

このコマンドを実行すると、"displayName": "Azure Container Instance Service" で設定されたサービス プリンシパルが表示されます。

サービス プリンシパルを正常に作成できない場合は、次のようにします。
* テナントでこれを行うためのアクセス許可があることを確認します
* ACI にデプロイするためのサービス プリンシパルがテナントに既に存在するかどうかを確認します。 そのためには、`az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` を実行し、代わりにそのサービス プリンシパルを使用します

### <a name="create-a-key-vault-resource"></a>Key Vault リソースを作成する

[Azure portal](../key-vault/general/quick-create-portal.md)、[Azure CLI](../key-vault/general/quick-create-cli.md)、または [Azure PowerShell](../key-vault/general/quick-create-powershell.md) を使用して、Azure Key Vault を作成します。

キー コンテナーのプロパティについては、次のガイドラインを使用します。 
* 名前:一意の名前が必要です。 
* サブスクリプション:サブスクリプションを選択します。
* [リソース グループ] で、既存のリソース グループを選択するか、新しく作成してリソース グループ名を入力します。
* [場所] プルダウン メニューで、場所を選択します。
* 他のオプションは、既定値のままにしておいても、追加の要件に基づいて選択してもかまいません。

> [!IMPORTANT]
> カスタマー マネージド キーを使用して ACI のデプロイ テンプレートを暗号化する場合は、キー コンテナーで [論理的な削除] と [Do Not Purge]\(消去しない\) の 2 つのプロパティを設定することをお勧めします。 これらのプロパティは既定では有効になっていませんが、新規または既存のキー コンテナーに対して PowerShell または Azure CLI を使用して有効にすることができます。

### <a name="generate-a-new-key"></a>新しいキーを生成する 

キー コンテナーが作成されたら、Azure portal でリソースに移動します。 リソース ブレードの左側のナビゲーション メニューで、[設定] の **[キー]** をクリックします。 [キー] のビューで、[生成/インポート] をクリックして新しいキーを生成します。 このキーに対する一意の名前を指定し、他の設定は必要に応じて使用します。 

![新しいキーを生成する](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>アクセス ポリシーを設定する

ACI サービスによるキーへのアクセスを許可するための、新しいアクセス ポリシーを作成します。

* キーが生成されたら、キー コンテナー リソース ブレードに戻り、[設定] の下にある **[アクセス ポリシー]** をクリックします。
* キー コンテナーの [アクセス ポリシー] ページで、 **[アクセス ポリシーの追加]** をクリックします。
* *[キーのアクセス許可]* を、 **[取得]** と **[キーの折り返しを解除]** を含むように設定します ![キーのアクセス許可を設定する](./media/container-instances-encrypt-data/set-key-permissions.png)
* *[プリンシパルの選択]* で、**Azure Container Instance Service** を選択します
* 下部にある **[追加]** をクリックします 

アクセス ポリシーがお使いのキー コンテナーのアクセス ポリシーに表示されるようになります。

![新しいアクセス ポリシー](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>JSON デプロイ テンプレートを変更する

> [!IMPORTANT]
> カスタマー マネージド キーによるデプロイ データの暗号化は、現在ロールアウト中の最新の API バージョン (2019-12-01) で使用できます。デプロイ テンプレートでこの API バージョンを指定してください。 これに関して何か問題がある場合は、Azure サポートにご連絡ください。

キー コンテナーのキーとアクセス ポリシーを設定した後は、次のプロパティを ACI のデプロイ テンプレートに追加します。 テンプレートを使用した ACI リソースのデプロイについて詳しくは、「[チュートリアル: Resource Manager テンプレートを使用してマルチコンテナー グループをデプロイする](./container-instances-multi-container-group.md)」のテンプレートを編集した完全なテンプレートです。 
* `resources` の下で、`apiVersion` を `2019-12-01` に設定します。
* デプロイ テンプレートのコンテナー グループ プロパティ セクションに、次の値を含む `encryptionProperties` を追加します。
  * `vaultBaseUrl`: お使いのキー コンテナーの DNS 名。ポータルのキー コンテナー リソースの概要ブレードで確認できます。
  * `keyName`: 前に生成したキーの名前。
  * `keyVersion`: スキーマの現在のバージョン。 これは、キー自体をクリックして確認できます (キー コンテナー リソースの [設定] セクションの [キー] の下)
* コンテナー グループのプロパティの下に、`Standard` プロパティと値 `sku` を追加します。 API version 2019-12-01 では `sku` プロパティは必須です。

次のテンプレート スニペットは、デプロイ データを暗号化するための追加のプロパティを示しています。

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

次に示すのは、「[チュートリアル: Resource Manager テンプレートを使用してマルチコンテナー グループをデプロイする](./container-instances-multi-container-group.md)」のテンプレートを編集した完全なテンプレートです。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>リソースをデプロイする

デスクトップ上でテンプレート ファイルを作成および編集した場合は、そのファイルをドラッグすることによって Cloud Shell ディレクトリにアップロードできます。 

[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az deployment group create][az-deployment-group-create] コマンドを使用してテンプレートをデプロイします。

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

数秒以内に、Azure から最初の応答を受信します。 デプロイが完了すると、ACI サービスによって保持されているそのデプロイに関連したデータはすべて、指定したキーで暗号化されます。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group/#az-deployment-group-create
