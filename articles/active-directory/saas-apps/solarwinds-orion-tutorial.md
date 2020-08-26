---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と SolarWinds Orion の統合 | Microsoft Docs
description: Azure Active Directory と SolarWinds Orion の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: jeedes
ms.openlocfilehash: 723546d6a997d895c1a1df39b558aabb797d0a11
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545099"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SolarWinds Orion の統合

このチュートリアルでは、SolarWinds Orion と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SolarWinds Orion を統合すると、次のことができます。

* SolarWinds Orion にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SolarWinds Orion に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SolarWinds Orion でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SolarWinds Orion では、**SP と IDP** Initiated SSO がサポートされます
* SolarWinds Orion を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-solarwinds-orion-from-the-gallery"></a>ギャラリーからの SolarWinds Orion の追加

Azure AD への SolarWinds Orion の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SolarWinds Orion を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SolarWinds Orion**」と入力します。
1. 結果のパネルから **SolarWinds Orion** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>SolarWinds Orion に対する Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SolarWinds Orion に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SolarWinds Orion の関連ユーザーとの間にリンク関係を確立する必要があります。

SolarWinds Orion で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SolarWinds Orion の SSO の構成](#configure-solarwinds-orion-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SolarWinds Orion のテスト ユーザーの作成](#create-solarwinds-orion-test-user)** - SolarWinds Orion で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **SolarWinds Orion** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<ORION-HOSTNAME-OR-EXTERNAL-URL>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[SolarWinds Orion クライアント サポート チーム](mailto:technicalsupport@solarwinds.com)に問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. SolarWinds Orion アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、SolarWinds Orion アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 |  ソース属性|
    | ----------- | --------- |
    | FirstName | User.givenname |
    | LastName | User.surname |
    | Email |User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[SolarWinds Orion のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に SolarWinds Orion へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **SolarWinds Orion** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-solarwinds-orion-sso"></a>SolarWinds Orion の SSO を構成する

1. SolarWinds Orion にログインし、 **[Settings]\(設定\)**  ->  **[All Settings]\(すべての設定\)** に移動します。

    ![ SolarWinds Orion の構成 ](./media/solarwinds-orion-tutorial/settings.png)

1. **[USER ACCOUNTS]\(ユーザー アカウント\)** セクションで、 **[SAML Configuration]\(SAML の構成\)** を選択します。

    ![ SolarWinds Orion の構成 ](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. **[ADD IDENTITY PROVIDER]\(ID プロバイダーの追加\)** をクリックします。

    ![ SolarWinds Orion の構成 ](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. **[Add Identity Provider]\(ID プロバイダーの追加\)** ページで、次の手順のようにします。

    ![ SolarWinds Orion の構成 ](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. **[Configure]\(構成\)** タブに移動します。

    b. **[Identity Provider Name]\(ID プロバイダー名\)** テキスト ボックスに、任意の有効な名前 (例: `My SSO service`) を入力します。

    c. **[SSO Target URL]\(SSO ターゲット URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d.  **[Issuer URL]\(発行者 URL\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    e. Azure portal からダウンロードした**証明書 (Base64)** をメモ帳で開き、その内容を **[X.509 Signing Certificate]\(X.509 署名証明書\)** テキスト ボックスに貼り付けます。

    f. **[Save]** をクリックします。

### <a name="create-solarwinds-orion-test-user"></a>SolarWinds Orion のテスト ユーザーを作成する

1. SolarWinds Orion の Web サイトにログインし、 **[Settings]\(設定\)**  ->  **[All Settings]\(すべての設定\)** に移動します。

    ![ SolarWinds Orion でテスト ユーザーを作成する ](./media/solarwinds-orion-tutorial/settings.png)

1. **[USER ACCOUNTS]\(ユーザー アカウント\)** セクションで、 **[Manage Accounts]\(アカウントの管理\)** を選択します。

    ![ SolarWinds Orion でテスト ユーザーを作成する ](./media/solarwinds-orion-tutorial/user-accounts.png)

1. **[INDIVIDUAL ACCOUNTS]\(個別のアカウント\)** タブで、 **[ADD NEW ACCOUNT]\(新しいアカウントの追加\)** をクリックします。

    ![ SolarWinds Orion でテスト ユーザーを作成する ](./media/solarwinds-orion-tutorial/create-user.png)

1. アカウントの種類を選択します。SAML の個人ユーザーまたはグループを作成する必要があります。

    ![ SolarWinds Orion でテスト ユーザーを作成する ](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  **[NAME ID]\(名前 ID\)** テキスト ボックスに、Azure AD と正確に一致するユーザー名またはグループ名を入力します。

1.  **[Next]\(次へ\)** をクリックし、ページを送信します。

    ![ SolarWinds Orion でテスト ユーザーを作成する ](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SolarWinds Orion] タイルをクリックすると、SSO を設定した SolarWinds Orion に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で SolarWinds Orion を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって SolarWinds Orion を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

