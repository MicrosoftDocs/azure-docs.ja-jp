---
title: Azure Kubernetes Service (AKS) 用のサービス プリンシパル
description: Azure Kubernetes Service (AKS) のクラスター用の Azure Active Directory サービス プリンシパルを作成して管理する
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mlearned
ms.openlocfilehash: 304b9dae9f3a1e134809d8959a96dc4e3ec0edd3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615104"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのサービス プリンシパル

Azure API で操作するために、AKS クラスターには [Azure Active Directory (AD) サービス プリンシパル][aad-service-principal]が必要です。 サービス プリンシパルは、Azure のロード バランサーやコンテナー レジストリ (ACR) などの Azure リソースを動的に作成し、管理するために必要です。

この記事では、AKS クラスター用のサービス プリンシパルを作成して使用する方法を示します。

## <a name="before-you-begin"></a>開始する前に

Azure AD サービス プリンシパルを作成するには、アプリケーションを Azure AD テナントに登録し、そのアプリケーションをサブスクリプション内のロールに割り当てるためのアクセス許可が必要です。 必要なアクセス許可がない場合は、必要なアクセス許可を割り当てるよう Azure AD またはサブスクリプションの管理者に依頼するか、AKS クラスターで使用するサービス プリンシパルを事前に作成する必要が生じることがあります。

別の Azure AD テナントのサービス プリンシパルを使用している場合は、クラスターのデプロイ時に使用できるアクセス許可について追加の考慮事項があります。 ディレクトリ情報の読み取りと書き込みを行うために適切なアクセス許可がない可能性があります。 詳細については、「[Azure Active Directory の既定のユーザー アクセス許可とは][azure-ad-permissions]」を参照してください。

また、Azure CLI バージョン 2.0.59 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="automatically-create-and-use-a-service-principal"></a>サービス プリンシパルを自動的に作成して使用する

Azure portal で、または [az aks create][az-aks-create] コマンドを使用して AKS クラスターを作成するときには、Azure によるサービス プリンシパルの自動生成が可能です。

次の Azure CLI の例では、サービス プリンシパルは指定されていません。 このシナリオでは、Azure CLI によって、AKS クラスター用のサービス プリンシパルが作成されます。 この操作を正しく完了するためには、Azure アカウントが、サービス プリンシパルを作成するための適切な権限を持っている必要があります。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>手動でサービス プリンシパルを作成する

Azure CLI を使用して手動でサービス プリンシパルを作成するには、[az ad sp create-for-rbac][az-ad-sp-create] コマンドを使用します。 次の例では、`--skip-assignment` パラメーターによって、追加の既定の割り当てが行われないようにしています。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

出力は次の例のようになります。 使っている `appId` と `password` をメモします。 これらの値は、次のセクションで AKS クラスターを作成するときに使用します。

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2019-03-04-21-35-28",
  "name": "http://azure-cli-2019-03-04-21-35-28",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>AKS クラスター用のサービス プリンシパルを指定する

[az aks create][az-aks-create] コマンドを使用して AKS クラスターを作成するときに既存のサービス プリンシパルを使用するには、`--service-principal` パラメーターと `--client-secret` パラメーターを使用して、[az ad sp create-for-rbac][az-ad-sp-create] コマンドの出力の `appId` と `password` を指定します。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

Azure portal を使用して AKS クラスターをデプロイする場合は、 **[Create Kubernetes cluster]\(Kubernetes クラスターの作成)** ダイアログ ボックスの *[Authentication]\(認証)* ページで、 **[Configure service principal]\(サービス プリンシパルの構成)** を選択します。 **[既存のものを使用]** を選択し、以下の値を指定します。

- **[Service principal client ID]\(サービス プリンシパルのクライアント ID)** は、実際の *appId* です
- **[Service principal client secret]\(サービス プリンシパルのクライアント シークレット)** は、*password* の値です

