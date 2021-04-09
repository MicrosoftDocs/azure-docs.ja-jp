---
title: Azure から Kubernetes での Azure RBAC を管理する
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) での Kubernetes 認可に対して Azure RBAC を使用する方法について説明します。
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 9ce8bc71139b52d690893734435e6bfee090062d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184374"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Kubernetes 認可に Azure RBAC を使用する (プレビュー)

現時点で既に、[Azure Active Directory (Azure AD) と AKS の間で統合認証](managed-aad.md)を利用できます。 この統合を有効にすると、お客様は、Kubernetes RBAC の対象として Azure AD のユーザー、グループ、またはサービス プリンシパルを使用できます。詳細については、[こちら](azure-ad-rbac.md)を参照してください。
この機能を使用すると、Kubernetes に対するユーザーの ID と資格情報を個別に管理する必要がなくなります。 ただし、それでも Azure RBAC と Kubernetes RBAC を個別に設定および管理する必要があります。 AKS で RBAC を使用した認証と認可の詳細については、[こちら](concepts-identity.md)をご覧ください。

このドキュメントでは、Azure のリソース、AKS、Kubernetes のリソースで統一された管理とアクセス制御を可能にする新しいアプローチについて説明します。

## <a name="before-you-begin"></a>開始する前に

Azure から Kubernetes のリソースに対する RBAC を管理する機能では、クラスター リソースの RBAC を管理するために、Azure または Kubernetes のネイティブ メカニズムのどちらを使用するかを選択できます。 有効にすると、Azure AD プリンシパルは Azure RBAC だけで検証されますが、Kubernetes の通常のユーザーとサービス アカウントは Kubernetes RBAC だけで検証されます。 AKS で RBAC を使用した認証と認可の詳細については、[こちら](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview)をご覧ください。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>前提条件 
- Azure CLI バージョン 2.9.0 以降があることを確認します
- `EnableAzureRBACPreview` 機能フラグが有効になっていることを確認します。
- `aks-preview` [CLI 拡張機能][az-extension-add] v0.4.55 以降がインストールされていることを確認します
- [kubectl v1.18.3 以降][az-aks-install-cli]がインストールされていることを確認します。

#### <a name="register-enableazurerbacpreview-preview-feature"></a>`EnableAzureRBACPreview` プレビュー機能を登録します

Kubernetes 承認に Azure RBAC を使用する AKS クラスターを作成するには、サブスクリプションで `EnableAzureRBACPreview` 機能フラグを有効にする必要があります。

次の例に示すように [az feature register][az-feature-register] コマンドを使用して、`EnableAzureRBACPreview` 機能フラグを登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 登録状態を確認するには、[az feature list][az-feature-list] コマンドを使用します。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

