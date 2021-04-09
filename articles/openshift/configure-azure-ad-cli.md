---
title: OpenShift 4 を実行する Azure Red Hat OpenShift - コマンド ラインを使用して Azure Active Directory 認証を構成する
description: コマンド ラインを使用して OpenShift 4 を実行している Azure Red Hat OpenShift クラスターの Azure Active Directory 認証を構成する方法について説明します
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 06f7bfea9a88627733eb9ce9166e05d05790e23a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213064"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Azure Red Hat OpenShift 4 クラスターの Azure Active Directory 認証を構成する (CLI)

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.6.0 以降を実行していることが要件となります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

Azure Active Directory アプリケーションの構成に使用するクラスター固有の URL を取得します。

リソース グループとクラスター名の変数を設定します。

**\<resource_group>** をリソース グループの名前に置き換え、 **\<aro_cluster>** をクラスターの名前に置き換えます。

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

クラスターの OAuth コールバック URL を作成し、変数 **oauthCallbackURL** に格納します。 

> [!NOTE]
> OAuth コールバック URL の `AAD` セクションは、後でセットアップする OAuth ID プロバイダー名と一致している必要があります。


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

oauthCallbackURL の形式は、カスタム ドメインでは少し異なります。

* カスタム ドメイン (`contoso.com` など) を使用している場合は、次のコマンドを実行します。 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* カスタムドメインを使用していない場合、`$domain` は 8 文字の英数字文字列になり、`$location.aroapp.io` によって拡張されます。

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> OAuth コールバック URL の `AAD` セクションは、後でセットアップする OAuth ID プロバイダー名と一致している必要があります。

## <a name="create-an-azure-active-directory-application-for-authentication"></a>認証用の Azure Active Directory アプリケーションを作成する

**\<client_secret>** をセキュリティで保護されたアプリケーション用パスワードに置き換えます。

```azurecli-interactive
client_secret=<client_secret>
```

Azure Active Directory アプリケーションを作成し、作成したアプリケーション識別子を取得します。

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

アプリケーションが含まれるサブスクリプションのテナント ID を取得します。

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>マニフェスト ファイルを作成して、ID トークンに含める省略可能な要求を定義する

アプリケーション開発者は、Azure AD アプリケーションで[省略可能な要求](../active-directory/develop/active-directory-optional-claims.md)を使用して、アプリケーションに送信されるトークンに含める要求を指定できます。

次の処理に省略可能な要求を使用できます。

- アプリケーションのトークンに含める追加の要求を選択する。
- Azure AD からトークンで返される特定の要求の動作を変更する。
- アプリケーションのカスタムの要求を追加してアクセスする。

Azure Active Directory によって返される ID トークンの一部として `upn` を追加することで、OpenShift で `email` 要求を使用し、`upn` にフォールバックして推薦ユーザー名を設定するよう構成します。

**manifest.json** ファイルを作成して、Azure Active Directory アプリケーションを構成します。

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Azure Active Directory アプリケーションの optionalClaims をマニフェストで更新する

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Azure Active Directory アプリケーション スコープのアクセス許可を更新する

Azure Active Directory からユーザー情報を読み取れるようにするには、適切なスコープを定義する必要があります。

**Azure Active Directory Graph.User.Read** スコープでサインインとユーザー プロファイルの読み取りを有効にする権限を追加します。

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> この Azure Active Directory のグローバル管理者として認証されていない限り、同意を与えるためのメッセージは無視することができます。 標準ドメイン ユーザーは、AAD 資格情報を使用してクラスターに初めてログインするときに、同意を付与するように求められます。

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>ユーザーとグループをクラスターに割り当てる (省略可能)

Azure Active Directory (Azure AD) テナントに登録されたアプリケーションは、既定ではテナントの正常に認証されたすべてのユーザーが利用できます。 Azure AD により、テナントの管理者と開発者が、テナントのユーザーまたはセキュリティ グループの特定のセットにアプリを制限できるようになります。

Azure Active Directory のドキュメントに記載されている手順に従って、[ユーザーとグループをアプリに割り当てます](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration)。

## <a name="configure-openshift-openid-authentication"></a>OpenShift OpenID 認証の構成

`kubeadmin` の資格情報を取得します。 次のコマンドを実行して、`kubeadmin` ユーザーのパスワードを検索します。

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

次のコマンドを使用して、OpenShift クラスターの API サーバーにログインします。 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

Azure Active Directory アプリケーション シークレットを格納するための OpenShift シークレットを作成します。

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

**oidc.yaml** ファイルを作成し、Azure Active Directory に対して OpenShift OpenID 認証を構成します。 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

構成をクラスターに適用します。

```azurecli-interactive
oc apply -f oidc.yaml
```

次のような応答が返されます。

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Azure Active Directory を使用したログインの検証

OpenShift Web コンソールをログアウトしてもう一度ログインしようとすると、**AAD** でログインするための新しいオプションが表示されます。 数分待つ必要がある場合があります。

![Azure Active Directory オプションを使用したログイン画面](media/aro4-login-2.png)
