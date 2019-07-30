---
title: チュートリアル:Azure Active Directory と Palo Alto Networks - Admin UI の統合 | Microsoft Docs
description: Azure Active Directory と Palo Alto Networks - Admin UI の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd0ca3bb356319f4661e24b192a5f7e776d14cd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095081"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>チュートリアル:Azure Active Directory と Palo Alto Networks - Admin UI の統合

このチュートリアルでは、Palo Alto Networks - Admin UI と Azure Active Directory (Azure AD) を統合する方法について説明します。
Palo Alto Networks - Admin UI と Azure AD の統合には、次のメリットがあります。

* Palo Alto Networks - Admin UI にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Palo Alto Networks - Admin UI に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Azure AD と Palo Alto Networks - Admin UI の統合を構成するには、次のアイテムが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Palo Alto Networks - Admin UI でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Palo Alto Networks - Admin UI では、**SP** によって開始される SSO がサポートされます
* Palo Alto Networks - Admin UI では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>ギャラリーからの Palo Alto Networks - Admin UI の追加

Azure AD への Palo Alto Networks - Admin UI の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Palo Alto Networks - Admin UI を追加する必要があります。

**ギャラリーから Palo Alto Networks - Admin UI を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Palo Alto Networks - Admin UI**」と入力し、結果ウィンドウで **[Palo Alto Networks - Admin UI]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Palo Alto Networks - Admin UI](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Palo Alto Networks - Admin UI で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Palo Alto Networks - Admin UI 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Palo Alto Networks - Admin UI で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Palo Alto Networks - Admin UI のシングル サインオンの構成](#configure-palo-alto-networks---admin-ui-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Palo Alto Networks - Admin UI テスト ユーザーの作成](#create-palo-alto-networks---admin-ui-test-user)** - Palo Alto Networks - Admin UI で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Palo Alto Networks - Admin UI で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Palo Alto Networks - Admin UI** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Palo Alto Networks - Admin UI のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<Customer Firewall FQDN>/php/login.php` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<Customer Firewall FQDN>:443/SAML20/SP` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<Customer Firewall FQDN>:443/SAML20/SP/ACS` という形式を使用して、Assertion Consumer Service (ACS) URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[Palo Alto Networks - Admin UI クライアント サポート チーム](https://support.paloaltonetworks.com/support)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. Palo Alto Networks - Admin UI アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > 属性の値はサンプルです。*username* と *adminrole* には適切な値をマップしてください。 さらにもう 1 つ、省略可能な属性 *accessdomain* があります。この属性は、ファイアウォールで特定の仮想システムへの管理アクセスを制限する目的で使用します。
   >

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name |  ソース属性|
    | --- | --- |
    | username | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

    > [!NOTE]
    > これらの属性の詳細については、次の記事を参照してください。
    > * [Admin UI の 管理ロール プロファイル (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Admin UI の デバイス アクセス ドメイン (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

8. **[Palo Alto Networks - Admin UI のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Palo Alto Networks - Admin UI のシングル サインオンの構成

1. 新しいウィンドウで Palo Alto Networks Firewall Admin UI を管理者として開きます。

2. **[Device]\(デバイス\)** タブを選択します。

    ![[Device]\(デバイス\) タブ](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 左側のウィンドウで **[SAML Identity Provider]\(SAML ID プロバイダー\)** を選択し、 **[Import]\(インポート\)** を選択してメタデータ ファイルをインポートします。

    ![メタデータ ファイルの [Import]\(インポート\) ボタン](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. **[SAML Identify Provider Server Profile Import]\(SAML ID プロバイダー サーバー プロファイル インポート\)** ウィンドウで、次の手順を実行します。

    ![[SAML Identify Provider Server Profile Import]\(SAML ID プロバイダー サーバー プロファイル インポート\) ウィンドウ](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. **[Profile Name]\(プロファイル名\)** ボックスに名前 (「**AzureAD Admin UI**」など) を入力します。
    
    b. **[Identity Provider Metadata]\(ID プロバイダー メタデータ\)** の下で **[Browse]\(参照\)** を選択して、前の手順で Azure Portal からダウンロードした metadata.xml ファイルを選択ます。
    
    c. **[Validate Identity Provider Certificate]\(ID プロバイダー証明書の検証\)** チェック ボックスをオフにします。
    
    d. **[OK]** を選択します。
    
    e. ファイアウォールの構成を確定するには **[Commit]\(コミット\)** を選択します。

5. 左側のウィンドウで、 **[SAML Identity Provider]\(SAML ID プロバイダー\)** を選択し、前の手順で作成した SAML ID プロバイダー プロファイル (たとえば、 **[AzureAD Admin UI]** ) を選択します。

    ![SAML ID プロバイダー プロファイル](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. **[SAML Identify Provider Server Profile]\(SAML ID プロバイダー サーバー プロファイル\)** ウィンドウで、次の手順を実行します。

    ![[SAML Identify Provider Server Profile]\(SAML ID プロバイダー サーバー プロファイル\) ウィンドウ](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. **[Identity Provider SLO URL]\(ID プロバイダー SLO URL\)** ボックスで、前の手順でインポートした SLO URL を `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` で置き換えます。
  
    b. **[OK]** を選択します。

7. Palo Alto Networks Firewall の Admin UI で、 **[Device]\(デバイス\)** をクリックし、 **[Admin Roles]\(管理者ロール\)** を選択します

8. **[追加]** ボタンを選びます。

9. **[Admin Role Profile]\(管理者ロール プロファイル\)** ウィンドウの **[名前]** ボックスに管理者ロールの名前 (たとえば **fwadmin**) を入力します。 この管理者ロール名は、ID プロバイダーから送信された SAML 管理者ロール属性名と一致する必要があります。 管理者ロールの名前と値は、Azure portal の **[ユーザー属性]** セクションで作成されています。

    ![Palo Alto Networks の管理者ロールの構成](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Firewall の Admin UI で、 **[Device]\(デバイス\)** をクリックし、 **[Authentication Profile]\(認証プロファイル\)** を選択します。

11. **[追加]** ボタンを選びます。

12. **[Authentication Profile]\(認証プロファイル\)** ウィンドウで、次の手順を実行します。 

    ![[Authentication Profile]\(認証プロファイル\) ウィンドウ](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. **[Name]\(名前\)** ボックスに名前 (「**AzureSAML_Admin_AuthProfile**」など) を入力します。

    b. **[Type]\(種類\)** ドロップダウン リストで、 **[SAML]** を選択します。 

    c. **[IdP Server Profile]\(IdP サーバー プロファイル\)** ドロップダウン リストで適切な SAML ID プロバイダー サーバーのプロファイル ( **[AzureAD Admin UI]** など) を選択します。

    c. **[Enable Single Logout]\(シングル ログアウトを有効にする\)** チェック ボックスをオンにします

    d. **[Admin Role Attribute]\(管理者ロール属性\)** ボックスに属性名 (「**adminrole**」など) を入力します。

    e. **[Advanced]\(詳細設定\)** タブを選択してから、 **[Allow List]\(許可リスト\)** で **[Add]\(追加\)** を選択します。

    ![[Advanced]\(詳細設定\) タブの [Add]\(追加\) ボタン](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. **[All]\(すべて\)** チェック ボックスをオンにするか、このプロファイルで認証できるユーザーとグループを選択します。  
    ユーザーが認証すると、ファイアウォールは関連するユーザー名またはグループをこの一覧のエントリと照合します。 エントリを追加しないと、ユーザーは認証できません。

    g. **[OK]** を選択します。

13. 管理者が Azure を使用して SAML SSO を使用できるようにするには、 **[Device]\(デバイス\)**  >  **[Setup]\(セットアップ\)** を選択します。 **[Setup]\(セットアップ\)** ウィンドウで **[Management]\(管理\)** タブを選択してから、 **[Authentication Settings]\(認証の設定\)** で**設定** ("歯車") ボタンを選択します。

    ![[設定] ボタン](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. [Authentication Profile]\(認証プロファイル\) ウィンドウで作成した SAML 認証プロファイル (たとえば、**AzureSAML_Admin_AuthProfile**) を選択します。

    ![[Authentication Profile]\(認証プロファイル\) フィールド](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. **[OK]** を選択します。

16. 構成をコミットするには **[Commit]\(コミット\)** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Palo Alto Networks - Admin UI へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Palo Alto Networks - Admin UI]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Palo Alto Networks - Admin UI]** を選択します。

    ![アプリケーションの一覧の [Palo Alto Networks - Admin UI] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Palo Alto Networks - Admin UI のテスト ユーザーの作成

Palo Alto Networks - Admin UI では、Just-In-Time ユーザー プロビジョニングがサポートされます。 ユーザーが既に存在していない場合、認証の成功後に自動的にシステム内に作成されます。 ユーザーを作成する操作は不要です。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Palo Alto Networks - Admin UI] タイルをクリックすると、SSO を設定した Palo Alto Networks - Admin UI に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
