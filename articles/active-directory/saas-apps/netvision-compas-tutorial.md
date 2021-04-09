---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Netvision Compas の統合 | Microsoft Docs
description: Azure Active Directory と Netvision Compas の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.openlocfilehash: 423ac882c81582f2843eeba37d11c660662ad6e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519403"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Netvision Compas の統合

このチュートリアルでは、Netvision Compas と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Netvision Compas を統合すると、次のことができます。

* Netvision Compas にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Netvision Compas に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Netvision Compas でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Netvision Compas では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Netvision Compas を構成したら、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。


## <a name="adding-netvision-compas-from-the-gallery"></a>ギャラリーからの Netvision Compas の追加

Azure AD への Netvision Compas の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Netvision Compas を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Netvision Compas**」と入力します。
1. 結果のパネルから **[Netvision Compas]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Netvision Compas の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Netvision Compas に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Netvision Compas の関連ユーザーとの間にリンク関係を確立する必要があります。

Netvision Compas で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Netvision Compas の SSO の構成](#configure-netvision-compas-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Netvision Compas のテスト ユーザーの構成](#configure-netvision-compas-test-user)** - Netvision Compas で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Netvision Compas** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<TENANT>.compas.cloud/Identity/Saml20` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Netvision Compas クライアント サポート チーム](mailto:contact@net.vision)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を見つけて **[ダウンロード]** を選択し、メタデータ ファイルをダウンロードしてコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)



### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Netvision Compas へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Netvision Compas]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-netvision-compas-sso"></a>Netvision Compas の SSO の構成

このセクションでは、**Netvision Compas** の SAML SSO を有効にします。
1. 管理者アカウントを使用して **Netvision Compas** にログインし、管理領域にアクセスします。

    ![管理領域](media/netvision-compas-tutorial/admin.png)

1. **[Syetem]\(システム\)** 領域を見つけて、 **[Identity Providers]\(ID プロバイダー\)** を選択します。

    ![IDP の管理](media/netvision-compas-tutorial/admin-idps.png)

1. Azure AD を新しい IDP として登録するには、**追加** アクションを選択します。

    ![IDP の追加](media/netvision-compas-tutorial/idps-add.png)

1. **[Provider type]\(プロバイダーの種類\)** で **[SAML]** を選択します。
1. **[Display name]\(表示名\)** と **[Description]\(説明\)** フィールドに、わかりやすい値を入力します。
1. **Netvision Compas** ユーザーを IDP に割り当てるには、 **[Available users]\(使用可能なユーザー\)** 一覧から選択し、 **[Add selected]\(選択項目の追加\)** ボタンを選択します。 プロビジョニングの手順に従って、ユーザーを IDP に割り当てることもできます。
1. **[Metadata]\(メタデータ\)** SAML オプションでは、 **[Choose File]\(ファイルの選択\)** ボタンをクリックし、前にコンピューターに保存したメタデータ ファイルを選択します。
1. **[保存]** をクリックします。

    ![IDP の編集](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Netvision Compas のテスト ユーザーの構成

このセクションでは、SSO に Azure AD を使用するように **Netvision Compas** の既存のユーザーを構成します。
1. 自社で定義している **Netvision Compas** ユーザー プロビジョニング手順に従います。または、既存のユーザー アカウントを編集します。
1. ユーザーのプロファイルを定義するときに、ユーザーの **[Email (Personal)]\(メール (個人用)\)** アドレスが Azure AD ユーザー名の username@companydomain.extension と一致するようにします。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。

    ![[Edit user]](media/netvision-compas-tutorial/user-config.png)

シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、Azure AD のシングル サインオン構成をテストします。

### <a name="using-the-access-panel-idp-initiated"></a>アクセス パネルを使用する (IDP 開始)

アクセス パネル上で [Netvision Compas] タイルをクリックすると、SSO を設定した Netvision Compas に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Netvision Compas に直接アクセスする (SP 開始)

1. **Netvision Compas** URL にアクセスします。 たとえば、「 `https://tenant.compas.cloud` 」のように入力します。
1. **Netvision Compas** ユーザー名を入力し、 **[Next]\(次へ\)** を選択します。

    ![ログイン ユーザー](media/netvision-compas-tutorial/login-user.png)

1. **(省略可能)** ユーザーが **Netvision Compas** 内の複数の IDP を割り当てられている場合は、使用可能な IDP の一覧が表示されます。 **Netvision Compas** で前に構成した Azure AD IDP を選択します。

    ![ログインの選択](media/netvision-compas-tutorial/login-choose.png)

1. 認証を実行するために Azure AD にリダイレクトされます。 正常に認証されると、SSO を設定した **Netvision Compas** に自動的にサインインします。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Netvision Compas を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)