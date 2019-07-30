---
title: Azure Kubernetes Service で RBAC と Azure AD を使用してクラスター リソースを制御する
description: Azure Kubernetes Service (AKS) でロールベースのアクセス制御 (RBAC) を使用してクラスター リソースへのアクセスを制限するために、Azure Active Directory グループのメンバーシップを使用する方法を学習します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: mlearned
ms.openlocfilehash: fba54fd23fefbe0029b9a809b23568490f05b23e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616162"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service でロールベースのアクセス制御と Azure Active Directory ID を使用してクラスター リソースへのアクセスを制限する

Azure Kubernetes Service (AKS) は、ユーザー認証に Azure Active Directory (AD) を使うように構成することができます。 この構成では、Azure AD 認証トークンを使って AKS クラスターにサインインします。 また、ユーザーの ID またはグループ メンバーシップに基づいて、クラスター リソースへのアクセスを制限するように Kubernetes のロールベースのアクセス制御 (RBAC) を構成することもできます。

この記事では、AKS クラスターで Kubernetes RBAC を使用して名前空間とクラスター リソースへのアクセスを制御するために、Azure AD グループのメンバーシップを使用する方法を示します。 Azure AD でグループとユーザーの例が作成され、リソースを作成および表示するために適切な権限を付与するため AKS クラスター内に Role と RoleBinding が作成されます。

## <a name="before-you-begin"></a>開始する前に

この記事は、Azure AD の統合で有効になっている AKS クラスターが既に存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure Active Directory と AKS の統合][azure-ad-aks-cli]に関するページを参照してください。

Azure CLI バージョン 2.0.61 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="create-demo-groups-in-azure-ad"></a>Azure AD でデモ グループを作成する

この記事では、Kubernetes RBAC と Azure AD がクラスター リソースへのアクセスを制御する方法を示すために使用できる 2 つのユーザー ロールを作成します。 次の 2 つのロールの例が使用されます。

* **アプリケーション開発者**
    * *appdev* グループの一員である *aksdev* という名前のユーザー。
* **Site Reliability Engineer**
    * *opssre* グループの一員である *akssre* という名前のユーザー。

運用環境では、Azure AD テナント内の既存のユーザーとグループを使用できます。

最初に、[az aks show][az-aks-show] コマンドを使用して AKS クラスターのリソース ID を取得します。 追加のコマンドで参照できるように、リソース ID を *AKS_ID* という名前の変数に割り当てます。

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

[az ad group create][az-ad-group-create] コマンドを使用して、アプリケーション開発者用に Azure AD 内に最初のグループ例を作成します。 次の例では、*appdev* という名前のグループを作成します。

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

次に、[az role assignment create][az-role-assignment-create] コマンドを使用して、*appdev* グループに Azure ロール割り当てを作成します。 この割り当てにより、*Azure Kubernetes Service クラスター ユーザー ロール* を付与することで、グループの任意のメンバーが `kubectl` を使用して AKS クラスターとやり取りすることができます。

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` などのエラーが発生した場合は、Azure AD グループ オブジェクト ID がディレクトリ全体に伝達されるまで数秒待ってから、`az role assignment create` コマンドをもう一度試します。

2 番目のグループ例を作成します。これは *opssre* という名前の SRE 用です。

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

ここでも、グループのメンバーに *Azure Kubernetes Service クラスター ユーザー ロール*を付与するための Azure ロール割り当てを作成します。

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Azure AD でデモ ユーザーを作成する

このアプリケーション開発者と SRE に Azure AD で作成した 2 つのグループ例を使用して、2 つのユーザー例を作成しましょう。 この記事の最後で RBAC 統合をテストするには、これらのアカウントを使用して AKS クラスターにサインインします。

[az ad user create][az-ad-user-create] コマンドを使用して、Azure AD で最初のユーザー アカウントを作成します。

次の例では、表示名が *AKS Dev* で `aksdev@contoso.com` のユーザー プリンシパル名 (UPN) を持つユーザーを作成します。 Azure AD テナントの確認済みドメインを含めるように UPN を更新 (*contoso.com* を自分のドメインに置き換えます) し、セキュリティで保護された自分の `--password` 資格情報を指定します。

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

ここで、[az ad group member add][az-ad-group-member-add] コマンドを使用して、ユーザーを前のセクションで作成した *appdev* グループに追加します。

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

2 つ目のユーザー アカウントを作成します。 次の例では、表示名が *AKS SRE* で `akssre@contoso.com` のユーザー プリンシパル名 (UPN) を持つユーザーを作成します。 ここでも、Azure AD テナントの確認済みドメインを含めるように UPN を更新 (*contoso.com* を自分のドメインに置き換えます) し、セキュリティで保護された自分の `--password` 資格情報を指定します。

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>アプリケーション開発者向けに AKS クラスター リソースを作成する

Azure AD グループとユーザーが作成されました。 標準ユーザーとして AKS クラスターに接続するグループ メンバーのために、Azure ロール割り当てが作成されています。 ここで、これらの異なるグループに特定のリソースへのアクセスを許可するように AKS クラスターを構成してみましょう。

まず、[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、クラスター管理者資格情報を取得します。 次のセクションのいずれかで、通常の *user* クラスターの資格情報を取得して、Azure AD 認証のフローの動作を確認します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

[kubectl create namespace][kubectl-create] コマンドを使用して、AKS クラスター内に名前空間を作成します。 次の例では、*dev* という名前空間の名前を作成します。

```console
kubectl create namespace dev
```

Kubernetes では、*Role* によって付与するアクセス許可が定義され、*RoleBinding* によってそれらが目的のユーザーまたはグループに適用されます。 これらの割り当ては、特定の名前空間に適用することも、クラスター全体に適用することもできます。 詳細については、[RBAC 認可の使用][rbac-authorization]に関するページを参照してください。

最初に、*dev* 名前空間に Role を作成します。 このロールにより名前空間に完全なアクセス許可が付与されます。 運用環境では、さまざまなユーザーまたはグループに対してより細かくアクセス許可を指定できます。

`role-dev-namespace.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

