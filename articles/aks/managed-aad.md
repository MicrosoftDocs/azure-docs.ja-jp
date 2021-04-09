---
title: Azure Kubernetes Service で Azure AD を使用する
description: Azure Kubernetes Service (AKS) における Azure AD の使用方法
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 2cf72da8f7ca82c37088cd6456f094ada2580982
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418965"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS マネージド Azure Active Directory 統合

AKS マネージド Azure AD 統合は、Azure AD の統合エクスペリエンスを簡素化するように設計されています。これまでユーザーは、クライアント アプリとサーバー アプリを作成し、Azure AD テナントでディレクトリの読み取りアクセス許可を付与する必要がありました。 新しいバージョンでは、クライアント アプリとサーバー アプリは AKS リソース プロバイダーで管理されます。

## <a name="azure-ad-authentication-overview"></a>Azure AD 認証の概要

クラスター管理者は、ユーザーの ID またはディレクトリ グループのメンバーシップに基づいて、Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を構成できます。 Azure AD 認証は、OpenID Connect によって AKS クラスターに提供されます。 OpenID Connect は、OAuth 2.0 プロトコル上に構築された ID レイヤーです。 OpenID Connect の詳細については、[OpenID Connect のドキュメント][open-id-connect]を参照してください。

[Azure Active Directory 統合の概念に関するドキュメント](concepts-identity.md#azure-active-directory-integration)で、Azure AD 統合フローの詳細を確認してください。

## <a name="limitations"></a>制限事項 

* AKS マネージド Azure AD 統合は無効にできません
* AKS マネージド Azure AD 統合クラスターをレガシ AAD に変更することはできません
* AKS マネージド Azure AD 統合では、Kubernetes RBAC に対応していないクラスターはサポートされません
* AKS マネージド Azue AD 統合に関連付けられている Azure AD テナントの変更はサポートされません

## <a name="prerequisites"></a>前提条件

* Azure CLI バージョン 2.11.0 以降
* バージョン [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) 以降の kubectl、または [kubelogin](https://github.com/Azure/kubelogin)
* [helm](https://github.com/helm/helm) を使用している場合は、helm 3.3 以降

> [!Important]
> バージョン 1.18.1 以降の kubectl、または kubelogin を使用する必要があります。 Kubernetes と kubectl のマイナー バージョンの差は、1 バージョンを超えることはできません。 適切なバージョンを使用しない場合は、認証の問題が発生します。

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

既存の Kubernetes RBAC 対応クラスターで AKS マネージド Azure AD 統合を有効にすることができます。 クラスターへのアクセスが維持されるように、管理者グループを設定してください。

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

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Azure AD と AKS で条件付きアクセスを使用する

Azure AD を AKS クラスターと統合する場合、[条件付きアクセス][aad-conditional-access]を使用してクラスターへのアクセスを制御することもできます。

> [!NOTE]
> Azure AD 条件付きアクセスは Azure AD Premium の機能です。

AKS で使用する条件付きアクセス ポリシーの例を作成するには、次の手順を実行します。

1. Azure portal の上部で、[Azure Active Directory] を検索して選択します。
1. 左側の Azure Active Directory のメニューで、 *[エンタープライズ アプリケーション]* を選択します。
1. 左側のエンタープライズ アプリケーションのメニューで、 *[条件付きアクセス]* を選択します。
1. 左側の条件付きアクセスのメニューで、 *[ポリシー]* 、 *[新しいポリシー]* の順に選択します。
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="条件付きアクセス ポリシーの追加":::
1. ポリシーの名前を入力します (例: *aks-policy*)。
1. *[ユーザーとグループ]* を選択し、 *[含める]* で *[ユーザーとグループを選択]* を選択します。 ポリシーを適用するユーザーとグループを選択します。 この例では、クラスターへの管理アクセス権を持つ同じ Azure AD グループを選択します。
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="条件付きアクセス ポリシーを適用するユーザーまたはグループの選択":::
1. *[クラウド アプリまたはアクション]* を選択し、 *[含める]* で *[アプリを選択]* を選択します。 *[Azure Kubernetes Service]* を検索し、 *[Azure Kubernetes Service AAD Server]* を選択します。
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="条件付きアクセスポリシーを適用するための Azure Kubernetes Service AD Server の選択":::
1. *[アクセス制御]* で *[許可]* を選択します。 *[アクセス権の付与]* 、 *[デバイスは準拠としてマーク済みである必要があります]* の順に選択します。
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="準拠デバイスのみに条件付きアクセス ポリシーを許可する選択":::
1. *[ポリシーを有効化する]* で、 *[オン]* 、 *[作成]* の順に選択します。
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="条件付きアクセス ポリシーの有効化":::

次のように、クラスターにアクセスするためのユーザー資格情報を取得します。

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

手順に従ってサインインします。

`kubectl get nodes` コマンドを使用してクラスター内のノードを表示します。

```azurecli-interactive
kubectl get nodes
```

手順に従ってもう一度サインインします。 正常にログインしているというエラー メッセージが表示されますが、管理者の要求により、アクセスを要求しているデバイスは Azure AD で管理されていないとリソースにアクセスできません。

Azure portal で [Azure Active Directory] に移動し、 *[エンタープライズ アプリケーション]* を選択し、 *[アクティビティ]* で *[サインイン]* を選択します。一番上のエントリの *[状態]* が *[失敗]* 、 *[条件付きアクセス]* が *[成功]* になっています。 このエントリを選択し、 *[詳細]* で *[条件付きアクセス]* を選択します。 条件付きアクセス ポリシーが表示されます。

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="条件付きアクセス ポリシーにより失敗したサインインのエントリ":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Azure AD と AKS を使用して Just-In-Time クラスター アクセスを構成する

クラスター アクセス制御のもう 1 つの選択肢は、Just-In-Time 要求に Privileged Identity Management (PIM) を使用することです。

>[!NOTE]
> PIM は、Premium P2 SKU を必要とする Azure AD Premium 機能です。 Azure AD SKU の詳細については、[料金ガイド][aad-pricing]を参照してください。

AKS マネージド Azure AD 統合を使用して、Just-In-Time アクセス要求を AKS クラスターに統合するには、次の手順を実行します。

1. Azure portal の上部で、[Azure Active Directory] を検索して選択します。
1. テナント ID (以下の残りの手順にある `<tenant-id>`) をメモしておきます。:::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="Web ブラウザーで、Azure Active Directory の Azure portal 画面にテナント ID が強調表示されています。":::
1. 左側の Azure Active Directory のメニューにある *[管理]* で *[グループ]* を選択し、 *[新しいグループ]* を選択します。
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="[新しいグループ] オプションが強調表示されている Azure portal の Active Directory グループ画面を示します。":::
1. グループの種類として *[セキュリティ]* が選択されていることを確認し、*myJITGroup* などのグループ名を入力します。 *[グループに Azure AD ロールを割り当てることができる (プレビュー)]* で *[はい]* を選択します。 最後に、 *[作成]* を選択します。
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Azure portal の新しいグループ作成画面を示します。":::
1. *[グループ]* ページに戻ります。 新しく作成したグループを選択し、オブジェクト ID (残りの手順では `<object-id>`) をメモします。
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="作成したグループの Azure portal 画面でオブジェクト ID が強調表示されている状態を示す":::
1. 前の `<tenant-id>` および `<object-id>` の値を使用して、AKS マネージド Azure AD 統合を使用した AKS クラスターを配置します。
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. Azure portal に戻り、左側の *[アクティビティ]* メニューで、 *[特権アクセス (プレビュー)]* を選択し、 *[特権アクセスの有効化]* を選択します。
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="[特権アクセスの有効化] が強調表示された Azure portal の [特権アクセス (プレビュー)] ページを示す":::
1. *[割り当ての追加]* を選択してアクセスの付与を開始します。
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="有効化後の Azure portal の [特権アクセス (プレビュー)] 画面を示します。[割り当ての追加] のオプションが強調表示されています。":::
1. *メンバー* ロールを選択し、クラスターへのアクセス権を付与するユーザーとグループを選択します。 これらの割り当ては、グループ管理者によっていつでも変更できます。先に進む準備ができたら、 *[次へ]* を選択します。
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Azure portal の [割り当ての追加] メンバーシップ画面が表示され、サンプル ユーザーがメンバーとして追加されるように選択されています。[次へ] オプションが強調表示されています。":::
1. 割り当ての種類として *[アクティブ]* を選択し、目的の期間を選択して、理由を指定します。 続行する準備ができたら、 *[割り当て]* を選択します。 割り当ての種類の詳細については、「[Privileged Identity Management で特権アクセス グループ (プレビュー) の資格を割り当てる][aad-assignments]」を参照してください。
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Azure portal の [割り当ての追加] の設定画面を示しています。割り当ての種類として [アクティブ] が選択されており、サンプルの理由が指定されています。オプションの [割り当て] が強調表示されています。":::

割り当てが完了したら、クラスターにアクセスして、Just-In-Time アクセスが機能していることを確認します。 次に例を示します。

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

手順に従ってサインインします。

`kubectl get nodes` コマンドを使用してクラスター内のノードを表示します。

```azurecli-interactive
kubectl get nodes
```

認証要件を確認し、手順に従って認証します。 成功した場合は、次のような出力が表示されます。

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>トラブルシューティング

`kubectl get nodes` から次のようなエラーが表示されます。

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

セキュリティ グループの管理者によって、アカウントに *[アクティブ]* な割り当てが付与されていることを確認します。

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
[aad-pricing]: /azure/pricing/details/active-directory

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
