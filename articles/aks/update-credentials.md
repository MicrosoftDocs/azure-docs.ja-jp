---
title: クラスターの資格情報のリセット
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) クラスター用のサービス プリンシパル資格情報または AAD アプリケーション資格情報を更新またはリセットする方法について説明します
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 84cadc54a11c2e57d73fd1f06e98deb3d599d648
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501177"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 用の資格情報を更新またはローテーションする

サービス プリンシパルで作成された AKS クラスターには 1 年間の有効期間があります。 期限が近づいたら、資格情報をリセットしてサービス プリンシパルの期限を延長することができます。 また、定義済みのセキュリティ ポリシーの一環として、資格情報を更新またはローテーションすることもできます。 この記事では、AKS クラスターのこれらの資格情報の更新方法について説明します。

また、[AKS クラスターと Azure Active Directory を統合][aad-integration]してあり、クラスターの認証プロバイダーとしてそれを使用する場合もあります。 その場合は、クラスター、AAD サーバー アプリ、AAD クライアント アプリ用にさらに 2 つの ID が作成されていて、それらの資格情報もリセットできます。

または、サービス プリンシパルの代わりに、マネージド ID をアクセス許可に使用できます。 マネージド ID は、サービス プリンシパルよりも簡単に管理でき、更新やローテーションが必要ありません。 詳細については、[マネージド ID の使用](use-managed-identity.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.65 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>AKS クラスター用の新しいサービス プリンシパルを更新または作成する

AKS クラスターの資格情報を更新するときは、以下のどちらかを選択できます。

* 既存のサービス プリンシパルの資格情報を更新します。
* 新しいサービス プリンシパルを作成し、それらの新しい資格情報を使用するようにクラスターを更新します。 

> [!WARNING]
> "*新しい*" サービス プリンシパルの作成を選択する場合、これらの資格情報を使用するための大規模な AKS クラスターの更新には、完了までに時間がかかることがあります。

### <a name="check-the-expiration-date-of-your-service-principal"></a>サービス プリンシパルの有効期限を確認する

サービス プリンシパルの有効期限を確認するには、[az ad sp credential list][az-ad-sp-credential-list] コマンドを使用します。 次の例では、[az aks show][az-aks-show] コマンドを使用して、*myResourceGroup* リソース グループ内の *myAKSCluster* という名前のクラスターのサービス プリンシパル ID を取得します。 サービス プリンシパル ID は、[az ad sp credential list][az-ad-sp-credential-list] コマンドで使用するための *SP_ID* という名前の変数として設定されます。

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>既存のサービス プリンシパルの資格情報をリセットする

既存のサービス プリンシパル資格情報を更新するには、[az aks show][az-aks-show] コマンドを使用して、クラスターのサービス プリンシパル ID を取得します。 以下の例は、*myResourceGroup* リソース グループにある *myAKSCluster* という名前のクラスターの ID を取得します。 サービス プリンシパル ID は、追加コマンドで使用するための *SP_ID* という名前の変数として設定されます。 これらのコマンドでは Bash 構文を使用します。

> [!WARNING]
> Azure Virtual Machine Scale Sets を使用する AKS クラスターでクラスターの資格情報をリセットすると、[ノード イメージのアップグレード][node-image-upgrade]が実行され、新しい資格情報でノードが更新されます。

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

サービス プリンシパル ID を含む変数セットを指定し、[az ad sp credential reset][az-ad-sp-credential-reset] を使用して資格情報をリセットします。 以下の例では、Azure プラットフォームがサービス プリンシパルの新しいセキュア シークレットを生成できます。 この新しいセキュア シークレットは、変数としても保管されます。

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

次に、[新しいサービス プリンシパル資格情報での AKS クラスターの更新](#update-aks-cluster-with-new-service-principal-credentials)に進みます。 このステップは、サービス プリンシパルの変更を AKS クラスターに反映させるために必要です。

### <a name="create-a-new-service-principal"></a>新しいサービス プリンシパルを作成する

前のセクションで既存のサービス プリンシパル資格情報の更新を選択した場合は、このステップをスキップしてください。 続いて、[新しいサービス プリンシパル資格情報で AKS クラスターを更新](#update-aks-cluster-with-new-service-principal-credentials)します。

サービス プリンシパルを作成してから、それらの新しい資格情報を使用するように AKS クラスターを更新するには、[az ad sp create-for-rbac][az-ad-sp-create] コマンドを使用します。 次の例では、`--skip-assignment` パラメーターによって、追加の既定の割り当てが行われないようにしています。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

出力は次の例のようになります。 使っている `appId` と `password` をメモします。 これらの値は、次のステップで使用します。

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

次に、以下の例に示すように、使用した [az ad sp create-for-rbac][az-ad-sp-create] コマンドの出力を使用して、サービス プリンシパル ID とクライアント シークレットの変数を定義します。 *SP_ID* は *appId* で、*SP_SECRET* は *パスワード* です。

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

次に、[新しいサービス プリンシパル資格情報での AKS クラスターの更新](#update-aks-cluster-with-new-service-principal-credentials)に進みます。 このステップは、サービス プリンシパルの変更を AKS クラスターに反映させるために必要です。

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>新しいサービス プリンシパル資格情報で AKS クラスターを更新する

> [!IMPORTANT]
> 大規模なクラスターでは、新しいサービス プリンシパルによる AKS クラスターの更新が完了するまでに、時間がかかることがあります。

既存のサービス プリンシパル資格情報の更新を選択したか、サービス プリンシパルの作成を選択したかに関係なく、ここで [az aks update-credentials][az-aks-update-credentials] コマンドを使用して、新しい資格情報で AKS クラスターを更新します。 *--service-principal* と *--client-secret* の変数が使用されます。

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

小規模および中規模のクラスターの場合は、AKS でサービス プリンシパルの資格情報の更新にかかる時間はそれほど長くありません。

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>新しい AAD アプリケーション資格情報で AKS クラスターを更新する

[AAD 統合手順][create-aad-app]に従って、新しい AAD サーバー アプリケーションとクライアント アプリケーションを作成できます。 または、[サービス プリンシパルのリセットの場合と同じ方法](#reset-the-existing-service-principal-credential)に従って、既存の AAD アプリケーションをリセットします。 その後は、同じ [az aks update-credentials][az-aks-update-credentials] コマンドを使用し、ただし *--reset-aad* 変数を使用して、クラスター AAD アプリケーションの資格情報を更新することだけが必要です。

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>次のステップ

この記事では、AKS クラスター自体と AAD 統合アプリケーション用のサービス プリンシパルを更新しました。 クラスター内のワークロードの ID を管理する方法について詳しくは、「[AKS の認証と認可のベスト プラクティス][best-practices-identity]」を参照してください。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az-ad-sp-credential-list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[node-image-upgrade]: ./node-image-upgrade.md
