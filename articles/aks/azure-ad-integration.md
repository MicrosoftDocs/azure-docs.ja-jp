---
title: Azure Active Directory と Azure Kubernetes Service を統合する
description: Azure Active Directory 対応の Azure Kubernetes Service (AKS) クラスターを作成する方法
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 80137023643630e8472a70fcca6cb656aeba7123
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616383"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Active Directory と Azure Kubernetes Service を統合する

Azure Kubernetes Service (AKS) を、ユーザー認証に Azure Active Directory (Azure AD) を使うように構成することができます。 この構成では、Azure AD 認証トークンを使って AKS クラスターにサインインできます。

クラスター管理者は、ユーザーの ID またはディレクトリ グループのメンバーシップに基づいて、Kubernetes のロールベースのアクセス制御 (RBAC) を構成できます。

この記事では、次の方法について説明します。

- AKS と Azure AD の前提条件をデプロイする。
- Azure AD 対応のクラスターをデプロイする。
- Azure portal を使って AKS クラスターに基本的な RBAC ロールを作成する。

これらの手順は、[Azure CLI][azure-ad-cli] を使って行うこともできます。

> [!NOTE]
> Azure AD は、RBAC が有効なクラスターを新しく作成するときにのみ有効にできます。 既存の AKS クラスターで Azure AD を有効にすることはできません。

## <a name="authentication-details"></a>認証の詳細

Azure AD 認証は、OpenID Connect のある AKS クラスターに対して提供されます。 OpenID Connect は、OAuth 2.0 プロトコル上に構築された ID レイヤーです。

OpenID Connect について詳しくは、「[OpenID Connect と Azure Active Directory を使用する Web アプリケーションへのアクセスの承認][open-id-connect]」をご覧ください。

Kubernetes クラスターの内部では、Webhook トークン認証が認証トークンに使用されます。 webhook トークン認証は、AKS クラスターの一部として構成および管理されます。

Webhook トークン認証について詳しくは、Kubernetes のドキュメントの「[Webhook Token Authentication (Webhook トークン認証)][kubernetes-webhook]」セクションをご覧ください。

AKS クラスターに対して Azure AD 認証を提供するため、2 つの Azure AD アプリケーションが作成されます。 1 つのアプリケーションは、ユーザー認証を提供するサーバー コンポーネントです。 もう 1 つのアプリケーションは、CLI によってユーザーに認証が要求されるときに使用されるクライアント コンポーネントです。 このクライアント アプリケーションでは、クライアントによって提供された資格情報の実際の認証には、サーバー アプリケーションが使われます。

> [!NOTE]
> AKS 認証用に Azure AD を構成すると、2 つの Azure AD アプリケーションが構成されます。 Azure テナント管理者は、各アプリケーションのアクセス許可を委任する手順を完了する必要があります。

## <a name="create-the-server-application"></a>サーバー アプリケーションを作成する

最初の Azure AD アプリケーションは、ユーザーの Azure AD グループ メンバーシップを取得するために適用されます。 Azure portal でこのアプリケーションを作成するには:

1. **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** の順に選択します。

    a. アプリケーションに名前を付けます (*AKSAzureADServer* など)。

    b. **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
    
    c. リダイレクト URI の種類で **[Web]** を選択し、 *https://aksazureadserver* のような形式の URI 値を入力します。

    d. 完了したら、 **[登録]** を選択します。

2. **[マニフェスト]** を選択し、**groupMembershipClaims:** の値を **All** と編集します。 更新が終了したら、 **[保存]** を選択します。

    ![グループ メンバーシップを全部に更新する](media/aad-integration/edit-manifest.png)

3. Azure AD アプリケーションの左側のウィンドウで、 **[証明書とシークレット]** を選択します。

    a. **[+ 新しいクライアント シークレット]** を選択します。

    b. キーの説明を追加します (例: "*AKS Azure AD サーバー*")。 有効期限を選択し、 **[追加]** を選択します。

    c. キーの値を記録しておきます。この時点でしか表示されません。 Azure AD が有効な AKS クラスターをデプロイするときは、この値はサーバー アプリケーション シークレットと呼ばれます。

