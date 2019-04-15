---
title: Azure Active Directory と Azure Kubernetes Service を統合する
description: Azure Active Directory 対応の Azure Kubernetes Service (AKS) クラスターを作成する方法。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: c2ed053479b11bada4cfc0ec808ad148f024dee6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803250"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Active Directory と Azure Kubernetes Service を統合する

Azure Kubernetes Service (AKS) は、ユーザー認証に Azure Active Directory (AD) を使うように構成することができます。 この構成では、Azure Active Directory 認証トークンを使って AKS クラスターにログインできます。 さらに、クラスター管理者は、ユーザー ID またはディレクトリ グループのメンバーシップを基にして Kubernetes のロールベースのアクセス制御 (RBAC) を構成できます。

この記事では、AKS および Azure AD の前提条件をデプロイする方法、Azure AD 対応クラスターをデプロイする方法、および AKS クラスターで簡単な RBAC ロールを作成する方法について説明します。

次の制限事項が適用されます。

- Azure AD は、RBAC が有効なクラスターを新しく作成するときにのみ有効にできます。 既存の AKS クラスターで Azure AD を有効にすることはできません。
- 別のディレクトリからフェデレーション ログインを使用している場合など、Azure AD の "*ゲスト*" ユーザーはサポートされていません。

## <a name="authentication-details"></a>認証の詳細

Azure AD 認証は、OpenID Connect によって AKS クラスターに提供されます。 OpenID Connect は、OAuth 2.0 プロトコル上に構築された ID レイヤーです。 OpenID Connect の詳細については、[OpenID Connect のドキュメント][open-id-connect]を参照してください。

Kubernetes クラスターの内部からは、webhook トークン認証を使って認証トークンが確認されます。 webhook トークン認証は、AKS クラスターの一部として構成および管理されます。 Webhook トークン認証の詳細については、[Webhook 認証のドキュメント][kubernetes-webhook]を参照してください。

> [!NOTE]
> AKS 認証用に Azure AD を構成すると、2 つの Azure AD アプリケーションが構成されます。 この操作は、Azure テナント管理者が行う必要があります。

## <a name="create-server-application"></a>サーバー アプリケーションを作成する

最初の Azure AD アプリケーションは、ユーザーの Azure AD グループ メンバーシップを取得するために使われます。

1. **[Azure Active Directory]** > **[アプリの登録]** > **[新しいアプリケーションの登録]** を選択します。

   アプリケーションの名前を指定し、アプリケーションの種類として **[Web アプリ/API]** を選び、**[サインオン URL]** に URI 形式の値を入力します。 完了したら **[作成]** を選択します。

   ![Azure AD の登録を作成する](media/aad-integration/app-registration.png)

2. **[マニフェスト]** を選び、`groupMembershipClaims` の値を `"All"` に編集します。

   更新が終わったら保存します。

   ![グループ メンバーシップを全部に更新する](media/aad-integration/edit-manifest.png)

3. Azure AD アプリケーションに戻り、**[設定]** > **[キー]** を選びます。

   キーの説明を追加し、有効期限を選んで、**[保存]** を選びます。 キーの値を書き留めておきます。 Azure AD が有効な AKS クラスターを展開するときに、`Server application secret` としてこの値を参照します。

   ![アプリケーションの秘密キーを取得する](media/aad-integration/application-key.png)

4. Azure AD アプリケーションに戻り、**[設定]** > **[必要なアクセス許可]** > **[追加]** > **[API を選択します]** > **[Microsoft Graph]** > **[選択]** の順に選びます。

   ![Graph API を選択する](media/aad-integration/graph-api.png)

5. **[アプリケーションのアクセス許可]** で、**[ディレクトリ データの読み取り]** をオンにします。

   ![アプリケーションの Graph のアクセス許可を設定する](media/aad-integration/read-directory.png)

6. **[委任されたアクセス許可]** で、**[サインインとユーザー プロファイルの読み取り]** と **[ディレクトリ データの読み取り]** をオンにします。 終わったら保存します。

   ![アプリケーションの Graph のアクセス許可を設定する](media/aad-integration/delegated-permissions.png)

   **[完了]** を選択します。

7. API のリストから *[Microsoft Graph]* を選択してから、**[アクセス許可の付与]** を選択します。 現在のアカウントがテナント管理者ではない場合、このステップは失敗します。

   ![アプリケーションの Graph のアクセス許可を設定する](media/aad-integration/grant-permissions.png)

   アクセス許可が正常に付与されると、ポータルに次の通知が表示されます。

   ![アクセス許可が正常に付与されたことを示す通知](media/aad-integration/permissions-granted.png)

