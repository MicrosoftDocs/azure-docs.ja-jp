---
title: 高可用性のためのゾーン冗長レジストリ
description: Azure Container Registry でのゾーン冗長の有効化について説明します。 Azure 可用性ゾーンでコンテナー レジストリまたはレプリケーションを作成します。 ゾーン冗長は、Premium サービス レベルの機能です。
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: a190ea68f41196fb11c20259b9953f516d6f5370
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203863"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>回復性と高可用性のために Azure Container Registry でゾーン冗長を有効にする

リージョン操作の可用性向上と待機時間短縮のために 1 つ以上の Azure リージョンにレジストリ データをレプリケートする [geo レプリケーション](container-registry-geo-replication.md)に加えて、Azure Container Registry ではオプションの *ゾーン冗長* がサポートされています。 [ゾーン冗長](../availability-zones/az-overview.md#availability-zones)を使用すると、特定のリージョンのレジストリまたはレプリケーション リソース (レプリカ) に回復性と高可用性が提供されます。

この記事では、Azure CLI、Azure portal、または Azure Resource Manager テンプレートを使用して、ゾーン冗長コンテナー レジストリまたはレプリカを設定する方法について説明します。 

ゾーン冗長は、Premium Container Registry サービス レベルの **プレビュー** 機能です。 レジストリ サービスのレベルと制限については、「[Azure Container Registry のサービス レベル](container-registry-skus.md)」を参照してください。

## <a name="preview-limitations"></a>プレビューの制限事項

* 現在は、米国東部、米国東部 2、米国西部 2、北ヨーロッパ、西ヨーロッパ、東日本の各リージョンでサポートされています。
* 可用性ゾーンへのリージョンの変換は、現在はサポートされていません。 リージョンで可用性ゾーンのサポートを有効にするには、可用性ゾーンのサポートを有効にして、必要なリージョンにレジストリを作成するか、可用性ゾーンのサポートを有効にしてレプリケートされたリージョンを追加する必要があります。
* リージョンでゾーン冗長を無効にすることはできません。
* [ACR タスク](container-registry-tasks-overview.md)では、可用性ゾーンはまだサポートされていません。

## <a name="about-zone-redundancy"></a>ゾーン冗長について

回復性と可用性に優れた Azure コンテナー レジストリを Azure リージョンに作成するには、[可用性ゾーン](../availability-zones/az-overview.md)を使用します。 たとえば、組織では、他の[サポートされている Azure リソース](../availability-zones/az-region.md)を使用してゾーン冗長 Azure コンテナー レジストリを設定して、データ所在地や他のコンプライアンス要件を満たしながら、リージョン内で高可用性を提供することができます。

Azure Container Registry でやはりサポートされている [geo レプリケーション](container-registry-geo-replication.md)を使用すると、複数のリージョンにサービスがレプリケートされ、他の場所にあるリソースでの冗長性と局所性が実現されます。 リージョン内の冗長性のための可用性ゾーンと、複数のリージョンをまたぐ geo レプリケーションを組み合わせることで、レジストリの信頼性とパフォーマンスが強化されます。

可用性ゾーンは、Azure リージョン内の一意の物理的な場所です。 回復性を確保するため、有効になっているリージョンにはいずれも最低 3 つのゾーンが別個に存在しています。 ゾーンごとに、独立した電源、冷房、ネットワークを備えた 1 つまたは複数のデータセンターがあります。 ゾーン冗長対応に構成されたレジストリ (または別のリージョンのレジストリ レプリカ) は、リージョン内のすべての可用性ゾーンにレプリケートされ、データセンターで障害が発生した場合でも使用可能に保たれます。

## <a name="create-a-zone-redundant-registry---cli"></a>ゾーン冗長レジストリを作成する - CLI

Azure CLI を使用してゾーン冗長を有効にするには、Azure CLI バージョン 2.17.0 以降または Azure Cloud Shell が必要です。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

### <a name="create-a-resource-group"></a>リソース グループを作成する

必要に応じて、[az group create](/cli/azure/group#az_group_create) コマンドを実行して、レジストリ用のリソース グループを作成します。

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>ゾーン対応レジストリを作成する

[az acr create](/cli/azure/acr#az_acr_create) コマンドを実行して、Premium サービス レベルにゾーン冗長レジストリを作成します。 Azure Container Registry の[可用性ゾーンをサポートする](../availability-zones/az-region.md)リージョンを選択します。 次の例では、*eastus* リージョンでゾーン冗長が有効になっています。 レジストリ オプションの詳細については、`az acr create` コマンドのヘルプを参照してください。

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

コマンドの出力で、レジストリの `zoneRedundancy` プロパティを確認します。 有効の場合、レジストリはゾーン冗長です。

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>ゾーン冗長レプリケーションを作成する

[az acr replication create](/cli/azure/acr/replication#az_acr_replication_create) コマンドを実行して、*westus2* など、Azure Container Registry の [可用性ゾーンをサポートする](../availability-zones/az-region.md)リージョンにゾーン冗長レジストリ レプリカを作成します。 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
コマンドの出力で、レプリカの `zoneRedundancy` プロパティを確認します。 有効の場合、レプリカはゾーン冗長です。

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="create-a-zone-redundant-registry---portal"></a>ゾーン冗長レジストリを作成する - ポータル

1. Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。
1. **[リソースの作成]**  >  **[コンテナー]**  >  **[コンテナー レジストリ]** の順に選択します。
1. **[基本]** タブで、リソース グループを選択または作成し、一意のレジストリ名を入力します。 
1. **[場所]** で、Azure Container Registry のゾーン冗長がサポートされているリージョンを選択します ("*米国東部*" など)。
1. **[SKU]** で **[Premium]** を選択します。
1. **[可用性ゾーン]** で、 **[有効]** を選択します。
1. 必要に応じて、追加のレジストリ設定を構成してから、 **[確認および作成]** を選択します。
1. **[作成]** を選択して、レジストリ インスタンスをデプロイします。

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Azure portal でゾーン冗長を有効にする":::

ゾーン冗長レプリケーションを作成するには:

1. Premium レベルのコンテナー レジストリに移動し、 **[レプリケーション]** を選択します。
1. 表示されるマップで、Azure Container Registry のゾーン冗長がサポートされているリージョンの緑の六角形を選択します (**米国西部 2** など)。 または **[+ 追加]** を選択します。
1. **[レプリケーションの作成]** ウィンドウで、 **[場所]** を確認します。 **[可用性ゾーン]** で **[有効]** を選択し、次に **[作成]** を選択します。

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Azure portal でゾーン冗長レプリケーションを有効にする":::

## <a name="create-a-zone-redundant-registry---template"></a>ゾーン冗長レジストリを作成する - テンプレート

### <a name="create-a-resource-group"></a>リソース グループを作成する

必要に応じて、[az group create](/cli/azure/group#az_group_create) コマンドを実行し、Azure Container Registry の [可用性ゾーンがサポート](../availability-zones/az-region.md)されているリージョン (*eastus* など) に、レジストリ用のリソース グループを作成します。 このリージョンは、レジストリの場所を設定するためにテンプレートによって使用されます。

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>テンプレートのデプロイ 

次の Resource Manager テンプレートを使用して、ゾーン冗長の geo レプリケートされるレジストリを作成できます。 既定では、このテンプレートによって、レジストリとリージョン レプリカでゾーン冗長が有効になります。 

次の内容を新しいファイルにコピーし、`registryZone.json` などのファイル名を使用して保存します。

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

次の [az deployment group create](/cli/azure/group/deployment#az_group_deployment_create) コマンドを実行し、前述のテンプレート ファイルを使用してレジストリを作成します。 示された場所で次のように指定します。

* 一意のレジストリ名。または、パラメーターを指定せずにテンプレートをデプロイすると、一意の名前が自動的に作成されます
* 可用性ゾーンがサポートされているレプリカの場所 (*westus2* など)

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

コマンドの出力で、レジストリとレプリカの `zoneRedundancy` プロパティを確認します。 有効になっていれば、各リソースはゾーン冗長です。

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>次のステップ

* [可用性ゾーンがサポートされているリージョン](../availability-zones/az-region.md)について、さらに確認する。
* Azure での[信頼性](/azure/architecture/framework/resiliency/overview)の構築について、さらに確認する。