4. Azure AD アプリケーションの左側のウィンドウで、 **[API のアクセス許可]** を選択し、 **[+ アクセス許可の追加]** を選択します。

    a. **[Microsoft API]** で、 **[Microsoft Graph]** を選択します。

    b. **[委任されたアクセス許可]** を選択し、 **[ディレクトリ] > [Directory.Read.All (ディレクトリ データの読み取り)]** のチェック ボックスをオンにします。

    c. **[ユーザー] > [User.Read (サインインとユーザー プロファイルの読み取り)]** に対する既定の委任されたアクセス許可が存在しない場合は、そのチェック ボックスをオンにします。

    d. **[アプリケーションの許可]** を選択し、 **[ディレクトリ] > [Directory.Read.All (ディレクトリ データの読み取り)]** のチェック ボックスをオンにします。

    ![Graph のアクセス許可を設定する](media/aad-integration/graph-permissions.png)

    e. **[アクセス許可の追加]** を選択して、更新を保存します。

    f. **[同意する]** で、 **[管理者の同意を与えます]** を選択します。 現在のアカウントがテナント管理者ではない場合、このボタンは使用できます。

    アクセス許可が正常に付与されると、ポータルに次の通知が表示されます。

   ![アクセス許可が正常に付与されたことを示す通知](media/aad-integration/permissions-granted.png)

5. Azure AD アプリケーションの左側のウィンドウで、 **[API の公開]** を選択し、 **[+ Scope の追加]** を選択します。
    
    a. **[スコープ名]** 、 **[管理者の同意の表示名]** 、 **[管理者の同意の説明]** を入力します (*AKSAzureADServer* など)。

    b. **[状態]** が **[有効]** に設定されていることを確認します。

    ![他のサービスで使用するために API としてサーバー アプリを公開する](media/aad-integration/expose-api.png)

    c. **[スコープの追加]** を選択します。

