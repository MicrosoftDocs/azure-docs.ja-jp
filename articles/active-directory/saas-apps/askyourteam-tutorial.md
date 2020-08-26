---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と AskYourTeam の統合 | Microsoft Docs
description: Azure Active Directory と AskYourTeam の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.openlocfilehash: ad68208f8993ce84c71c97d97de7e2b090f1e7da
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549420"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と AskYourTeam の統合

このチュートリアルでは、AskYourTeam と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と AskYourTeam を統合すると、次のことができます。

* AskYourTeam にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して AskYourTeam に自動的にサインインできるようになります。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AskYourTeam でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* AskYourTeam では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* AskYourTeam を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-askyourteam-from-the-gallery"></a>ギャラリーからの AskYourTeam の追加

Azure AD への AskYourTeam の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に AskYourTeam を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**AskYourTeam**」と入力します。
1. 結果のパネルから **[AskYourTeam]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>AskYourTeam の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、AskYourTeam に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと AskYourTeam の関連ユーザーとの間にリンク関係を確立する必要があります。

AskYourTeam に対する Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[AskYourTeam の SSO の構成](#configure-askyourteam-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[AskYourTeam のテスト ユーザーの作成](#create-askyourteam-test-user)** - AskYourTeam で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **AskYourTeam** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<COMPANY>.app.askyourteam.com/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値は、実際の応答 URL およびサインオン URL の値に更新します。実際の値については、このチュートリアルの後の方で説明します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[AskYourTeam のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に AskYourTeam へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[AskYourTeam]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-askyourteam-sso"></a>AskYourTeam の SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者として AskYourTeam Web サイトにサインインします。

1. **[My Organisation]\(マイ組織\)** をクリックします。

    ![AskYourTeam の構成](./media/askyourteam-tutorial/user1.png)

1. **[統合]** をクリックします。

    ![AskYourTeam の構成](./media/askyourteam-tutorial/configure1.png)

1. **[Edit Settings]\(設定の編集\)** をクリックします。

    ![AskYourTeam の構成](./media/askyourteam-tutorial/configure2.png)

1. **[Edit Single Sign-On Integration]\(シングル サインオンの統合の編集\)** ページで、次の手順を実行します。 

    ![AskYourTeam の構成](./media/askyourteam-tutorial/configure3.png)

    a. **[SAML Single Sign-On Service URL]\(SAML シングル サインオンサービス URL\)** テキスト ボックスに、Azure portal からコピーした**ログインURL** の値を貼り付けます。

    b. **[SAML Entity ID]\(SAML エンティティ ID\)** テキスト ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. **[Sign-Out URL]\(サインアウト URL\)** ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    d. Azure portal からダウンロードした**証明書 (Base64)** をメモ帳で開き、その内容を **[SAML Signing Certificate - Base64]\(SAML 署名証明書 - Base64\)** テキスト ボックスに貼り付けます。

    > [!NOTE]
    > または、 **[Choose File]\(ファイルの選択\)** オプションをクリックして、**フェデレーション メタデータ XML** ファイルをアップロードすることもできます。

    e. **[Reply URL (Assertion Consumer Service URL)]\(応答 URL (アサーション コンシューマー サービス URL)\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキスト ボックスに貼り付けます。

    f. **[Sign on URL]\(サインオン URL\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** テキスト ボックスに貼り付けます。

    g. **[保存]** をクリックします。

### <a name="create-askyourteam-test-user"></a>AskYourTeam のテスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、管理者として AskYourTeam Web サイトにサインインします。

1. **[My Organisation]\(マイ組織\)** をクリックします。

    ![AskYourTeam の構成](./media/askyourteam-tutorial/user1.png)

1. **[Users]\(ユーザー\)** をクリックし、 **[New User]\(新しいユーザー\)** を選択します。

    ![AskYourTeam の構成](./media/askyourteam-tutorial/user2.png)

1. **[New user]\(新しいユーザー\)** セクションで、次の手順を実行します。

    ![AskYourTeam の構成](./media/askyourteam-tutorial/user3.png)

    1. **[First name]\(名\)** テキスト ボックスに、ユーザーの名前を入力します。

    1. **[Last name]\(姓\)** テキスト ボックスに、ユーザーの姓を入力します。

    1. **[Email]\(電子メール\)** テキスト ボックスに、ユーザーのメール アドレス (B.Simon@contoso.com など) を入力します。

    1. 組織の要件に従って、ユーザーの**ロール**を選択します。

    1. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [AskYourTeam] タイルをクリックすると、SSO を設定した AskYourTeam に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で AskYourTeam を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
