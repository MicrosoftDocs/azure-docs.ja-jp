---
title: チュートリアル:Azure Active Directory と SAP Analytics Cloud の統合 | Microsoft Docs
description: Azure Active Directory と SAP Analytics Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.openlocfilehash: 9edbb499788b61135d761c5dfa69ce42c9275932
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548752"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>チュートリアル:SAP Analytics Cloud と Azure Active Directory の統合

このチュートリアルでは、SAP Analytics Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP Analytics Cloud と Azure AD を統合すると、次のことができます。

* SAP Analytics Cloud にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して SAP Analytics Cloud に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SAP Analytics Cloud でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SAP Analytics Cloud では、**SP** Initiated SSO がサポートされます

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>ギャラリーからの SAP Analytics Cloud の追加

Azure AD への SAP Analytics Cloud の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SAP Analytics Cloud を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**SAP Analytics Cloud**」と入力します。
1. 結果のパネルから **[SAP Analytics Cloud]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAP Analytics Cloud に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SAP Analytics Cloud の関連ユーザーとの間にリンク関係を確立する必要があります。

SAP Analytics Cloud に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[SAP Analytics Cloud SSO の構成](#configure-sap-analytics-cloud-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SAP Analytics Cloud のテスト ユーザーの作成](#create-sap-analytics-cloud-test-user)** - SAP Analytics Cloud で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **SAP Analytics Cloud** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次の形式で URL を入力します。

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. **[識別子 (エンティティ ID)]** テキスト ボックスに、次のパターンで URL を入力します。

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > これらの URL の値は、単なる例です。 実際のサインオン URL と識別子 URLでこれらの値を更新してください。 サインオン URL を取得するには、[SAP Analytics Cloud クライアント サポート チーム](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)に問い合わせてください。 識別子 URL は、管理コンソールから SAP Analytics Cloud のメタデータをダウンロードすることで取得できます。 これについては、このチュートリアルの後半で説明します。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[SAP Analytics Cloud のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>SAP Analytics Cloud SSO の構成

1. Web ブラウザーの別のウィンドウで、SAP Analytics Cloud 企業サイトに管理者としてサインインします。

2. **[Menu]\(メニュー\)**  >  **[System]\(システム\)**  >  **[Administration]\(管理\)** を選択します。
    
    ![[Menu]\(メニュー\)、[System]\(システム\)、および [Administration]\(管理\) の選択](./media/sapboc-tutorial/config1.png)

3. **[Security]\(セキュリティ\)** タブで **[Edit]\(編集\)** (ペン) アイコンを選択します。
    
    ![[Security]\(セキュリティ\) タブで [Edit]\(編集\)アイコンを選択](./media/sapboc-tutorial/config2.png)  

4. **[Authentication Method]\(認証方法\)** として **[SAML Single Sign-On (SSO)]\(SAML シングル サインオン (SSO)\)** を選択します。

    ![認証方法として [SAML Single Sign-On (SSO)]\(SAML シングル サインオン (SSO)\) を選択](./media/sapboc-tutorial/config3.png)  

5. サービス プロバイダーのメタデータをダウンロードする (手順 1) には、 **[Download]\(ダウンロード\)** を選択します。 メタデータ ファイルで、**entityID** を見つけてその値をコピーします。 Azure portal で、 **[基本的な SAML 構成]** ダイアログの **[識別子]** ボックスに値を貼り付けます。

    ![EntityID 値をコピーして貼り付ける](./media/sapboc-tutorial/config4.png)  

6. Azure ポータルからダウンロードしたファイル内のサービス プロバイダーのメタデータをアップロードする (手順 2) には、 **[Upload Identity Provider metadata]\(ID プロバイダーのメタデータのアップロード\)** で、 **[アップロード]** を選択します。  

    ![[Upload Identity Provider metadata]\(ID プロバイダーのメタデータのアップロード\) で [アップロード] を選択](./media/sapboc-tutorial/config5.png)

7. **[User Attribute]\(ユーザー属性\)** の一覧で、実装で使用するユーザー属性を選択します (手順 3)。 このユーザー属性が ID プロバイダーにマップされます。 ユーザーのページでカスタム属性を入力するには、 **[Custom SAML Mapping]\(カスタム SAML マッピング\)** オプションを使用します。 または、ユーザー属性として **[Email]\(電子メール\)** または **[USER ID]\(ユーザー ID\)** のいずれかを選択できます。 この例では、Azure portal で **[ユーザー属性とクレーム]** セクションの **userprincipalname** 属性にユーザー識別子要求をマップしたため、 **[Email]\(電子メール\)** を選択しています。 これにより、一意のユーザーのメールが用意され、SAML 応答が成功するたびに SAP Analytics Cloud アプリケーションに送信されます。

    ![ユーザー属性の選択](./media/sapboc-tutorial/config6.png)

8. アカウントを ID プロバイダーで確認する (ステップ 4) には、 **[Login Credential (Email)]\(ログイン資格情報 (電子メール)\)** ボックスに、ユーザーの電子メール アドレスを入力します。 次に、 **[Verify Account]\(アカウントの確認\)** を選択します。 システムがサインイン資格情報をユーザー アカウントに追加します。

    ![電子メール アドレスを入力し、[Verify Account]\(アカウントの確認\) を選択](./media/sapboc-tutorial/config7.png)

9. **[Save]\(保存\)**  アイコンを選択します。

    ![[Save]\(保存\) アイコン](./media/sapboc-tutorial/save.png)

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

このセクションでは、B.Simon に SAP Analytics Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SAP Analytics Cloud]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-sap-analytics-cloud-test-user"></a>SAP Analytics Cloud のテスト ユーザーの作成

Azure AD ユーザーが SAP Analytics Cloud にサインインできるようにするには、そのユーザーを SAP Analytics Cloud にプロビジョニングしておく必要があります。 SAP Analytics Cloud では、プロビジョニングは手動のタスクです。

ユーザー アカウントをプロビジョニングするには:

1. SAP Analytics Cloud 企業サイトに管理者としてサインインします。

2. **[Menu]\(メニュー\)**  >  **[Security]\(セキュリティ\)**  >  **[Users]\(ユーザー\)** を選択します。

    ![従業員の追加](./media/sapboc-tutorial/user1.png)

3. **[Users]\(ユーザー\)** ページで、新しいユーザーの詳細を追加するには、 **+** を選択します。 

    ![[Add Users]\(ユーザーの追加\) ページ](./media/sapboc-tutorial/user4.png)

    その後、次の手順を完了します。

    a. **[USER ID]\(ユーザー ID\)** ボックスに、ユーザーのユーザー ID を入力します (**B** など)。

    b. **[FIRST NAME]\(名\)** ボックスに、ユーザーの名を入力します (**B** など)。

    c. **[LAST NAME]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    d. **[DISPLAY NAME]\(表示名\)** ボックスに、ユーザーのフル ネームを入力します (**B.Simon** など)。

    e. **[E-MAIL]\(メール\)** ボックスに、ユーザーのメール アドレスを入力します (`b.simon@contoso.com` など)。

    f. **[Select Roles]\(ロールの選択\)** ページで、ユーザーの適切なロールを選択し、 **[OK]** を選択します。

      ![Select role](./media/sapboc-tutorial/user3.png)

    g. **[Save]\(保存\)**  アイコンを選択します。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SAP Analytics Cloud] タイルをクリックすると、SSO を設定した SAP Analytics Cloud に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