6. アプリケーションの **[概要]** ページに戻り、 **[アプリケーション (クライアント) ID]** を記録しておきます。 Azure AD が有効な AKS クラスターをデプロイするときは、この値はサーバー アプリケーション ID と呼ばれます。

    ![アプリケーション ID を取得する](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>クライアント アプリケーションを作成する

もう 1 つの Azure AD アプリケーションは、Kubernetes CLI (kubectl) でサインインするときに使われます。

1. **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** の順に選択します。

    a. アプリケーションに名前を付けます (*AKSAzureADClient* など)。

    b. **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。

    c. リダイレクト URI の種類で **[Web]** を選択し、 *https://aksazureadclient* のような形式の URI 値を入力します。

    d. 完了したら、 **[登録]** を選択します。

2. Azure AD アプリケーションの左側のウィンドウで、 **[API のアクセス許可]** を選択し、 **[+ アクセス許可の追加]** を選択します。

    a. **[自分の API]** を選択した後、前のステップで作成した Azure AD サーバー アプリケーションを選択します (*AKSAzureADServer* など)。

    b. **[委任されたアクセス許可]** を選択した後、Azure AD サーバー アプリの横にあるチェック ボックスをオンにします。

    ![アプリケーションのアクセス許可を構成する](media/aad-integration/select-api.png)

    c. **[アクセス許可の追加]** を選択します.

    d. **[同意する]** で、 **[管理者の同意を与えます]** を選択します。 現在のアカウントがテナント管理者ではない場合、このボタンは使用できません。アクセス許可が付与されると、ポータルに次の通知が表示されます。

    ![アクセス許可が正常に付与されたことを示す通知](media/aad-integration/permissions-granted.png)

3. Azure AD アプリケーションの左側のウィンドウで、 **[認証]** を選択します。

    - **[既定のクライアントの種類]** で、 **[Treat the client as a public client]\(クライアントをパブリック クライアントとして扱う\)** に対して **[はい]** を選択します。

5. Azure AD アプリケーションの左側のウィンドウで、アプリケーション ID を記録しておきます。 Azure AD が有効な AKS クラスターをデプロイするときは、この値はクライアント アプリケーション ID と呼ばれます。

   ![アプリケーション ID を取得する](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>テナント ID を取得する

次に、Azure テナントの ID を取得します。 AKS クラスターを作成するときにこの値を使います。

Azure portal から、 **[Azure Active Directory]**  >  **[プロパティ]** の順に選択し、 **[ディレクトリ ID]** を記録しておきます。 Azure AD が有効な AKS クラスターを作成するとき、この値はテナント ID と呼ばれます。

![Azure テナント ID を取得する](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>AKS クラスターをデプロイする

AKS クラスターのリソース グループを作成するには、[az group create][az-group-create] コマンドを使います。

```azurecli
az group create --name myResourceGroup --location eastus
```

AKS クラスターをデプロイするには、[az aks create][az-aks-create] コマンドを使います。 次に、以下のサンプル コマンドで値を置き換えます。 Azure AD アプリケーションを作成するときに収集した、サーバー アプリ ID、アプリ シークレット、クライアント アプリ ID、テナント ID の値を使います。

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

AKS クラスターの作成には数分かかります。

## <a name="create-an-rbac-binding"></a>RBAC のバインドを作成する

Azure Active Directory アカウントを AKS クラスターで使う前に、ロールのバインドまたはクラスター ロールのバインドを作成する必要があります。 付与するアクセス許可をロールによって定義し、それらをバインドによって目的のユーザーに適用します。 これらの割り当ては、特定の名前空間に適用することも、クラスター全体に適用することもできます。 詳細については、[RBAC 承認の使用][rbac-authorization]に関するページを参照してください。

最初に、[az aks get-credentials][az-aks-get-credentials] コマンドと `--admin` 引数を使って、管理者アクセス権でクラスターにサインインします。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

次に、AKS クラスターへのアクセス権を付与したい Azure AD アカウントに対して ClusterRoleBinding を作成します。 次の例では、クラスター内のすべての名前空間へのフル アクセスをアカウントに付与します。

- RBAC のバインドを付与するユーザーが同じ Azure AD テナント内にいる場合、ユーザー プリンシパル名 (UPN) に基づいてアクセス許可を割り当てます。 ClusterRoleBinding に対する YAML マニフェストを作成するステップに移動します。

- ユーザーが別の Azure AD テナント内にいる場合、代わりに **objectId** プロパティをクエリして使用します。 必要に応じて、[az ad user show][az-ad-user-show] コマンドを使って、必要なユーザー アカウントの objectId を取得します。 必要なアカウントのユーザー プリンシパル名 (UPN) を指定してください。

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

*rbac-aad-user.yaml* のようなファイルを作成し、次の内容を貼り付けます。 最後の行で、**userPrincipalName_or_objectId** を UPN またはオブジェクト ID に置き換えます。 どちらを選ぶかは、ユーザーが同じ Azure AD テナントかどうかによって異なります。

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

次の例に示すように [kubectl apply][kubectl-apply] コマンドを使ってバインドを適用します。

```console
kubectl apply -f rbac-aad-user.yaml
```

Azure AD グループのすべてのメンバーに対するロール バインドを作成することもできます。 Azure AD グループは、次の例に示すように、グループ オブジェクト ID を使用して指定します。

*rbac-aad-group.yaml* のようなファイルを作成し、次の内容を貼り付けます。 グループ オブジェクト ID は、Azure AD テナントのもので更新します:

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

次の例に示すように [kubectl apply][kubectl-apply] コマンドを使ってバインドを適用します。

```console
kubectl apply -f rbac-aad-group.yaml
```

RBAC を使って Kubernetes クラスターをセキュリティで保護する方法について詳しくは、「[Using RBAC Authorization (RBAC の承認の使用)][rbac-authorization]」をご覧ください。

## <a name="access-the-cluster-with-azure-ad"></a>Azure AD でクラスターにアクセスする

[az aks get-credentials][az-aks-get-credentials] コマンドを使って、管理者以外のユーザーのコンテキストを取得します。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

`kubectl` コマンドを実行した後、Azure を使って認証することを求められます。 次の例に示すように、画面の指示に従ってプロセスを完了します。

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

プロセスが終了すると、認証トークンがキャッシュされます。 トークンの有効期限が切れたとき、または Kubernetes の構成ファイルが再作成されたときにのみ、再度サインインするよう求められます。

正常にサインインした後で承認エラー メッセージが表示される場合は、次の条件を確認します。

```console
error: You must be logged in to the server (Unauthorized)
```


- ユーザー アカウントが同じ Azure AD テナント内にあるかどうかに応じて、適切なオブジェクト ID または UPN を定義した。
- ユーザーが 200 を超えるグループのメンバーにはなっていない。
- サーバーのアプリケーション登録に定義されているシークレットが、`--aad-server-app-secret` を使用して構成された値と一致する。

## <a name="next-steps"></a>次の手順

Azure AD ユーザーとグループを使用してクラスター リソースへのアクセスを制御するには、[AKS でロールベースのアクセス制御と Azure AD の ID を使用してクラスター リソースへのアクセスを制限する][azure-ad-rbac]方法に関するページを参照してください。

Kubernetes クラスターをセキュリティで保護する方法について詳しくは、「[Azure Kubernetes Service (AKS) でのアクセスと ID オプション][rbac-authorization]」をご覧ください。

ID とリソース管理の詳細については、「[Azure Kubernetes Service (AKS) の認証と認可のベスト プラクティス][operator-best-practices-identity]」をご覧ください。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
