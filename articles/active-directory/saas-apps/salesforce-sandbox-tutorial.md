---
title: チュートリアル:Azure Active Directory と Salesforce Sandbox の統合 | Microsoft Docs
description: Azure Active Directory と Salesforce Sandbox の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: 1e303485a03edcd9ba3d3e7380aa4c7ae8b1a4b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092612"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>チュートリアル:Azure Active Directory と Salesforce Sandbox の統合

このチュートリアルでは、Salesforce Sandbox と Azure Active Directory (Azure AD) を統合する方法について説明します。

Sandbox を使用すると、Salesforce 運用組織内のデータとアプリケーションを侵害することなく、多様な用途 (開発、テスト、トレーニングなど) ごとに別環境に組織の複数コピーを作成できます。
詳細については、[サンドボックスの概要](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5)に関するページを参照してください。

Salesforce Sandbox と Azure AD の統合には、次の利点があります。

* Salesforce Sandbox にアクセスできる Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Salesforce Sandbox に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Salesforce Sandbox の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Salesforce Sandbox でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Salesforce Sandbox では、**SP と IDP** によって開始される SSO がサポートされます
* Salesforce Sandbox では、**Just-In-Time** ユーザー プロビジョニングがサポートされます
* Salesforce Sandbox では、[**自動化された**ユーザー プロビジョニング](salesforce-sandbox-provisioning-tutorial.md)がサポートされます

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>ギャラリーからの Salesforce Sandbox の追加

Azure AD への Salesforce Sandbox の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Salesforce Sandbox を追加する必要があります。

**ギャラリーから Salesforce Sandbox を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Salesforce Sandbox**」と入力し、結果パネルで **[Salesforce Sandbox]** を選択し、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果リスト内の Salesforce Sandbox](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Salesforce Sandbox で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Salesforce Sandbox ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Salesforce Sandbox の関連ユーザーの間で、リンク関係が確立されている必要があります。

Salesforce Sandbox で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Salesforce Sandbox シングル サインオンの構成](#configure-salesforce-sandbox-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Salesforce Sandbox テスト ユーザーの作成](#create-salesforce-sandbox-test-user)** - Salesforce Sandbox で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Salesforce Sandbox で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Salesforce Sandbox** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **サービス プロバイダー メタデータ ファイル**を保持しており、**IDP** によって開始されるモードに構成したい場合は、 **[基本的な SAML 構成]** セクション上で次の手順を実行します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![メタデータ ファイルをアップロードする](common/upload-metadata.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![メタデータ ファイルを選択する](common/browse-upload-metadata.png)

    > [!NOTE]
    > このチュートリアルの後述するように、Salesforce Sandbox 管理ポータルからサービス プロバイダーのメタデータ ファイルを取得します。

    c. メタデータ ファイルが正常にアップロードされると、 **[応答 URL]** の値が **[応答 URL]** ボックスに自動的に入力されます。

    ![image](common/both-replyurl.png)

    > [!Note]
    > **応答 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

5. **[Set up Single Sign-On with SAML]\(SAML でシングル サインオンをセットアップします\)**  ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションから**メタデータ XML**をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Salesforce Sandbox のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-salesforce-sandbox-single-sign-on"></a>Salesforce Sandbox シングル サインオンの構成

1. ブラウザーで新しいタブを開き、Salesforce Sandbox の管理者アカウントにサインインします。

2. ページの右上隅の**設定アイコン**の下の **[Setup]\(セットアップ\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure1.png)

3. 左側のナビゲーション ウィンドウの **[設定]** まで下にスクロールし、 **[ID]** をクリックして、関連セクションを展開します。 次に、 **[シングル サインオンの設定]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. **[シングル サインオンの設定]** ページで、 **[編集]** ボタンをクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure3.png)

5. **[SAML 有効]** を選択し、 **[保存]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. SAML のシングル サインオン設定を構成するには、 **[New from Metadata File]\(メタデータ ファイルから新規)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. **[ファイルの選択]** をクリックして、Azure portal からダウンロードしたメタデータ XML ファイルをアップロードし、 **[作成]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. **[SAML シングル サインオンの設定]** ページでは、フィールドは自動的に入力されます。[保存] をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. **[シングル サインオンの設定]** ページで **[メタデータのダウンロード]** ボタンをクリックし、サービス プロバイダーのメタデータ ファイルをダウンロードします。 前述の必要な URL を構成するために、Azure portal の **[基本的な SAML 構成]** セクションでこのファイルを使用します。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure4.png)

