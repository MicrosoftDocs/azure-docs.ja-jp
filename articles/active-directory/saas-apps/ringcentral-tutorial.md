---
title: チュートリアル:Azure Active Directory と RingCentral の統合 | Microsoft Docs
description: Azure Active Directory と RingCentral の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9cd97bc226ec69441b933a9f7bf3caec17f1478
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57877124"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>チュートリアル:Azure Active Directory と RingCentral の統合

このチュートリアルでは、RingCentral と Azure Active Directory (Azure AD) を統合する方法について説明します。

RingCentral と Azure AD の統合には、次の利点があります。

- RingCentral にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に RingCentral にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と RingCentral の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- RingCentral でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの RingCentral の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-ringcentral-from-the-gallery"></a>ギャラリーからの RingCentral の追加
Azure AD への RingCentral の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから RingCentral を追加する必要があります。

**ギャラリーから RingCentral を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![image](./media/ringcentral-tutorial/selectazuread.png)

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![image](./media/ringcentral-tutorial/a_select_app.png)
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![image](./media/ringcentral-tutorial/a_new_app.png)

4. 検索ボックスに「**RingCentral**」と入力し、結果ウィンドウで **RingCentral** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![image](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、RingCentral で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する RingCentral ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと RingCentral の関連ユーザーの間で、リンク関係が確立されている必要があります。

RingCentral で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[RingCentral テスト ユーザーの作成](#create-a-ringcentral-test-user)** - RingCentral で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、RingCentral アプリケーションでシングル サインオンを構成します。

**RingCentral で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. [Azure portal](https://portal.azure.com/) の **RingCentral** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![image](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. 画面上部の **[シングル サインオン モードの変更]** をクリックして、**[SAML]** モードを選択します。

      ![image](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードを選択して、シングル サインオンを有効にします。

    ![image](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![image](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル**がある場合は、次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![image](./media/ringcentral-tutorial/b9_saml.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、**[アップロード]** をクリックします。

    ![image](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. メタデータ ファイルが正常にアップロードされると、次に示すように、**識別子**と**応答 URL** の値が、**[基本的な SAML 構成]** セクションのテキスト ボックスに自動的に設定されます。

    ![image](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. **[サインオン URL]** テキスト ボックスに、URL を入力します。

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > RingCentral SSO 構成ページで、**サービス プロバイダー メタデータ ファイル**を取得します。これについては後で説明します。

6. **サービス プロバイダー メタデータ ファイル**がない場合は、次の手順に従います。

    a. **[サインオン URL]** テキスト ボックスに、URL を入力します。

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. **[識別子]** ボックスに次の URL を入力します。

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. **[応答 URL]** ボックスに、URL を入力します。
    
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![image](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. **[Set up Single Sign-On with SAML]\(SAML でのシングル サインオンの設定\)** ページの **[SAML Signing Certificate]\(SAML 署名証明書\)** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの証明書をダウンロードして、お使いのコンピューターに保存します。

    ![image](./media/ringcentral-tutorial/certificatebase64.png)
    
8. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として RingCentral にログインします。

9. 上部の **[ツール]** をクリックします。

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

10. **[シングル サインオン]** に移動します。

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

11. **[Single Sign-on]\(シングル サインオン\)** ページの **[SSO Configuration]\(SSO 構成\)** セクションで、**[Step 1]\(ステップ 1\)** の **[Edit]\(編集\)** をクリックして、次の手順を実行します。

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

12. **[Set up Single Sign-on]\(シングル サインオンのセットアップ\)** ページで、次の手順を実行します。

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. **[Browse]\(参照\)** をクリックし、Azure portal からダウンロードしたメタデータ ファイルをアップロードします。

    b. メタデータをアップロードすると、**[SSO General Information]\(SSO の一般情報\)** セクションの値が自動的に設定されます。

    c. **[Attribute Mapping]\(属性マッピング\)** セクションの **[Map Email Attribute to]\(メール属性のマップ先\)** で `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` を選択します

    d. **[Save]** をクリックします。

    e. **[Step 2]\(ステップ 2\)** の **[Download]\(ダウンロード\)** をクリックして**サービス プロバイダー メタデータ ファイル**をダウンロードし、Azure portal 内でそれを **[基本的な SAML 構成]** セクションにアップロードして、**[識別子]** と **[応答 URL]** を自動的に設定します。

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. 同じページで **[Enable SSO]\(SSO の有効化\)** セクションに移動し、次の手順を実行します。

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    a. **[Enable SSO Service]\(SSO サービスを有効にする\)** を選択します。
    
    b. **[Allow users to log in with SSO or RingCentral credential]\(ユーザーが SSO または RingCentral 資格情報でログインできるようにする\)** を選択します。

    c. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![image](./media/ringcentral-tutorial/d_users_and_groups.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![image](./media/ringcentral-tutorial/d_adduser.png)

3. [ユーザーのプロパティ] で、次の手順のようにします。

    ![image](./media/ringcentral-tutorial/d_userproperties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-ringcentral-test-user"></a>RingCentral テスト ユーザーを作成する

このセクションでは、RingCentral で Britta Simon というユーザーを作成します。  [RingCentral クライアント サポート チーム](https://success.ringcentral.com/RCContactSupp)と連携し、RingCentral プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に RingCentral へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![image](./media/ringcentral-tutorial/d_all_applications.png)

2. アプリケーションの一覧で **[RingCentral]** を選択します。

    ![image](./media/ringcentral-tutorial/d_all_proapplications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![image](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. **[追加]** ボタンを選択し、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![image](./media/ringcentral-tutorial/d_assign_user.png)

4. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

5. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [RingCentral] タイルをクリックすると、自動的に RingCentral アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

