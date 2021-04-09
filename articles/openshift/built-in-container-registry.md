---
title: Azure Red Hat OpenShift 4 の組み込みのコンテナー レジストリを構成する
description: Azure Red Hat OpenShift 4 の組み込みのコンテナー レジストリを構成する
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: dda050b8d824f0ff0ac1c84d2f008387de55aedf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636382"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4 の組み込みのコンテナー レジストリを構成する

Azure Red Hat OpenShift には [OpenShift Container Registry (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) と呼ばれる統合されたコンテナー イメージ レジストリが用意されています。これにより、必要に応じて新しいイメージ リポジトリを自動的にプロビジョニングする機能が追加されます。 これにより、結果のイメージをプッシュするためのアプリケーション ビルドの組み込みの場所がユーザーに提供されます。

この記事では、Azure Red Hat OpenShift (ARO) 4 クラスター用の組み込みのコンテナー イメージ レジストリを構成します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD の設定
> * OpenID Connect の設定
> * 組み込みのコンテナー イメージ レジストリへのアクセス

## <a name="before-you-begin"></a>開始する前に

この記事は、ARO クラスターが既に存在していることを前提としています。 ARO クラスターが必要な場合は、ARO チュートリアル「[Azure Red Hat OpenShift 4 クラスターを作成する](./tutorial-create-cluster.md)」を参照してください。 `az aro create` に対して `--pull-secret` 引数を指定してクラスターを作成する必要があります。  これは、Azure Active Directory 認証と組み込みコンテナー レジストリを構成するために必要です。

クラスターを作成したら、「[Azure Red Hat OpenShift 4 クラスターに接続する](./tutorial-connect-cluster.md)」の手順に従ってクラスターに接続します。
   * この記事の後半で `oc` コマンドを使用するため、「OpenShift CLI をインストールする」の手順に従ってください。
   * `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` のようなクラスター コンソールの URL をメモします。 `<random>` と `<region>` の値は、この記事の後半で使用します。
   * `kubeadmin` 資格情報をメモします。 これは、この記事の後半でまた使用します。

### <a name="configure-azure-active-directory-authentication"></a>Azure Active Directory 認証を構成する 

Azure Active Directory (Azure AD) は、OpenID Connect (OIDC) を実装しています。 OIDC により、Azure AD を使用して ARO クラスターにサインインできます。 [Azure Active Directory 認証の構成](configure-azure-ad-cli.md)の手順に従って、クラスターを設定します。

## <a name="access-the-built-in-container-image-registry"></a>組み込みのコンテナー イメージ レジストリへのアクセス

これで、ARO クラスターに認証方法を設定できました。次に、組み込みレジストリへのアクセスを有効にします。

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>管理者にする Azure AD ユーザーを定義する

1. Azure AD ユーザーの資格情報を使用して、ブラウザーから OpenShift Web コンソールにサインインします。 Azure Active Directory に対する OpenShift OpenID 認証を利用して、OpenID を使用して管理者を定義します。

   1. InPrivate、Incognito、またはその他の同等のブラウザー ウィンドウ機能を使用して、コンソールにサインインします。 このウィンドウは、OIDC が有効になった後、外観が変わります。
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="OpenID Connect が有効なサインイン ウィンドウ。":::
   1. **[openid]** を選択します。

   > [!NOTE]
   > ここでサインインに使用するユーザー名とパスワードをメモします。 このユーザー名とパスワードは、この記事および他の記事の他のアクションで管理者用に使用します。
2. 次の手順に従い、OpenShift CLI を使用してサインインします。  このプロセスは `oc login` と呼ばれています。
   1. Web コンソールの右上で、サインイン済みユーザーのコンテキスト メニューを展開し、 **[Copy Login Command]\(ログイン コマンドのコピー\)** を選択します。
   2. 必要に応じて、同じユーザーを使用して新しいタブ ウィンドウにサインインします。
   3. **[Display Token]\(トークンの表示\)** を選択します。
   4. こちらに示すように、 **[Login with this token]\(このトークンを使用してログイン\)** の下に表示されている値をクリップボードにコピーし、それをシェルで実行します。

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. コンソールで `oc whoami` を実行し、 **\<aad-user>** としての出力をメモします。  この値は、この記事の後の方で使用します。
4. OpenShift Web コンソールからサインアウトします。 ブラウザー ウィンドウの右上にある **\<aad-user>** というラベルのボタンを選択し、 **[ログアウト]** を選択します。


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>レジストリの操作に必要なロールを Azure AD ユーザーに付与する

1. `kubeadmin` の資格情報を使用して、ブラウザーから OpenShift Web コンソールにサインインします。
1. 上の `oc login` の手順に従って、`kubeadmin` のトークンを使用して OpenShift CLI にサインインします。ただし、これは、`kubeadmin` を使用して Web コンソールにサインインした後に実行します。
1. 次のコマンドを実行して、**aad-user** の組み込みレジストリにアクセスできるようにします。

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>コンテナー レジストリ URL を取得する

次に示すように、`oc get route` コマンドを使用してコンテナー レジストリの URL を取得します。

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > **コンテナー レジストリ URL** のコンソール出力をメモします。 これは、このガイドと以降のガイドで完全修飾レジストリ名として使用します。

## <a name="next-steps"></a>次のステップ

組み込みのコンテナー イメージ レジストリを設定したので、OpenShift でアプリケーションをデプロイすることから始めることができます。 Java アプリケーションの場合は、「[Azure Red Hat OpenShift 4 クラスターに Open Liberty/WebSphere Liberty を使用する Java アプリケーションをデプロイする](howto-deploy-java-liberty-app.md)」を参照してください。