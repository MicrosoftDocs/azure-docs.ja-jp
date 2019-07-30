---
title: Azure Active Directory と Azure Kubernetes Service を統合する
description: Azure CLI を使用して Azure Active Directory 対応の Azure Kubernetes Service (AKS) クラスターを作成する方法を学習する
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: mlearned
ms.openlocfilehash: 5b99d76ef20c288d6ae0bd33e1e2b6a75a359d3a
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "67616268"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Azure CLI を使用して Azure Active Directory と Azure Kubernetes Service を統合する

Azure Kubernetes Service (AKS) は、ユーザー認証に Azure Active Directory (AD) を使うように構成することができます。 この構成では、Azure AD 認証トークンを使って AKS クラスターにログインできます。 また、クラスター オペレーターが、ユーザーの ID またはディレクトリ グループ メンバーシップに基づいて、Kubernetes のロールベースのアクセス制御 (RBAC) を構成することもできます。

この記事では、必要な Azure AD コンポーネントを作成してから、Azure AD 対応クラスターをデプロイして AKS クラスターで基本的な RBAC ロールを作成する方法について説明します。 [これらの手順は、Azure portal を使用して完了][azure-ad-portal]することもできます。

この記事で使用されているサンプル スクリプトの完成版については、[Azure CLI のサンプルの AKS と Azure AD の統合][complete-script]に関するページを参照してください。

次の制限事項が適用されます。

- Azure AD は、RBAC が有効なクラスターを新しく作成するときにのみ有効にできます。 既存の AKS クラスターで Azure AD を有効にすることはできません。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.61 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

一貫性を保ち、この記事のコマンドを実行するのに役立てるため、目的の AKS クラスター名に変数を作成します。 次の例では、*myakscluster* という名前を使用しています。

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD 認証の概要

Azure AD 認証は、OpenID Connect によって AKS クラスターに提供されます。 OpenID Connect は、OAuth 2.0 プロトコル上に構築された ID レイヤーです。 OpenID Connect の詳細については、[OpenID Connect のドキュメント][open-id-connect]を参照してください。

Kubernetes クラスターの内部からは、webhook トークン認証を使って認証トークンが確認されます。 webhook トークン認証は、AKS クラスターの一部として構成および管理されます。 Webhook トークン認証の詳細については、[Webhook 認証のドキュメント][kubernetes-webhook]を参照してください。

> [!NOTE]
> AKS 認証用に Azure AD を構成すると、2 つの Azure AD アプリケーションが構成されます。 この操作は、Azure テナント管理者が行う必要があります。

## <a name="create-azure-ad-server-component"></a>Azure AD サーバー コンポーネントを作成する

AKS と統合するには、ID 要求のエンドポイントとして機能する Azure AD アプリケーションを作成して使用します。 必要な最初の Azure AD アプリケーションが、ユーザーの Azure AD グループ メンバーシップを取得します。

