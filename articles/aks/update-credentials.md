---
title: Azure Kubernetes Service (AKS) クラスターの資格情報のリセット
description: Azure Kubernetes Service (AKS) のクラスターのサービス プリンシパル資格情報を更新またはリセットする方法について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 5aac941133296d2040d5dd670155b80f5807e1e9
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614122"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) のサービス プリンシパル資格情報の更新またはローテーション

既定では、有効期限が 1 年のサービス プリンシパルと共に AKS クラスターが作成されます。 期限が近づいたら、資格情報をリセットしてサービス プリンシパルの期限を延長することができます。 また、定義済みのセキュリティ ポリシーの一環として、資格情報を更新またはローテーションすることもできます。 この記事では、AKS クラスターのこれらの資格情報の更新方法について説明します。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.65 以降がインストールされて構成されている必要があります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

## <a name="choose-to-update-or-create-a-service-principal"></a>サービス プリンシパルの更新または作成の選択

AKS クラスターの資格情報を更新する場合、以下の方法から選択できます。

* クラスターで使用されている既存のサービス プリンシパルの資格情報を更新する。または、
* サービス プリンシパルを作成し、それらの新しい資格情報を使用するようにクラスターを更新する。

サービス プリンシパルを作成してから、AKS クラスターを更新する場合は、このセクションの残りのステップをスキップして、「[サービス プリンシパルの作成](#create-a-service-principal)」に進んでください。 AKS クラスターで使用されている既存のサービス プリンシパル資格情報を更新する場合は、このセクションのステップを続行してください。

### <a name="get-the-service-principal-id"></a>サービス プリンシパル ID の取得

既存のサービス プリンシパル資格情報を更新するには、[az aks show][az-aks-show] コマンドを使用して、クラスターのサービス プリンシパル ID を取得します。 以下の例は、*myResourceGroup* リソース グループにある *myAKSCluster* という名前のクラスターの ID を取得します。 サービス プリンシパル ID は、追加コマンドで使用するための *SP_ID* という名前の変数として設定されます。

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>サービス プリンシパル資格情報の更新

サービス プリンシパル ID を含む変数セットを指定し、[az ad sp credential reset][az-ad-sp-credential-reset] を使用して資格情報をリセットします。 以下の例では、Azure プラットフォームがサービス プリンシパルの新しいセキュア シークレットを生成できます。 この新しいセキュア シークレットは、変数としても保管されます。

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

次に、「[新しい資格情報で AKS クラスターを更新](#update-aks-cluster-with-new-credentials)」に進みます。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

前のセクションで既存のサービス プリンシパル資格情報の更新を選択した場合は、このステップをスキップしてください。 「[新しい資格情報で AKS クラスターを更新](#update-aks-cluster-with-new-credentials)」に進みます。

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

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>新しい資格情報で AKS クラスターを更新

既存のサービス プリンシパル資格情報の更新を選択したか、サービス プリンシパルの作成を選択したかに関係なく、ここで [az aks update-credentials][az-aks-update-credentials] コマンドを使用して、新しい資格情報で AKS クラスターを更新します。 *--service-principal* と *--client-secret* の変数が使用されます。

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

サービス プリンシパル資格情報が AKS で更新されるまでに少し時間がかかります。

## <a name="next-steps"></a>次の手順

この記事では、AKS クラスター自体のサービス プリンシパルが更新されました。 クラスター内のワークロードの ID を管理する方法について詳しくは、「[AKS の認証と認可のベスト プラクティス][best-practices-identity]」を参照してください。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