8. アプリケーションに戻り、**[アプリケーション ID]** を書き留めます。 Azure AD が有効な AKS クラスターを展開するときに、`Server application ID` としてこの値を参照します。

   ![アプリケーション ID を取得する](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>クライアント アプリケーションを作成する

もう 1 つの Azure AD アプリケーションは、Kubernetes CLI (kubectl) でログインするときに使われます。

1. **[Azure Active Directory]** > **[アプリの登録]** > **[新しいアプリケーションの登録]** を選択します。

   アプリケーションの名前を指定し、アプリケーションの種類として **[ネイティブ]** を選び、**[リダイレクト URI]** に URI 形式の値を入力します。 完了したら **[作成]** を選択します。

   ![AAD の登録を作成する](media/aad-integration/app-registration-client.png)

2. Azure AD アプリケーションから **[設定]** > **[必要なアクセス許可]** > **[追加]** > **[API を選択します]** の順に選び、このドキュメントの前のステップで作成したサーバー アプリケーションの名前を検索します。

   ![アプリケーションのアクセス許可を構成する](media/aad-integration/select-api.png)

3. アプリケーションの横にチェック マークを付けて、**[選択]** をクリックします。

   ![AKS AAD サーバー アプリケーション エンドポイントを選択する](media/aad-integration/select-server-app.png)

   **[完了]** を選択します

4. 一覧からサーバー API を選択し、**[アクセス許可の付与]** を選択します。

   ![アクセス許可を付与する](media/aad-integration/grant-permissions-client.png)

5. AD アプリケーションに戻り、**[アプリケーション ID]** を書き留めます。 Azure AD が有効な AKS クラスターを展開するときに、`Client application ID` としてこの値を参照します。

   ![アプリケーション ID を取得する](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>テナント ID を取得する

最後に、Azure テナントの ID を取得します。 この値は、AKS クラスターを展開するときにも使われます。

Azure portal から、**[Azure Active Directory]** > **[プロパティ]** の順に選び、**[ディレクトリ ID]** を書き留めます。 Azure AD が有効な AKS クラスターを展開するときに、`Tenant ID` としてこの値を参照します。

![Azure テナント ID を取得する](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>クラスターを展開する

AKS クラスターのリソース グループを作成するには、[az group create][az-group-create] コマンドを使います。

```azurecli
az group create --name myResourceGroup --location eastus
```

[az aks create][az-aks-create] コマンドを使って、クラスターを展開します。 以下のサンプル コマンドの値を、Azure AD アプリケーションを作成するときに収集した値に置き換えます。

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>RBAC のバインドを作成する

Azure Active Directory アカウントを AKS クラスターで使用できるようにするには、その前にまず、ロールのバインドまたはクラスター ロールのバインドを作成する必要があります。 付与するアクセス許可を "*ロール*" によって定義し、それらを "*バインド*" によって目的のユーザーに適用します。 これらの割り当ては、特定の名前空間に適用することも、クラスター全体に適用することもできます。 詳細については、「[Using RBAC authorization (RBAC 認可の使用)][rbac-authorization]」を参照してください。

最初に、[az aks get-credentials][az-aks-get-credentials] コマンドと `--admin` 引数を使って、管理者アクセス権でクラスターにログインします。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

次に、以下のマニフェストを使って、Azure AD アカウントの ClusterRoleBinding を作成します。 この例では、クラスターのすべての名前空間へのフル アクセスをアカウントに付与します。 

[az ad user show][az-ad-user-show] コマンドを使用して、必要なユーザー アカウントの *objectId* を取得します。 必要なアカウントのユーザー プリンシパル名 (UPN) を指定してください。

```azurecli-interactive
az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
```

*rbac-aad-user.yaml* のようなファイルを作成し、次の内容を貼り付けます。 前の手順で Azure AD から取得したユーザー アカウントのオブジェクト ID でユーザー名を更新します。

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
  name: "947026ec-9463-4193-c08d-4c516e1f9f52"
```

次の例に示すように [kubectl apply][kubectl-apply] コマンドを使用してバインドを適用します:

```console
kubectl apply -f rbac-aad-user.yaml
```

Azure AD グループのすべてのメンバーに対するロール バインドを作成することもできます。 Azure AD グループは、次の例に示すように、グループ オブジェクト ID を使用して指定します。 *rbac-aad-group.yaml* のようなファイルを作成し、次の内容を貼り付けます。 グループ オブジェクト ID は、Azure AD テナントのもので更新します:

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

次の例に示すように [kubectl apply][kubectl-apply] コマンドを使用してバインドを適用します:

```console
kubectl apply -f rbac-aad-group.yaml
```

RBAC を使って Kubernetes クラスターをセキュリティで保護する方法について詳しくは、「[Using RBAC Authorization][rbac-authorization]」(RBAC の承認の使用) をご覧ください。

## <a name="access-cluster-with-azure-ad"></a>Azure AD でクラスターにアクセスする

次に、[az aks get-credentials][az-aks-get-credentials] コマンドを使って、管理者以外のユーザーのコンテキストを取得します。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

いずれかの kubectl コマンドを実行すると、Azure による認証を求められます。 画面の指示に従います。

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

完了すると、認証トークンがキャッシュされます。 トークンの有効期限が切れたとき、または Kubernetes の構成ファイルが再作成されたときにのみ、ログインを再び求められます。

正常にサインインした後に承認エラー メッセージが表示される場合は、以下に該当するかどうかをチェックしてください。
1. サインインしようとしているユーザーが、その Azure AD インスタンスで Guest ではない (これに該当することが多いのは、異なるディレクトリからのフェデレーション ログインを使用している場合です)。
2. ユーザーが 200 を超えるグループのメンバーにはなっていない。

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>次の手順

「[Using RBAC Authorization][rbac-authorization]」(RBAC の承認の使用) ドキュメントで、RBAC を使った Kubernetes クラスターのセキュリティ保護についてさらに学習してください。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
