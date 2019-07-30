---
title: Kubernetes on Azure のチュートリアル - クラスターのデプロイ
description: この Azure Kubernetes Service (AKS) のチュートリアルでは、AKS クラスターを作成し、kubectl を使用して Kubernetes マスター ノードに接続します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 27ec77e15d1289742fa40320631684d37c9660a1
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614269"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>チュートリアル:Azure Kubernetes Service (AKS) クラスターのデプロイ

Kubernetes には、コンテナー化されたアプリケーション用の分散プラットフォームが用意されています。 AKS を使うと、運用開始準備の整った Kubernetes クラスターを迅速に作成できます。 このチュートリアル (7 部構成の第 3 部) では、Kubernetes クラスターを AKS にデプロイします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * リソースとの対話に使用するサービス プリンシパルを作成する
> * Kubernetes AKS クラスターをデプロイする
> * Kubernetes CLI (kubectl) をインストールする
> * kubectl を構成して AKS クラスターに接続する

追加のチュートリアルでは、Azure Vote アプリケーションをクラスターにデプロイし、スケーリングおよび更新します。

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、コンテナー イメージを作成して、Azure Container Registry インスタンスにアップロードしました。 これらの手順を完了しておらず、順番に進めたい場合は、[チュートリアル 1 - コンテナー イメージを作成する][aks-tutorial-prepare-app]に関するページから開始してください。

このチュートリアルでは、Azure CLI バージョン 2.0.53 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

AKS クラスターが他の Azure リソースと対話できるようにするために、Azure Active Directory のサービス プリンシパルを使用します。 このサービス プリンシパルは、Azure CLI または Azure portal で自動的に作成するか、または事前に手動で作成しておいて、アクセス許可を追加で割り当てることができます。 このチュートリアルでは、サービス プリンシパルを手動で作成し、前のチュートリアルで作成した Azure Container Registry (ACR) インスタンスへのアクセス権を与えた後、AKS クラスターを作成することにします。

[az ad sp create-for-rbac][] コマンドを使用して、サービス プリンシパルを作成します。 `--skip-assignment` パラメーターで、余分なアクセス許可の割り当てを制限します。 既定では、このサービス プリンシパルは 1 年間有効です。

```azurecli
az ad sp create-for-rbac --skip-assignment
```

出力は次の例のようになります。

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

*appId* と *password* を書き留めておいてください。 これらの値は、以降の手順で使用します。

## <a name="configure-acr-authentication"></a>ACR 認証を構成する

ACR に格納されているイメージにアクセスするためには、ACR からイメージをプルするための適切な権限を AKS のサービス プリンシパルに与える必要があります。

まず、[az acr show][] を使用して、ACR のリソース ID を取得します。 `<acrName>` レジストリ名は実際の ACR インスタンスの名前に、またリソース グループは、その ACR インスタンスが存在するリソース グループの名前に置き換えてください。

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

ACR に格納されているイメージをプルするための適切なアクセス権を AKS クラスターに与えるには、[az role assignment create][] コマンドを使用して `AcrPull` ロールを割り当てます。 `<appId`> と `<acrId>` は、前の 2 つの手順で書き留めた値に置き換えてください。

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Kubernetes クラスターの作成

AKS クラスターでは、Kubernetes のロールベースのアクセス制御 (RBAC) を使用できます。 これらのコントロールを使用すると、ユーザーに割り当てられているロールに基づいてリソースへのアクセスを定義できます。 ユーザーに複数のロールが割り当てられている場合は、アクセス許可が組み合わされます。また、アクセス許可のスコープを 1 つの名前空間またはクラスター全体に設定できます。 Azure CLI の既定では、AKS クラスターを作成するときに RBAC が自動的に有効になります。

[az aks create][] を使用して AKS クラスターを作成します。 次の例では、*myResourceGroup* という名前のリソース グループに *myAKSCluster* という名前のクラスターを作成します。 このリソース グループは、[前のチュートリアル][aks-tutorial-prepare-acr]で作成しました。 先ほどサービス プリンシパルを作成する手順で使用した実際の `<appId>` と `<password>` を指定してください。

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

数分してデプロイが完了すると、この AKS デプロイに関する情報が JSON 形式で表示されます。

## <a name="install-the-kubernetes-cli"></a>Kubernetes CLI のインストール

ご利用のローカル コンピューターから Kubernetes クラスターに接続するには、[kubectl][kubectl] (Kubernetes コマンドライン クライアント) を使用します。

Azure Cloud Shell を使用している場合、`kubectl` は既にインストールされています。 [az aks install-cli][] コマンドを使用してローカルにインストールすることもできます。

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>kubectl を使用したクラスターへの接続

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az aks get-credentials][] コマンドを使用します。 次の例では、*myResourceGroup* の *myAKSCluster* という名前の AKS クラスターの資格情報を取得します。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

ご利用のクラスターへの接続を確認するには、[kubectl get nodes][kubectl-get] コマンドを実行します。

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-28993262-0   Ready    agent   3m18s   v1.9.11
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Kubernetes クラスターを AKS にデプロイし、`kubectl` を構成してクラスターに接続しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * リソースとの対話に使用するサービス プリンシパルを作成する
> * Kubernetes AKS クラスターをデプロイする
> * Kubernetes CLI (kubectl) をインストールする
> * kubectl を構成して AKS クラスターに接続する

次のチュートリアルに進み、アプリケーションをクラスターにデプロイする方法を学習してください。

> [!div class="nextstepaction"]
> [Kubernetes でアプリケーションをデプロイする][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
