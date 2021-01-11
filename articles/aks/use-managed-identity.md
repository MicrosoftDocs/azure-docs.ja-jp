---
title: Azure Kubernetes Service でマネージド ID を使用する
description: Azure Kubernetes Service (AKS) でマネージド ID を使用する方法について説明します。
services: container-service
ms.topic: article
ms.date: 07/17/2020
ms.author: thomasge
ms.openlocfilehash: 8c5c4a6e5d8b2997d80c7263ba17a705d3846ed8
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987394"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service でマネージド ID を使用する

現在、Azure Kubernetes Service (AKS) クラスター (具体的には Kubernetes クラウド プロバイダー) で Azure 内にロード バランサーやマネージド ディスクなどの追加リソースを作成するには、ID が必要です。 この ID には、*マネージド ID* または*サービス プリンシパル*を指定できます。 [サービス プリンシパル](kubernetes-service-principal.md)を使用する場合、それを 1 つ指定する必要があります。指定しない場合、AKS によって代理で作成されます。 マネージド ID を使用する場合は、AKS によってこれが自動作成されます。 サービス プリンシパルを使用するクラスターでは、やがてサービス プリンシパルを更新しないと、そのクラスターを動作させ続けることができない状態になります。 サービス プリンシパルを管理しなければならない場合、複雑さが増すので、代わりにマネージ ID を使用した方が簡単です。 アクセス許可の要件は、サービス プリンシパルにおいてもマネージド ID においても同じです。

*マネージド ID* は、本質的にサービス プリンシパルのラッパーであり、管理がより簡単になります。 MI の資格情報のローテーションは、Azure Active Directory の既定に従って 46 日ごとに自動的に行われます。 AKS では、システム割り当てとユーザー割り当ての両方の種類のマネージド ID が使用されます。 これらの ID は、現在変更できません。 詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) について確認してください。

## <a name="before-you-begin"></a>開始する前に

次のリソースがインストールされている必要があります。

- Azure CLI バージョン 2.8.0 以降

## <a name="limitations"></a>制限事項

* マネージド ID を指定した AKS クラスターは、クラスターの作成時にのみ有効にすることができます。
* 既存の AKS クラスターはマネージド ID に移行できません。
* クラスターの**アップグレード**操作中は、マネージド ID が一時的に使用できなくなります。
* マネージド ID が有効になっているクラスターのテナントの移動/移行はサポートされていません。

## <a name="summary-of-managed-identities"></a>マネージド ID の概要

AKS では、組み込みのサービスとアドオンに対して複数のマネージド ID が使用されます。

| ID                       | 名前    | 使用事例 | 既定のアクセス許可 | 独自の ID を使用する
|----------------------------|-----------|----------|
| コントロール プレーン | 非表示 | イングレス ロード バランサーや AKS マネージド パブリック IP などのマネージド ネットワーク リソース用に AKS によって使用されます | ノード リソース グループの共同作成者ロール | プレビュー
| kubelet | AKS クラスター名 - agentpool | Azure Container Registry (ACR) を使用した認証 | ノード リソース グループの閲覧者ロール | 現在、サポートされていません
| アドオン | AzureNPM | ID は必要ありません | NA | いいえ
| アドオン | AzureCNI ネットワーク監視 | ID は必要ありません | NA | いいえ
| アドオン | azurepolicy (ゲートキーパー) | ID は必要ありません | NA | いいえ
| アドオン | azurepolicy | ID は必要ありません | NA | いいえ
| アドオン | Calico | ID は必要ありません | NA | いいえ
| アドオン | ダッシュボード | ID は必要ありません | NA | いいえ
| アドオン | HTTPApplicationRouting | 必要なネットワーク リソースを管理します | ノード リソース グループの閲覧者ロール、DNS ゾーンの共同作成者ロール | いいえ
| アドオン | イングレス アプリケーション ゲートウェイ | 必要なネットワーク リソースを管理します| ノード リソース グループの共同作成者ロール | いいえ
| アドオン | omsagent | AKS メトリックを Azure Monitor に送信するために使用されます | 監視メトリック パブリッシャー ロール | いいえ
| アドオン | 仮想ノード (ACIConnector) | Azure Container Instances (ACI) のために必要なネットワーク リソースを管理します | ノード リソース グループの共同作成者ロール | いいえ


## <a name="create-an-aks-cluster-with-managed-identities"></a>マネージド ID を指定して AKS クラスターを作成する

次の CLI コマンドを使用し、マネージド ID を指定して AKS クラスターを作成できるようになりました。

最初に、Azure リソース グループを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

次に、AKS クラスターを作成します。

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

マネージド ID を使用して正常にクラスターが作成されると、次のサービス プリンシパル プロファイル情報が含まれます。

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

次のコマンドを使用して、コントロール プレーン マネージド ID の objectid を照会します。

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

結果は次のようになります。

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

クラスターが作成されたら、新しいクラスターにアプリケーションのワークロードをデプロイし、サービス プリンシパル ベースの AKS クラスターの場合と同様に対話できます。

> [!NOTE]
> 独自の VNet、静的 IP アドレス、またはアタッチされた Azure ディスク (リソースはワーカー ノード リソース グループの外部にある) を作成および使用するには、クラスター System Assigned Managed Identity の PrincipalID を使用してロールの割り当てを実行します。 ロールの割り当ての詳細については、[他の Azure リソースへのアクセスの委任](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)に関する記事を参照してください。
>
> Azure クラウド プロバイダーによって使用されるクラスター マネージド ID へのアクセス許可が付与されるまでに最大 60 分かかる場合があります。

最後に、クラスターにアクセスする資格情報を取得します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="bring-your-own-control-plane-mi-preview"></a>独自のコントロール プレーン MI を使用する (プレビュー)
カスタムのコントロール プレーン ID を使用すると、クラスターの作成前に、既存の ID にアクセス権を付与できます。 これにより、カスタム VNET や outboundType UDR をマネージド ID と一緒に使用するなどのシナリオが可能になります。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

次のリソースがインストールされている必要があります。
- Azure CLI バージョン 2.9.0 以降
- aks-preview 0.4.57 拡張機能

独自のコントロール プレーン MI を使用する場合の制限事項 (プレビュー) :
* Azure Government は現在サポートされていません。
* Azure China 21Vianet は現在サポートされていません。

```azurecli-interactive
az extension add --name aks-preview
az extension list
```

```azurecli-interactive
az extension update --name aks-preview
az extension list
```

```azurecli-interactive
az feature register --name UserAssignedIdentityPreview --namespace Microsoft.ContainerService
```

状態が "**登録済み**" と表示されるまでに数分かかることがあります。 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UserAssignedIdentityPreview')].{Name:name,State:properties.state}"
```

状態が登録済みと表示されたら、[az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) コマンドを使用して、`Microsoft.ContainerService` リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

マネージド ID をまだ持っていない場合は、[az identity CLI][az-identity-create] などを使用して作成してください。

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
結果は次のようになります。

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

マネージド ID がサブスクリプションの一部である場合は、[az identity CLI コマンド][az-identity-list]を使用してクエリを実行できます。  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

これで、次のコマンドを使用して、既存の ID でクラスターを作成できるようになりました。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

独自のマネージド ID を使用して正常にクラスターが作成されると、次の userAssignedIdentities プロファイル情報が含まれています。

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>次のステップ
* マネージド ID が有効になっているクラスターを作成するには、[Azure Resource Manager (ARM) テンプレート][aks-arm-template]を使用します。

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list
