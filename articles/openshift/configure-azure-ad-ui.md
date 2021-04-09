---
title: OpenShift 4 を実行する Azure Red Hat OpenShift - Azure portal および OpenShift Web コンソールを使用して Azure Active Directory 認証を構成する
description: Azure portal および OpenShift Web コンソールを使用して、OpenShift 4 を実行している Azure Red Hat OpenShift クラスターの Azure Active Directory 認証を構成する方法について説明します
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: eda45b1a1a011a646915cf45e45218ae168a2af6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213081"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Azure Red Hat OpenShift 4 クラスターの Azure Active Directory 認証を構成する (ポータル)

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.6.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

クラスターの **OAuth コールバック URL** を作成し、メモしておきます。 **aro-rg** を実際のリソース グループの名前に、**aro-cluster** を実際のクラスター名に置き換えてください。

> [!NOTE]
> OAuth コールバック URL の `AAD` セクションは、後でセットアップする OAuth ID プロバイダー名と一致している必要があります。

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>認証用の Azure Active Directory アプリケーションを作成する

Azure portal にログインし、[[アプリの登録]](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ブレードに移動します。次に、 **[新規登録]** をクリックして新しいアプリケーションを作成します。

アプリケーションの名前 (**aro-azuread-auth** など) を指定し、上記の手順で取得した OAuth コールバック URL の値を使用して **リダイレクト URI** を入力します。

![[新しいアプリケーションの登録]](media/aro4-ad-registerapp.png)

**[証明書とシークレット]** に移動し、 **[新しいクライアント シークレット]** をクリックして詳細を入力します。 後の段階で使用するので、キーの値をメモしておきます。 これは、再度取得することはできません。

![シークレットの作成](media/aro4-ad-clientsecret.png)

**[概要]** に移動し、**アプリケーション (クライアント) ID** と **ディレクトリ (テナント) ID** をメモしておきます。 この値は後の段階で必要となります。

![アプリケーション (クライアント) ID とディレクトリ (テナント) ID を取得する](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>省略可能な要求の構成

アプリケーション開発者は、Azure AD アプリケーションで[省略可能な要求](../active-directory/develop/active-directory-optional-claims.md)を使用して、アプリケーションに送信されるトークンに含める要求を指定できます。

次の処理に省略可能な要求を使用できます。

* アプリケーションのトークンに含める追加の要求を選択する。
* Azure AD からトークンで返される特定の要求の動作を変更する。
* アプリケーションのカスタムの要求を追加してアクセスする。

Azure Active Directory によって返される ID トークンの一部として `upn` を追加することで、OpenShift で `email` 要求を使用し、`upn` にフォールバックして推薦ユーザー名を設定するよう構成します。

**トークン構成 (プレビュー)** に移動し、 **[Add optional claim]\(省略可能な要求を追加\)** をクリックします。 **[ID]** を選択し、 **[電子メール]** と **upn** 要求を確認します。

![追加された電子メールと UPN 要求を示すスクリーンショット。](media/aro4-ad-tokens.png)

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>ユーザーとグループをクラスターに割り当てる (省略可能)

Azure Active Directory (Azure AD) テナントに登録されたアプリケーションは、既定ではテナントの正常に認証されたすべてのユーザーが利用できます。 Azure AD により、テナントの管理者と開発者が、テナントのユーザーまたはセキュリティ グループの特定のセットにアプリを制限できるようになります。

Azure Active Directory のドキュメントに記載されている手順に従って、[ユーザーとグループをアプリに割り当てます](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration)。

## <a name="configure-openshift-openid-authentication"></a>OpenShift OpenID 認証の構成

`kubeadmin` の資格情報を取得します。 次のコマンドを実行して、`kubeadmin` ユーザーのパスワードを検索します。

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

次の出力例は、パスワードが `kubeadminPassword` に含まれることを示しています。

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

次のコマンドを実行すると、クラスター コンソールの URL を調べることができます。これは、`https://console-openshift-console.apps.<random>.<region>.aroapp.io/` のようになります

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

ブラウザーでコンソールの URL にアクセスし、`kubeadmin` の資格情報を使用してログインします。

**[管理]** に移動し、 **[クラスター設定]** をクリックして、 **[グローバル構成]** タブを選択します。スクロールして **[OAuth]** を選択します。

下にスクロールして **[ID プロバイダー]** の下の **[追加]** を選択し **[OpenID Connect]** を選択します。
![[ID プロバイダー] ドロップダウンから [OpenID Connect] を選択します](media/aro4-oauth-idpdrop.png)

名前に **[AAD]** と入力し、 **[クライアント ID]** に **[アプリケーション ID]** と **[クライアント シークレット]** を入力します。 **[発行者の URL]** は、`https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` のように書式設定されます。 プレースホルダーを、上記の手順で取得したテナント ID に置き換えます。

![OAuth の詳細を入力する](media/aro4-oauth-idp-1.png)

下にスクロールして **[要求]** セクションに移動し、 **[推奨ユーザー名]** を **upn** の値で更新します。

![要求の詳細を入力する](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Azure Active Directory を使用したログインの検証

OpenShift Web コンソールをログアウトしてもう一度ログインしようとすると、**AAD** でログインするための新しいオプションが表示されます。 数分待つ必要がある場合があります。

![Azure Active Directory オプションを使用したログイン画面](media/aro4-login-2.png)
