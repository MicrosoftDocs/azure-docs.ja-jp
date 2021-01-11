---
title: Azure Kubernetes Service で Azure AD を使用する
description: Azure Kubernetes Service (AKS) における Azure AD の使用方法
services: container-service
ms.topic: article
ms.date: 08/26/2020
ms.author: thomasge
ms.openlocfilehash: 32273bbb14e6cee73f03bd83b84be77299186370
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936998"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS マネージド Azure Active Directory 統合

AKS マネージド Azure AD 統合は、Azure AD の統合エクスペリエンスを簡素化するように設計されています。これまでユーザーは、クライアント アプリとサーバー アプリを作成し、Azure AD テナントでディレクトリの読み取りアクセス許可を付与する必要がありました。 新しいバージョンでは、クライアント アプリとサーバー アプリは AKS リソース プロバイダーで管理されます。

## <a name="azure-ad-authentication-overview"></a>Azure AD 認証の概要

クラスター管理者は、ユーザーの ID またはディレクトリ グループのメンバーシップに基づいて、Kubernetes のロールベースのアクセス制御 (RBAC) を構成できます。 Azure AD 認証は、OpenID Connect によって AKS クラスターに提供されます。 OpenID Connect は、OAuth 2.0 プロトコル上に構築された ID レイヤーです。 OpenID Connect の詳細については、[OpenID Connect のドキュメント][open-id-connect]を参照してください。

[Azure Active Directory 統合の概念に関するドキュメント](concepts-identity.md#azure-active-directory-integration)で、Azure AD 統合フローの詳細を確認してください。

## <a name="region-availability"></a>利用可能なリージョン

AKS マネージド Azure Active Directory 統合は、[AKS がサポートされている](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)パブリック リージョンで利用できます。

* Azure Government は現在サポートされていません。
* Azure China 21Vianet は現在サポートされていません。

## <a name="limitations"></a>制限事項 

* AKS マネージド Azure AD 統合は無効にできません
* AKS マネージド Azue AD 統合では、RBAC に対応していないクラスターはサポートされません
* AKS マネージド Azue AD 統合に関連付けられている Azure AD テナントの変更はサポートされません

## <a name="prerequisites"></a>前提条件

* Azure CLI バージョン 2.11.0 以降
* バージョン [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) 以降の kubectl、または [kubelogin](https://github.com/Azure/kubelogin)
* [helm](https://github.com/helm/helm) を使用している場合は、helm 3.3 以降

> [!Important]
> バージョン 1.18.1 以降の kubectl、または kubelogin を使用する必要があります。 適切なバージョンを使用しない場合は、認証の問題が発生します。

kubectl および kubelogin をインストールするには、次のコマンドを使用します。

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

他のオペレーティング システムでは、[これらの手順](https://kubernetes.io/docs/tasks/tools/install-kubectl/)を使用します。


## <a name="before-you-begin"></a>開始する前に

クラスターには、Azure AD グループが必要です。 このグループは、クラスターの管理アクセス許可を付与する、クラスターの管理者グループとして必要です。 既存の Azure AD グループを使用することも、新しい Azure AD グループを作成することもできます。 Azure AD グループのオブジェクト ID を記録します。

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

クラスター管理者用の新しい Azure AD グループを作成するには、次のコマンドを使用します。

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Azure AD が有効になっている AKS クラスターを作成する

次の CLI コマンドを使用して、AKS クラスターを作成します。

Azure リソース グループを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

AKS クラスターを作成し、Azure AD グループの管理アクセスを有効にします

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

正常に作成された AKS マネージド Azure AD クラスターの応答本文には、次のセクションが含まれます
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

クラスターを作成したら、クラスターへのアクセスを開始できます。

## <a name="access-an-azure-ad-enabled-cluster"></a>Azure AD が有効なクラスターへのアクセス

次の手順を実行するには、[Azure Kubernetes Service クラスター ユーザー](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role)組み込みロールが必要です。

クラスターにアクセスするためのユーザー資格情報を取得します。
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
手順に従ってサインインします。

kubectl get nodes コマンドを使用して、クラスターのノードを表示します。

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
[Azure ロールベースのアクセス制御 (Azure RBAC)](./azure-ad-rbac.md) を構成して、クラスターの追加のセキュリティ グループを構成します。

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Azure AD でのアクセスに関する問題のトラブルシューティング

> [!Important]
> 次に示す手順では、通常の Azure AD グループ認証をバイパスしています。 これは緊急時にのみ使用してください。

クラスターへのアクセス権を持つ有効な Azure AD グループへのアクセス権がないために永続的にブロックされている場合でも、クラスターに直接アクセスするための管理者資格情報を取得することができます。

これらの手順を実行するには、[Azure Kubernetes Service クラスター管理者](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)組み込みロールにアクセスできる必要があります。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>既存のクラスターで AKS マネージド Azure AD 統合を有効にする

既存の RBAC 対応クラスターで AKS マネージド Azure AD 統合を有効にすることができます。 クラスターへのアクセスが維持されるように、管理者グループを設定してください。

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

正常にアクティブ化された AKS マネージド Azure AD クラスターの応答本文には、次のセクションが含まれます

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

[こちら][access-cluster]の手順に従って、ユーザー資格情報をもう一度ダウンロードして、クラスターにアクセスします。

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>AKS マネージド Azure AD 統合へのアップグレード

クラスターでレガシ Azure AD 統合を使用している場合は、AKS マネージド Azure AD 統合にアップグレードできます。

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

正常に移行された AKS マネージド Azure AD クラスターの応答本文には、次のセクションが含まれます

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

クラスターにアクセスする場合は、[こちら][access-cluster]の手順に従ってください。

## <a name="non-interactive-sign-in-with-kubelogin"></a>kubelogin を使用した非対話型サインイン

継続的インテグレーション パイプラインなど、現在 kubectl で使用できない非対話型シナリオがいくつかあります。 [`kubelogin`](https://github.com/Azure/kubelogin) を使用して、非対話型サービス プリンシパル サインインでクラスターにアクセスできます。

## <a name="next-steps"></a>次のステップ

* [Kubernetes 承認用の Azure RBAC 統合][azure-rbac-integration]について学習する。
* [Azure AD と Kubernetes RBAC の統合][azure-ad-rbac]について学習する。
* [kubelogin](https://github.com/Azure/kubelogin) を使用して、kubectl では利用できない Azure 認証の機能にアクセスする。
* [AKS と Kubernetes ID の概念][aks-concepts-identity]について学習する。
* [Azure Resource Manager (ARM) テンプレート][aks-arm-template]を使用して、AKS マネージド Azure AD が有効なクラスターを作成する。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