10. **SP** 開始モードでアプリケーションを構成する場合、その前提条件を以下に示します。

    a. 確認済みドメインを持っている必要があります。

    b. Salesforce Sandbox でドメインを構成して有効にする必要があります。手順については、このチュートリアルで後述します。

    c. Azure portal の **[基本的な SAML 構成]** セクションで **[追加の URL を設定します]** をクリックして、次の手順を実行します。
  
    ![Salesforce Sandbox Domain のドメインと URL のシングル サインオン情報](common/both-signonurl.png)

    **[サインオン URL]** ボックスに、`https://<instancename>--Sandbox.<entityid>.my.salesforce.com` のパターンを使用して値を入力します。

    > [!NOTE]
    > この値は、ドメインを有効にした後で Salesforce Sandbox ポータルからコピーする必要があります。

11. **[SAML 署名証明書]** セクションで **[フェデレーション メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

12. ブラウザーで新しいタブを開き、Salesforce Sandbox の管理者アカウントにサインインします。

13. ページの右上隅の**設定アイコン**の下の **[Setup]\(セットアップ\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure1.png)

14. 左側のナビゲーション ウィンドウの **[設定]** まで下にスクロールし、 **[ID]** をクリックして、関連セクションを展開します。 次に、 **[シングル サインオンの設定]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. **[シングル サインオンの設定]** ページで、 **[編集]** ボタンをクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure3.png)

16. **[SAML 有効]** を選択し、 **[保存]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. SAML のシングル サインオン設定を構成するには、 **[New from Metadata File]\(メタデータ ファイルから新規)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. **[ファイルの選択]** をクリックし、メタデータ XML ファイルをアップロードして **[作成]** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. **[SAML Single Sign-On Settings]\(SAML のシングル サインオン設定\)** ページのフィールドには自動的に値が入力されています。 **[名前]** ボックスに構成の名前 (例: *SPSSOWAAD_Test*) を入力し、[保存] をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Salesforce Sandbox でドメインを有効にするには、次の手順を実行します。

    > [!NOTE]
    > ドメインを有効にする前に、Salesforce Sandbox 上に同じドメインを作成する必要があります。 詳細については、「[ドメイン名の定義](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)」をご覧ください。 ドメインを作成したら、ドメインが正しく構成されていることを確認してください。

21. Salesforce Sandbox の左側のナビゲーション ウィンドウで、 **[Company Settings]\(会社の設定\)** をクリックして関連するセクションを展開し、 **[My Domain]\(マイ ドメイン\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. **[Authentication Configuration]\(認証の構成\)** セクションで、 **[Edit]\(編集\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. **[Authentication Configuration]\(認証の構成\)** セクションで、 **[Authentication Service]\(認証サービス\)** として、Salesforce Sandbox の SSO 構成時に設定した [SAML Single Sign-On Setting]\(SAML シングル サインオン設定\) の名前を選択し、 **[Save]\(保存\)** をクリックします。

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Salesforce Sandbox へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Salesforce Sandbox]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Salesforce Sandbox]** を選択します。

    ![アプリケーション リストの [Salesforce Sandbox] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-salesforce-sandbox-test-user"></a>Salesforce Sandbox テスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Salesforce Sandbox に作成します。 Salesforce Sandbox では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Salesforce Sandbox に存在しない場合は、Salesforce Sandbox にアクセスしようとしたときに新しいユーザーが作成されます。 Salesforce Sandbox は、自動ユーザー プロビジョニングもサポートしています。自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](salesforce-sandbox-provisioning-tutorial.md)を参照してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Salesforce Sandbox] タイルをクリックすると、SSO を設定した Salesforce Sandbox に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [[ユーザー プロビジョニングの構成]](salesforce-sandbox-provisioning-tutorial.md)
