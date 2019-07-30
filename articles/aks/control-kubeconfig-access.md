---
title: Azure Kubernetes Service (AKS) 内の kubeconfig へのアクセスを制限する
description: クラスターの管理者とクラスターのユーザーを対象に Kubernetes 構成ファイル (kubeconfig) に対するアクセスを制御する方法を紹介します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: cbc653b86ed83f9d6a7348d39f51dc7cd49c6892
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615670"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Azure のロールベースのアクセス制御を使用して Azure Kubernetes Service (AKS) 内の Kubernetes 構成ファイルに対するアクセス権を定義する

`kubectl` ツールを使用すると、Kubernetes クラスターを操作できます。 Azure CLI には、`kubectl` を使って AKS クラスターに接続するためのアクセス資格情報と構成情報を簡単に取得できる手段が用意されています。 Kubernetes の構成 (*kubeconfig*) 情報を取得できる人物を限定したり、その時点で各人に与えられているアクセス許可を制限したりするには、Azure のロールベースのアクセス制御 (RBAC) を使用します。

この記事では、AKS クラスターの構成情報を取得できる人物を限定する RBAC ロールを割り当てる方法を説明します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、AKS のクイックスタートの、[Azure CLI を使用][aks-quickstart-cli]に関するページと、Azure portal に関するページor [using the Azure portal][aks-quickstart-portal]を参照してください。

この記事ではまた、Azure CLI バージョン 2.0.65 以降を実行していることも必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="available-cluster-roles-permissions"></a>利用可能なクラスター ロールのアクセス許可

`kubectl` ツールを使って AKS クラスターを操作するときは、クラスターの接続情報が定義された構成ファイルを使用します。 この構成ファイルは通常、 *~/.kube/config* にあります。この *kubeconfig* ファイルでは、複数のクラスターを定義できます。 クラスターを切り替えるときは、[kubectl config use-context][kubectl-config-use-context] コマンドを使用します。

[az aks get-credentials][az-aks-get-credentials] コマンドを実行すると、任意の AKS クラスターのアクセス資格情報を取得し、*kubeconfig* ファイルにマージすることができます。 このような資格情報に対するアクセスは、Azure のロールベースのアクセス制御 (RBAC) により制御できます。 Azure の RBAC ロールを使えば、*kubeconfig* ファイルを取得できる人物と、その人物がその時点にクラスター内で保有するアクセス許可を定義できます。

あらかじめ用意されているロールは 2 つあります。

* **Azure Kubernetes Service クラスター管理者ロール**  
    * *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* API 呼び出しにアクセスできます。 この API 呼び出しは、[クラスター管理者の資格情報を一覧表示][api-cluster-admin]するものです。
    * *clusterAdmin* ロール用の *kubeconfig* をダウンロードできます。
* **Azure Kubernetes Service クラスター ユーザー ロール**
    * *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* API 呼び出しにアクセスできます。 この API 呼び出しは、[クラスター ユーザーの資格情報を一覧表示][api-cluster-user]するものです。
    * *clusterUser* ロール用の *kubeconfig* をダウンロードできます。

これらの RBAC ロールは、Azure Active Directory (AD) のユーザーまたはグループに適用できます。

## <a name="assign-role-permissions-to-a-user-or-group"></a>ロールのアクセス許可をユーザーまたはグループに割り当てる

使用可能なロールのいずれかを割り当てるには、AKS クラスターのリソース ID と、Azure AD ユーザー アカウントまたはグループの ID を取得する必要があります。 次のコマンド例は以下のように機能します。

* [az aks show][az-aks-show] コマンドを使用して、*myResourceGroup* リソース グループに存在する *myAKSCluster* という名前のクラスターのリソース ID を取得する。 必要に応じて、独自のクラスター名とリソース グループ名を指定してください。
* [az account show ][az-account-show] および、az ad user showand [az ad user show][az-ad-user-show] コマンドを使用して、ユーザー ID を取得する。
* 最後に、[az role assignment create][az-role-assignment-create] コマンドを使用してロールを割り当てる。

次の例では、"*Azure Kubernetes Service クラスター管理者ロール*" を個々のユーザー アカウントに割り当てます。

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Azure AD グループにアクセス許可を割り当てる場合は、*ユーザー*ではなく、*グループ*のオブジェクト ID を使用して、上の例で示した `--assignee` パラメーターを更新してください。 グループのオブジェクト ID を取得するには、[az ad group show][az-ad-group-show] コマンドを使用します。 次の例は、*appdev* という名前の Azure AD グループのオブジェクト ID を取得します。`az ad group show --group appdev --query objectId -o tsv`

この割り当ては、必要に応じて "*クラスター ユーザー ロール*" に変更することもできます。

次の出力例は、ロールの割り当てが正常に作成されたことを示しています。

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>構成情報を取得および確認する

RBAC ロールが割り当てられていれば、[az aks get-credentials][az-aks-get-credentials] コマンドを使って AKS クラスターの *kubeconfig* の定義を取得できます。 次の例は *--admin* 資格情報を取得するもので、ユーザーに "*クラスター管理者ロール*" が付与されていれば正常に動作します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

その後 [kubectl config view][kubectl-config-view] コマンドを使うと、クラスターの "*コンテキスト*" のところで管理者の構成情報が適用されていることを確認できます。

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>ロールのアクセス許可を削除する

ロールの割り当てを削除するには、[az role assignment delete][az-role-assignment-delete] コマンドを使用します。 前に示したコマンドで取得したアカウント ID とクラスターのリソース ID を指定します。 ユーザーではなくグループにロールを割り当てた場合は、`--assignee` パラメーターに対して、アカウント オブジェクト ID ではなく、適切なグループ オブジェクト ID を指定します。

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>次の手順

AKS クラスターにアクセスする際のセキュリティを強化するために、[Azure Active Directory 認証を統合][aad-integration]します。

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
