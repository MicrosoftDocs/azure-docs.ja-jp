---
title: チュートリアル:Azure Active Directory と 123FormBuilder SSO の統合 | Microsoft Docs
description: Azure Active Directory と 123FormBuilder SSO の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: jeedes
ms.openlocfilehash: 2fadfac3fe9e66c3a05e2cceed19def607ff72c3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539285"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-123formbuilder-sso"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と 123FormBuilder SSO の統合

このチュートリアルでは、123FormBuilder SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。 123FormBuilder SSO と Azure AD を統合すると、次のことができるようになります。

* 123FormBuilder SSO にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して 123FormBuilder SSO に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* 123FormBuilder SSO でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* 123FormBuilder SSO では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* 123FormBuilder SSO では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。
* 123FormBuilder SSO を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-123formbuilder-sso-from-the-gallery"></a>ギャラリーからの 123FormBuilder SSO の追加

Azure AD への 123FormBuilder SSO の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に 123FormBuilder SSO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**123FormBuilder SSO**」と入力します。
1. 結果のパネルから **[123FormBuilder SSO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-123formbuilder-sso"></a>123FormBuilder SSO の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、123FormBuilder SSO に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと 123FormBuilder SSO の関連ユーザーとの間にリンク関係を確立する必要があります。

123FormBuilder SSO で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[123FormBuilder SSO の構成](#configure-123formbuilder-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[123FormBuilder SSO のテスト ユーザーの作成](#create-123formbuilder-sso-test-user)** - 123FormBuilder SSO で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **123FormBuilder SSO** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/metadata` の形式で URL を入力します。


    b. **[応答 URL]** ボックスに、`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/acs` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://www.123formbuilder.com/saml/azure_ad/<tenant_id>/sso` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、このチュートリアルの後半で説明する実際の URL と識別子に更新する必要があります。

1. **[SAML によるシングル サインオンのセットアップ]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[123FormBuilder SSO のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に 123FormBuilder SSO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[123FormBuilder SSO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-123formbuilder-sso"></a>123FormBuilder SSO の構成

1. **123FormBuilder SSO** 側からシングル サインオンを構成するために、[https://www.123formbuilder.com/form-2709121/](https://www.123formbuilder.com/form-2709121/) に移動して次の手順を実行します。

    ![Configure single sign-on](./media/123formbuilder-tutorial/submit.png) 

    a. **[Email]\(電子メール\)** ボックスに、ユーザーの電子メール (`B.Simon@Contoso.com` など) を入力します。

    b. **[Upload]\(アップロード\)** をクリックし、Azure portal からダウンロードしたダウンロード メタデータ XML ファイルを参照します。

    c. **[SUBMIT FORM]\(フォームの送信\)** をクリックします。

2. **[Microsoft Azure AD - Single sign-on - Configure App Settings]\(Microsoft Azure AD - シングル サインオン - アプリケーション設定の構成\)** で、次の手順を実行します。

    ![Configure single sign-on](./media/123formbuilder-tutorial/url3.png)

    a. アプリケーションを **IDP 開始モード**で構成する場合は、インスタンスの **[IDENTIFIER]\(識別子\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

    b. アプリケーションを **IDP 開始モード**で構成する場合は、インスタンスの **[REPLY URL]\(応答 URL\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    c. アプリケーションを **SP 開始モード**で構成する場合は、インスタンスの **[SIGN ON URL]\(サインオン URL\)** をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます。

### <a name="create-123formbuilder-sso-test-user"></a>123FormBuilder SSO のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを 123FormBuilder SSO に作成します。 123FormBuilder SSO では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 123FormBuilder SSO にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [123FormBuilder SSO] タイルをクリックすると、SSO を設定した 123FormBuilder SSO に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で 123FormBuilder SSO を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