Azure RBAC を使用する AKS クラスターを作成するには、*aks-preview* CLI 拡張機能バージョン 0.4.55 以降が必要です。 [az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールするか、[az extension update][az-extension-update] コマンドを使用して使用可能な更新プログラムをインストールします。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>制限事項

- [マネージド Azure AD 統合](managed-aad.md)が必要です。
- プレビュー期間中は Kubernetes 認可用の Azure RBAC を既存のクラスターに統合することはできませんが、一般提供 (GA) になるとできます。
- [kubectl v1.18.3 以降][az-aks-install-cli]を使用します。
- CRD を使用していて、カスタム ロール定義を作成している場合、現時点では、CRD をカバーする唯一の方法は `Microsoft.ContainerService/managedClusters/*/read` を提供することです。 AKS では、CRD にさらに詳細なアクセス許可を提供するように取り組んでいます。 残りのオブジェクトについては、特定の API グループを使用できます (例: `Microsoft.ContainerService/apps/deployments/read`)。
- 新しいロールの割り当ては、承認サーバーに伝達されて更新されるまでに最大で 5 分かかることがあります。
- 認証用に構成された Azure AD テナントは、AKS クラスターが含まれるサブスクリプションのテナントと同じである必要があります。 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Azure RBAC とマネージド Azure AD 統合を使用して新しいクラスターを作成する

次の CLI コマンドを使用して、AKS クラスターを作成します。

Azure リソース グループを作成します。

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

マネージド Azure AD 統合と Kubernetes 認可用の Azure RBAC を使用して、AKS クラスターを作成します。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Azure AD 統合と Kubernetes 認可用の Azure RBAC を使用してクラスターが正常に作成された場合、応答本文に次のセクションがあります。

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>ユーザーがクラスターにアクセスするためのロールの割り当てを作成する

AKS には、次の 4 つの組み込みロールがあります。


| Role                                | 説明  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC 閲覧者  | 名前空間内のほとんどのオブジェクトを表示するための読み取り専用アクセスが許可されます。 ロールまたはロールのバインドを表示することはできません。 このロールでは、`Secrets` の表示は許可されません。これは、Secrets の内容を読み取ると、名前空間の ServiceAccount 資格情報にアクセスでき、それにより名前空間の任意の ServiceAccount として API にアクセスできるようになるためです (特権エスカレーションの形式)  |
| Azure Kubernetes Service RBAC ライター | 名前空間内のほとんどのオブジェクトに対する読み取りと書き込みのアクセスが許可されます。 このロールでは、ロールまたはロールのバインドを表示または変更することはできません。 ただし、このロールを使用すると、`Secrets` にアクセスし、名前空間内の任意の ServiceAccount としてポッドを実行できるので、名前空間内の任意の ServiceAccount の API アクセス レベルを取得するために使用できます。 |
| Azure Kubernetes Service RBAC 管理者  | 名前空間内で付与されることが意図された、管理者アクセスが許可されます。 名前空間内でロールおよびロール バインドを作成する能力など、名前空間 (またはクラスター スコープ) 内のほとんどのリソースへの読み取りおよび書き込みアクセスが許可されます。 このロールでは、リソース クォータまたは名前空間自体への書き込みアクセスは許可されません。 |
| Azure Kubernetes Service RBAC クラスター管理者  | 任意のリソースに対して任意のアクションを実行できるスーパー ユーザー アクセスが許可されます。 これにより、クラスター内およびすべての名前空間内のすべてのリソースを完全に制御できます。 |


**AKS クラスター全体** を対象とするロールの割り当ては、Azure portal でクラスター リソースの [アクセス制御 (IAM)] ブレードを使用して、または次に示すように Azure CLI コマンドを使用して、行うことができます。

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

ここで、`<AAD-ENTITY-ID>` には、ユーザー名 (user@contoso.com など) や、サービス プリンシパルの ClientID を指定できます。

また、クラスター内の特定の **名前空間** を対象とするロールの割り当てを作成することもできます。

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

現在、名前空間を対象とするロールの割り当ては、Azure CLI を使用して構成する必要があります。


### <a name="create-custom-roles-definitions"></a>カスタム ロールの定義を作成する

必要に応じて、独自のロールの定義を作成し、上記のように割り当てることもできます。

次に示すのは、ユーザーがデプロイの読み取りだけを行うことができ、他のことはできないようにする、ロールの定義の例です。 使用可能なアクションの完全な一覧は、[こちら](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)で確認できます。


次の json を `deploy-view.json` という名前のファイルにコピーします。

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]
}
```

`<YOUR SUBSCRIPTION ID>` は、サブスクリプションの ID に置き換えます。これは、次を実行することで取得できます。

```azurecli-interactive
az account show --query id -o tsv
```


ここで、`deploy-view.json` を保存したフォルダーから次のコマンドを実行することで、ロールの定義を作成できます。

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

ロールの定義ができたので、次を実行して、ユーザーまたは他の ID にそれを割り当てることができます。

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>`kubectl` で Kubernetes 認可に Azure RBAC を使用する

> [!NOTE]
> 次のコマンドを実行して、最新の kubectl があることを確認します。
>
> ```azurecli-interactive
> az aks install-cli
> ```
> `sudo` 特権で実行することが必要な場合があります。 

これで、必要なロールとアクセス許可が割り当てられました。 たとえば `kubectl` から、Kubernetes API の呼び出しを始めることができます。

この目的のために、まず、次のコマンドを使用してクラスターの kubeconfig を取得してみましょう。

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> 上のステップを実行するには、[Azure Kubernetes Service クラスター ユーザー](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role)組み込みロールが必要です。

ここで、kubectl を使用して、たとえばクラスター内のノードの一覧を表示することができます。 初めて実行するときは、サインインする必要があります。それ以降のコマンドでは、それぞれのアクセス トークンが使用されます。

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>`kubelogin` で Kubernetes 認可に Azure RBAC を使用する

非対話型ログイン、古い `kubectl` バージョン、または新しいクラスターにサインインする必要なしでの複数のクラスター間での SSO の利用など、他のシナリオを使用できるようにするため、トークンが引き続き有効であることが許可され、AKS によって [`kubelogin`](https://github.com/Azure/kubelogin) という名前の実行プラグインが作成されました。

これを使用するには、次を実行します。

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

最初に、通常の kubectl と同様に対話形式でサインインする必要がありますが、その後は、新しい Azure AD クラスターであっても (トークンが有効である限り)、必要なくなります。

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>クリーンアップ

### <a name="clean-role-assignment"></a>ロールの割り当てをクリーンアップする

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
作成したすべての割り当てから ID をコピーします。

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>ロールの定義をクリーンアップする

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>クラスターとリソース グループを削除する

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>次のステップ

- AKS の認証、認可、Kubernetes RBAC、Azure RBAC の詳細については、[こちら](concepts-identity.md)をご覧ください。
- Azure RBAC の詳細については、[こちら](../role-based-access-control/overview.md)を参照してください。
- Kubernetes 承認に対するカスタム Azure ロールを細かく定義するために使用できるすべてのアクションの詳細については、[こちら](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)を参照してください。


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-provider-register]: /cli/azure/provider#az-provider-register
