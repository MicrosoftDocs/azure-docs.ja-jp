---
title: チュートリアル:Azure Active Directory と Sage Intacct の統合 | Microsoft Docs
description: Azure Active Directory と Sage Intacct の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: jeedes
ms.openlocfilehash: 79a77d3c55ee4ac545712329f0054123bec8fcae
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88535844"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>チュートリアル:Sage Intacct と Azure Active Directory を統合する

このチュートリアルでは、Sage Intacct と Azure Active Directory (Azure AD) を統合する方法について説明します。 Sage Intacct と Azure AD を統合すると、次のことができます。

* Azure AD で Sage Intacct にアクセスできるユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Sage Intacct に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Sage Intacct でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Sage Intacct では、**IDP** Initiated SSO がサポートされます
* Sage Intacct を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-sage-intacct-from-the-gallery"></a>ギャラリーからの Sage Intacct の追加

Azure AD への Sage Intacct の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sage Intacct を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Sage Intacct**」と入力します。
1. 結果パネルから **[Sage Intacct]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Sage Intacct の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Sage Intacct に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Sage Intacct の関連ユーザーの間で、リンク関係が確立されている必要があります。

Sage Intacct で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
2. **[Sage Intacct の SSO の構成](#configure-sage-intacct-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Sage Intacct のテスト ユーザーの作成](#create-sage-intacct-test-user)** - Sage Intacct で B.Simon に対応するユーザーを作成し、Azure AD のそのユーザーにリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Sage Intacct** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://www.intacct.com/ia/acct/sso_response.phtml` という URL を入力します。

1. Sage Intacct アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性 の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![image](common/edit-attribute.png)

1. その他に、Sage Intacct アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[User Attributes & Claims]\(ユーザーの属性と要求\)** ダイアログで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | 属性名  |  ソース属性|
    | ---------------| --------------- |
    | 会社名 | **Sage Intacct の会社 ID** |
    | name | 値は、Sage Intacct の **[User ID]\(ユーザー ID\)** と同じにするようにします。これは、このチュートリアルの後半で説明する「**Sage Intacct のテスト ユーザーの作成**」セクションで入力するものです。 |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力または選択します。

    f. **[OK]** をクリックします。

    g. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Sage Intacct のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Sage Intacct へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Sage Intacct]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sage-intacct-sso"></a>Sage Intacct SSO の構成

1. 別の Web ブラウザー ウィンドウで、Sage Intacct 企業サイトに管理者としてサインインします。

1. **[会社]** タブをクリックし、 **[会社情報]** をクリックします。

    ![Company](./media/intacct-tutorial/ic790037.png "[会社]")

1. **[セキュリティ]** タブをクリックし、 **[編集]** をクリックします。

    ![Security](./media/intacct-tutorial/ic790038.png "Security")

1. **[シングル サインオン (SSO)]** セクションで、次の手順を実行します。

    ![シングル サインオン](./media/intacct-tutorial/ic790039.png "シングル サインオン")

    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[ID プロバイダーの種類]** として **[SAML 2.0]** を選択します。

    c. **[発行者の URL]** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    d. **[ログイン URL]** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    e. **base-64** でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[証明書]** ボックスに貼り付けます。

    f. **[保存]** をクリックします。

### <a name="create-sage-intacct-test-user"></a>Sage Intacct のテスト ユーザーの作成

Azure AD ユーザーが Sage Intacct にサインインできるように設定するには、そのユーザーを Sage Intacct にプロビジョニングする必要があります。 Sage Intacct の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順を実行します。**

1. **Sage Intacct** テナントにサインインします。

1. **[会社]** タブをクリックし、 **[ユーザー]** をクリックします。

    ![ユーザー](./media/intacct-tutorial/ic790041.png "ユーザー")

1. **[Add (追加)]** タブをクリックします。

    ![追加](./media/intacct-tutorial/ic790042.png "追加")

1. **[ユーザー情報]** セクションで、次の手順を実行します。

    ![[User Information]\(ユーザー情報\)](./media/intacct-tutorial/ic790043.png "[ユーザー情報]")

    a. プロビジョニングする Azure AD アカウントの**ユーザー ID**、**姓**、**名**、**電子メール アドレス**、**役職**、**電話番号**を、 **[User Information (ユーザー情報)]** セクションに入力します。

    > [!NOTE]
    > 上のスクリーンショットの **[User ID]\(ユーザー ID\)** と、Azure portal の **[ユーザー属性]** セクションの **name** 属性にマップされている **[Source Attribute]\(ソース属性\)** の値が同じになるようにします。

    b. プロビジョニングする Azure AD アカウントの**管理者特権**を選択します。

    c. **[保存]** をクリックします。 
    
    d. Azure AD アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

1. **[Single sign-on]\(シングル サインオン\)** タブをクリックし、下のスクリーンショットの **[Federated SSO User ID]\(フェデレーション SSO のユーザー ID\)** と、Azure portal の **[ユーザー属性]** セクションの `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` にマップされている **[Source Attribute]\(ソース属性\)** の値が同じになるようにします。

    ![[User Information]\(ユーザー情報\)](./media/intacct-tutorial/ic790044.png "[ユーザー情報]")

> [!NOTE]
> Azure AD ユーザー アカウントをプロビジョニングするには、Sage Intacct から提供されているその他の Sage Intacct ユーザー アカウント作成ツールまたは API を使用できます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Sage Intacct] タイルをクリックすると、SSO を設定した Sage Intacct に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