[kubectl apply][kubectl-apply] コマンドを使用して Role を作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl apply -f role-dev-namespace.yaml
```

次に、[az ad group show][az-ad-group-show] コマンドを使用して、*appdev* グループのリソース ID を取得します。 このグループは、次の手順で RoleBinding のサブジェクトとして設定されます。

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

ここで、名前空間にアクセスするために以前に作成した Role を使用するため、*appdev* グループの RoleBinding を作成します。 `rolebinding-dev-namespace.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 最後の行で、*groupObjectId* を前のコマンドで出力されたグループ オブジェクト ID に置き換えます。

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

[kubectl apply][kubectl-apply] コマンドを使用して RoleBinding を作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>SRE 向けに AKS クラスター リソースを作成する

ここで、前の手順を繰り返して、SRE の名前空間、Role、および RoleBinding を作成します。

まず、[kubectl create namespace][kubectl-create] コマンドを使用して、*sre* の名前空間を作成します。

```console
kubectl create namespace sre
```

`role-sre-namespace.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

[kubectl apply][kubectl-apply] コマンドを使用して Role を作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl apply -f role-sre-namespace.yaml
```

[az ad group show][az-ad-group-show] コマンドを使用して、*opssre* グループのリソース ID を取得します。

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

名前空間にアクセスするために以前に作成した Role を使用するため、*opssre* グループの RoleBinding を作成します。 `rolebinding-sre-namespace.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。 最後の行で、*groupObjectId* を前のコマンドで出力されたグループ オブジェクト ID に置き換えます。

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

[kubectl apply][kubectl-apply] コマンドを使用して RoleBinding を作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Azure AD の ID を使用してクラスター リソースとやり取りする

ここで、AKS クラスター内でリソースを作成して管理する際に予想されるアクセス許可の動作をテストしてみましょう。 これらの例では、ユーザーの割り当てられた名前空間内でポッドをスケジュール設定して表示します。 次に、割り当てられた名前空間の外側でポッドをスケジュール設定して表示してみます。

まず、[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、*kubeconfig* コンテキストをリセットします。 前のセクションで、クラスター管理者資格情報を使用してコンテキストを設定します。 管理者ユーザーは、Azure AD サインイン プロンプトをバイパスします。 `--admin` パラメーターがないと、ユーザー コンテキストが適用され、すべての要求を Azure AD を使用して認証する必要があります。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

*dev* 名前空間で [kubectl run][kubectl-run] コマンドを使用して、基本的な NGINX ポッドをスケジュール設定します。

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

サインインを求められたら、この記事で最初に作成したご自分の `appdev@contoso.com` アカウントの資格情報を入力します。 正常にサインインすると、将来の `kubectl` コマンド用にアカウント トークンがキャッシュされます。 次の出力例に示すように、NGINX は正常にスケジュール設定されました。

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

今度は、[kubectl get pods][kubectl-get] コマンドを使用して、*dev* 名前空間にポッドを表示します。

```console
kubectl get pods --namespace dev
```

次の出力例に示すように、NGINX ポッドは正常に*実行されています*。

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>割り当てられた名前空間の外側でクラスター リソースを作成して表示する

ここで、*dev* 名前空間の外側でポッドを表示してみましょう。 もう一度 [kubectl get pods][kubectl-get] コマンドを使用します。今度は `--all-namespaces` を次のようにします。

```console
kubectl get pods --all-namespaces
```

次の出力例に示されているように、ユーザーのグループ メンバーシップには、このアクションを許可する Kubernetes Role がありません。

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

同様に、別の名前空間 (*sre* 名前空間など) でポッドをスケジュール設定してみましょう。 次の出力例に示されているように、ユーザーのグループ メンバーシップが、これらのアクセス許可を付与する Kubernetes Role と RoleBinding と一致しません。

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>AKS クラスター リソースへの SRE のアクセスをテストする

Azure AD グループ メンバーシップと Kubernetes RBAC が異なるユーザーとグループ間で正しく機能することを確認するには、*opssre* ユーザーとしてサインインしたときに、前のコマンドを試してみます。

以前にキャッシュした *aksdev* ユーザーの認証トークンをクリアする [az aks get-credentials][az-aks-get-credentials] コマンドを使用して、*kubeconfig* コンテキストをリセットします。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

割り当てられた *sre* 名前空間でポッドをスケジュール設定して表示してみます。 求められたら、この記事の最初に作成した自分の `opssre@contoso.com` 資格情報を使ってサインインします。

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

次の出力例に示すように、ポッドを正常に作成して表示できます。

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

ここで、割り当てられた SRE 名前空間の外側でポッドを表示またはスケジュール設定してみましょう。

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

次の出力例に示すように、これらの `kubectl` コマンドは失敗します。 ユーザーのグループ メンバーシップと Kubernetes Role および RoleBinding では、他の名前空間内でリソースを作成または管理するためのアクセス許可が付与されません。

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事では、AKS クラスター内にリソースを作成し、Azure AD でユーザーとグループを作成しました。 これらのリソースをすべてクリーンアップするには、次のコマンドを実行します。

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>次の手順

Kubernetes クラスターをセキュリティ保護する方法の詳細については、[AKS でのアクセスと ID オプション][rbac-authorization]に関するページを参照してください。

ID とリソース管理に関するベスト プラクティスについては、[AKS の認証と認可のベスト プラクティス][operator-best-practices-identity]に関するページを参照してください。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