![Azure Vote にブラウザーでアクセスしたところ](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>他の Azure リソースへのアクセスを委任する

AKS クラスターのサービス プリンシパルは、他のリソースへのアクセスに使用することができます。 たとえば、AKS クラスタを既存の Azure 仮想ネットワーク サブネットに展開したり、Azure Container Registry (ACR) に接続したりする場合、それらのリソースへのアクセスをサービス プリンシパルに委任する必要があります。

アクセス許可を委任するには、[az role assignment create][az-role-assignment-create] コマンドを使用してロールの割り当てを作成します。 リソース グループ、仮想ネットワーク リソースなど、特定のスコープに `appId` を割り当てます。 さらに、そのサービス プリンシパルが対象のリソースに対して持つアクセス許可がロールによって定義されます。次の例をご覧ください。

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

リソースの `--scope` には、 */subscriptions/\<guid\>/resourceGroups/myResourceGroup* や */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet* など、完全なリソース ID を指定する必要があります。

以降の各セクションでは、一般的に必要となる委任について詳しく取り上げます。

### <a name="azure-container-registry"></a>Azure Container Registry

Azure Container Registry (ACR) をコンテナーのイメージ ストアとして使用する場合、AKS クラスターがイメージを読み取ってプルするためのアクセス許可を与える必要があります。 レジストリに対する "*閲覧者*" ロールを AKS クラスターのサービス プリンシパルに委任する必要があります。 詳細な手順については、「[ACR へのアクセス許可を AKS に付与する][aks-to-acr]」を参照してください。

### <a name="networking"></a>ネットワーク

仮想ネットワークとサブネットまたはパブリック IP アドレスが別のリソース グループに存在する高度なネットワークを使用することも考えられます。 ロールの一連のアクセス許可として、次のいずれかを割り当てます。

- [カスタム ロール][rbac-custom-role]を作成し、次のロールのアクセス許可を定義します。
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/virtualNetworks/subnets/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- または、仮想ネットワーク内のサブネットに[ネットワーク共同作成者][rbac-network-contributor]の組み込みロールを割り当てます。

### <a name="storage"></a>Storage

別のリソース グループにある既存のディスク リソースにアクセスしなければならない場合があります。 ロールの一連のアクセス許可として、次のいずれかを割り当てます。

- [カスタム ロール][rbac-custom-role]を作成し、次のロールのアクセス許可を定義します。
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- または、リソース グループに対する[ストレージ アカウント共同作成者][rbac-storage-contributor]の組み込みロールを割り当てます。

### <a name="azure-container-instances"></a>Azure Container Instances

Virtual Kubelet を使用して AKS と統合し、AKS クラスターとは別のリソース グループで Azure Container Instances (ACI) を実行する場合は、AKS サービス プリンシパルに ACI リソース グループに対する "*共同作成者*" 権限を与える必要があります。

## <a name="additional-considerations"></a>追加の考慮事項

AKS と Azure AD サービス プリンシパルを使用する場合は、以下の考慮事項を念頭に置いてください。

- Kubernetes のサービス プリンシパルは、クラスター構成の一部です。 ただし、クラスターのデプロイに ID を使用しないでください。
- 既定では、このサービス プリンシパル資格情報は 1 年間有効です。 [サービス プリンシパルの資格情報はいつでも更新または回転][update-credentials]できます。
- すべてのサービス プリンシパルは、Azure AD アプリケーションに関連付けられています。 Kubernetes クラスターのサービス プリンシパルは、有効な任意の Azure AD アプリケーション名 (たとえば *https://www.contoso.org/example* ) に関連付けることができます。 アプリケーションの URL は、実際のエンドポイントである必要はありません。
- サービス プリンシパルの**クライアント ID** を指定するときには、`appId` の値を使用します。
- Kubernetes クラスター内のエージェント ノード VM では、サービス プリンシパルの資格情報が `/etc/kubernetes/azure.json` ファイルに格納されます
- [az aks create][az-aks-create] コマンドを使用してサービス プリンシパルを自動的に生成すると、サービス プリンシパルの資格情報は、コマンドの実行に使用されたコンピューター上の `~/.azure/aksServicePrincipal.json` ファイルに書き込まれます。
- [az aks create][az-aks-create] によって作成された AKS クラスターを削除しても、自動的に作成されたサービス プリンシパルは削除されません。
    - サービス プリンシパルを削除するには、まずクラスターの *servicePrincipalProfile.clientId* を照会し、[az ad app delete][az-ad-app-delete] で削除します。 次のリソース グループとクラスターの名前は、実際の値に置き換えてください。

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>トラブルシューティング

AKS クラスターのサービス プリンシパルの資格情報は、Azure CLI によってキャッシュされます。 これらの資格情報が期限切れになると、AKS クラスターのデプロイ中にエラーが発生します。 [az aks create][az-aks-create] を実行しているときの次のエラー メッセージは、キャッシュされているサービス プリンシパルの資格情報に問題があることを示す可能性があります。

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

次のコマンドを使用して、資格情報ファイルの有効期限を確認します。

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

サービス プリンシパルの資格情報の既定の有効期限は 1 年です。 *aksServicePrincipal.json* ファイルが 1 年よりも古い場合は、ファイルを削除して AKS クラスターを再度デプロイします。

## <a name="next-steps"></a>次の手順

Azure Active Directory サービス プリンシパルの詳細については、「[アプリケーション オブジェクトとサービス プリンシパル オブジェクト][service-principal]」を参照してください。

資格情報の更新方法について詳しくは、「[AKS でのサービス プリンシパルの資格情報の更新または回転][update-credentials]」を参照してください。

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: ../container-registry/container-registry-auth-aks.md?toc=%2fazure%2faks%2ftoc.json#grant-aks-access-to-acr
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
