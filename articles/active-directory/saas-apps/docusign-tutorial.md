---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と DocuSign の統合 | Microsoft Docs
description: Azure Active Directory と DocuSign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe1f018612e889f49993895d88cdaf9ad732b393
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306307"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と DocuSign の統合

このチュートリアルでは、DocuSign と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と DocuSign を統合すると、次のことができます。

* DocuSign にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して DocuSign に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* DocuSign でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* DocuSign では、**SP** Initiated SSO がサポートされます

* DocuSign では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

* DocuSign では、[自動ユーザー プロビジョニング](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial)がサポートされます

## <a name="adding-docusign-from-the-gallery"></a>ギャラリーからの DocuSign の追加

Azure AD への DocuSign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に DocuSign を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**DocuSign**」と入力します。
1. 結果のパネルから **[DocuSign]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>DocuSign の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、DocuSign に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと DocuSign の関連ユーザーとの間にリンク関係を確立する必要があります。

DocuSign で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[DocuSign の SSO の構成](#configure-docusign-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[DocuSign のテスト ユーザーの作成](#create-docusign-test-user)** - DocuSign で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **DocuSign** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子に値を置き換えます。実際の値については、このチュートリアルの **[View SAML 2.0 Endpoints]\(SAML 2.0 エンドポイントの表示\)** に関するセクションで説明します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[DocuSign のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

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

このセクションでは、B.Simon に DocuSign へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[DocuSign]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-docusign-sso"></a>DocuSign の SSO の構成

1. DocuSign 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Setup DocuSign]\(DocuSign の設定\)** をクリックすると、DocuSign アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して DocuSign にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. DocuSign を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として DocuSign 企業サイトにサインインして、次の手順を実行します。

4. ページの右上にあるプロファイル **ロゴ**をクリックし、 **[Go to Admin]\(管理に移動\)** をクリックします。
  
    ![シングル サインオンの構成][51]

5. ドメインのソリューション ページで **[Domains]\(ドメイン\)** をクリックします。

    ![シングル サインオンの構成][50]

6. **[Domains]\(ドメイン\)** セクションの **[CLAIM DOMAIN]\(ドメインの要求\)** をクリックします。

    ![シングル サインオンの構成][52]

7. **[Claim a domain]\(ドメインの要求\)** ダイアログの **[Domain Name]\(ドメイン名\)** ボックスに、所属する会社のドメインを入力してから、 **[CLAIM]\(要求\)** をクリックします。 ドメインを確認し、状態がアクティブであることを確かめてください。

    ![シングル サインオンの構成][53]

8. ドメインのソリューション ページで **[Identity Providers]\(ID プロバイダー\)** をクリックします。
  
    ![シングル サインオンの構成][54]

9. **[Identity Provider]\(ID プロバイダー\)** セクションで、 **[ADD IDENTITY PROVIDER]\(ID プロバイダー\)** をクリックします。 

    ![シングル サインオンの構成][55]

10. **[Identity Provider Settings (ID プロバイダーの設定)]** ページで、次の手順を実行します。

    ![シングル サインオンの構成][56]

    a. **[Name (名前)]** ボックスに、構成の一意の名前を入力します。 スペースは使用しないでください。

    b. **[ID プロバイダーの発行者]** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c. **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[Identity Provider Logout URL]\(ID プロバイダーのログアウト URL\)** テキスト ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    e. **[Sign AuthN request]\(AuthN 要求に署名する\)** を選択します。

    f. **[Send AuthN request by (認証要求の送信方法)]** として、 **[POST]** を選択します。

    g. **[Send logout request by]\(ログアウト要求の送信方法\)** として、 **[GET]** を選択します。

    h. **[Custom Attribute Mapping]\(カスタム属性のマッピング\)** セクションの **[ADD NEW MAPPING]\(新しいマッピングの追加\)** をクリックします。

    ![シングル サインオンの構成][62]

    i. Azure AD の要求とマッピングするフィールドを選択します。 この例では、**emailaddress** 要求は **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** の値とマップされます。 これは、Azure AD の電子メール要求の既定の要求名です。 **[SAVE]\(保存\)** をクリックします。

    ![シングル サインオンの構成][57]

    > [!NOTE]
    > Azure AD のユーザーを DocuSign のユーザー マッピングにマッピングする際は、適切な**ユーザー識別子**を使用してください。 適切なフィールドを選択し、組織の設定に基づく適切な値を入力してください。

    j. **[Identity Provider Certificate]\(ID プロバイダー証明書\)** セクションで **[ADD CERTIFICATE]\(証明書の追加\)** をクリックし、Azure AD ポータルからダウンロードした証明書をアップロードし、 **[SAVE]\(保存\)** をクリックします。

    ![シングル サインオンの構成][58]

    k. **[Identity Providers]\(ID プロバイダー\)** セクションで、 **[ACTIONS]\(アクション\)** をクリックし、 **[Endpoints]\(エンドポイント\)** をクリックします。

    ![シングル サインオンの構成][59]

    l. **DocuSign 管理者ポータル**の **[View SAML 2.0 Endpoints]\(SAML 2.0 エンドポイントの表示\)** セクションで、次の手順を実行します。

    ![シングル サインオンの構成][60]

    * **[Service Provider Issuer URL]\(サービス プロバイダー発行者 URL\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

    * **[Service Provider Login URL (サービス プロバイダーのログイン URL)]** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます。

    * **[閉じる]** をクリックします。

### <a name="create-docusign-test-user"></a>DocuSign のテスト ユーザーの作成

このセクションでは、B. Simon というユーザーを DocuSign に作成します。 DocuSign では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 DocuSign にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[DocuSign のサポート チーム](https://support.docusign.com/)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [DocuSign] タイルをクリックすると、SSO を設定した DocuSign に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で DocuSign を試す](https://aad.portal.azure.com/)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png