[az ad app create][az-ad-app-create] コマンドを使用してサーバー アプリケーション コンポーネントを作成してから、[az ad app update][az-ad-app-update] コマンドを使用してそのグループ メンバーシップの要求を更新します。 次の例では、「[開始する前に](#before-you-begin)」セクションで定義した *aksname* 変数を使用して変数を作成します。

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

ここで、[az ad sp create][az-ad-sp-create] コマンドを使用して、サーバー アプリのサービス プリンシパルを作成します。 このサービス プリンシパルは、Azure プラットフォーム内で自身を認証するために使用されます。 次に、[az ad sp credential reset][az-ad-sp-credential-reset] コマンドを使用してサービス プリンシパルのシークレットを取得し、次のいずれかのステップで使用するために *serverApplicationSecret* という名前の変数に割り当てます。

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD には、次のアクションを実行するためのアクセス許可が必要です。

* ディレクトリ データの読み取り
* サインインとユーザー プロファイルの読み取り

[az ad app permission add][az-ad-app-permission-add] コマンドを使用して、これらのアクセス許可を割り当てます。

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

最後に、[az ad app permission grant][az-ad-app-permission-grant] コマンドを使用して、前の手順で割り当てたアクセス許可をサーバー アプリケーションに付与します。 現在のアカウントがテナント管理者ではない場合、このステップは失敗します。また、[az ad app permission admin-consent][az-ad-app-permission-admin-consent] を使用して、管理者の同意を別途必要とする可能性のある情報を Azure AD アプリケーションが要求するためのアクセス許可を追加する必要もあります。

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Azure AD クライアント コンポーネントを作成する

2 つ目の Azure AD アプリケーションは、Kubernetes CLI (`kubectl`) を使用してユーザーが AKS クラスターにログインするときに使われます。 このクライアント アプリケーションでは、認証要求をユーザーから取得して、その資格情報とアクセス許可を確認します。 [az ad app create][az-ad-app-create] コマンドを使用して、クライアント コンポーネント用に Azure AD アプリを作成します。

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

[az ad sp create][az-ad-sp-create] コマンドを使用して、クライアント アプリケーション用にサービス プリンシパルを作成します。

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

[az ad app show][az-ad-app-show] コマンドを使用して、サーバー アプリの oAuth2 ID を取得して、2 つのアプリ コンポーネント間の認証フローを許可します。 この oAuth2 ID は、次のステップで使用されます。

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

[az ad app permission add][az-ad-app-permission-add] コマンドを使用して、クライアント アプリケーションおよびサーバー アプリケーション コンポーネントが oAuth2 通信フローを使用するためのアクセス許可を追加します。 次に、[az ad app permission grant][az-ad-app-permission-grant] コマンドを使用して、サーバー アプリケーションと通信するためのアクセス許可をクライアント アプリケーションに付与します。

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>クラスターをデプロイする

2 つの Azure AD アプリケーションが作成されたので、今度は AKS クラスターそのものを作成します。 最初に、[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。 次の例では、*米国東部*リージョンにリソース グループを作成します。

クラスター用にリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

[az account show][az-account-show] コマンドを使用して Azure サブスクリプションのテナント ID を取得します。 次に、[az aks create][az-aks-create] コマンドを使用して AKS クラスターを作成します。 AKS クラスターを作成するコマンドは、サーバーとクライアント アプリケーションの ID、サーバー アプリケーション サービス プリンシパルのシークレット、およびテナント ID を提供します。

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

最後に、[az aks get-credentials][az-aks-get-credentials] コマンドを使用して、クラスター管理者資格情報を取得します。 次のいずれかのステップで、通常の *user* クラスターの資格情報を取得して、Azure AD 認証のフローの動作を確認します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>RBAC のバインドを作成する

Azure Active Directory アカウントを AKS クラスターで使用できるようにするには、その前にまず、ロールのバインドまたはクラスター ロールのバインドを作成する必要があります。 付与するアクセス許可を "*ロール*" によって定義し、それらを "*バインド*" によって目的のユーザーに適用します。 これらの割り当ては、特定の名前空間に適用することも、クラスター全体に適用することもできます。 詳細については、[RBAC 承認の使用][rbac-authorization]に関するページを参照してください。

[az ad signed-in-user show][az-ad-signed-in-user-show] コマンドを使用して、現在ログインしているユーザーのユーザー プリンシパル名 (UPN) を取得します。 このユーザー アカウントは、次のステップで Azure AD の統合に対して有効化されます。

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> RBAC のバインドを付与するユーザーが同じ Azure AD テナント内にいる場合、*userPrincipalName* に基づいてアクセス許可を割り当てます。 ユーザーが別の Azure AD テナント内にいる場合、代わりに *objectId* プロパティをクエリして使用します。

`basic-azure-ad-binding.yaml` という名前の YAML マニフェストを作成し、次の内容を貼り付けます。 最後の行で、*userPrincipalName_or_objectId* を前のコマンドで出力された UPN またはオブジェクト ID に置き換えます。

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

[kubectl apply][kubectl-apply] コマンドを使用して ClusterRoleBinding を作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Azure AD でクラスターにアクセスする

ここで、AKS クラスターの Azure AD 認証の統合をテストしてみましょう。 通常のユーザーの資格情報を使用するように `kubectl` 構成のコンテキストを設定します。 このコンテキストは、Azure AD 経由ですべての認証要求を返します。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

今度は、[kubectl get pods][kubectl-get] コマンドを使用して、すべての名前空間にわたってポッドを表示します。

```console
kubectl get pods --all-namespaces
```

Web ブラウザーを使用して、Azure AD の資格情報を使用して認証するためのサインイン プロンプトが表示されます。 正常に認証を行うと、次の出力例に示されているように、`kubectl` コマンドによって AKS クラスター内のポッドが表示されます。

```console
$ kubectl get pods --all-namespaces

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

`kubectl` 用に受信した認証トークンがキャッシュされます。 トークンの有効期限が切れたとき、または Kubernetes の構成ファイルが再作成されたときにのみ、サインインを再び求められます。

Web ブラウザーを使用して正常にサインインした後に、次の出力例のような承認エラー メッセージが表示される場合は、次の考えられる問題を確認してくださいます。

```console
error: You must be logged in to the server (Unauthorized)
```

* ユーザー アカウントが同じ Azure AD テナント内にあるかどうかに応じて、適切なオブジェクト ID または UPN を定義した。
* ユーザーが 200 を超えるグループのメンバーにはなっていない。
* サーバーのアプリケーション登録に定義されているシークレットが、`--aad-server-app-secret` を使用して構成された値と一致する

## <a name="next-steps"></a>次の手順

この記事で示されているコマンドを含む完全なスクリプトについては、[AKS サンプル リポジトリの Azure AD 統合スクリプト][complete-script]に関するページを参照してください。

Azure AD ユーザーとグループを使用してクラスター リソースへのアクセスを制御するには、[AKS でロールベースのアクセス制御と Azure AD の ID を使用してクラスター リソースへのアクセスを制限する][azure-ad-rbac]方法に関するページを参照してください。

Kubernetes クラスターをセキュリティで保護する方法の詳細については、[AKS でのアクセスと ID オプション][rbac-authorization]に関するページを参照してください。

ID とリソース管理に関するベスト プラクティスについては、[AKS での認証と承認のベスト プラクティス][operator-best-practices-identity]に関するページを参照してください。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
