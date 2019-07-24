---
title: チュートリアル:Azure Active Directory と LinkedIn Sales Navigator の統合 | Microsoft Docs
description: Azure Active Directory と LinkedIn Sales Navigator の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63e3e98c2c3dc8f99e733174c86965304fe483ce
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182002"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>チュートリアル:Azure Active Directory と LinkedIn Sales Navigator の統合

このチュートリアルでは、LinkedIn Sales Navigator と Azure Active Directory (Azure AD) を統合する方法について説明します。

LinkedIn Sales Navigator と Azure AD の統合には、次の利点があります。

- LinkedIn Sales Navigator にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで LinkedIn Sales Navigator に自動的にサインオン (シングル サインオン) する機能を有効にすることができます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

LinkedIn Sales Navigator と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- LinkedIn Sales Navigator でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの LinkedIn Sales Navigator ナビゲーターの追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>ギャラリーからの LinkedIn Sales Navigator ナビゲーターの追加
Azure AD への LinkedIn Sales Navigator の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LinkedIn Sales Navigator を追加する必要があります。

**ギャラリーから LinkedIn Sales Navigator を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**LinkedIn Sales Navigator**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

1. 結果ウィンドウで **[LinkedIn Sales Navigator]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、LinkedIn Sales Navigator で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する LinkedIn Sales Navigator ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと LinkedIn Sales Navigator の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、LinkedIn Sales Navigator の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

LinkedIn Sales Navigator で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[LinkedIn Sales Navigator のテスト ユーザーの作成](#creating-a-linkedin-sales-navigator-test-user)** - LinkedIn Sales Navigatorで Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、LinkedIn Sales Navigator アプリケーションでシングル サインオンを構成します。

**LinkedIn Sales Navigator で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal で、**LinkedIn Sales Navigator** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択して、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

1. 別の Web ブラウザーのウィンドウで、管理者として **LinkedIn Sales Navigator** テナントにサインオンします。

1. **[Account Center (アカウント センター)]** で、**[Settings (設定)]** の下の **[Global Settings (グローバル設定)]** をクリックします。 さらに、ドロップダウン リストから **[Sales Navigator]** を選択します。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. **[OR Click Here to load and copy individual fields from the form (または、ここをクリックしてフォームから個々のフィールドを読み込み、コピーする)]** をクリックし、**[Entity Id (エンティティ ID)]** と **[Assertion Consumer Access (ACS) Url (Assertion Consumer Access (ACS) URL)]** をコピーします。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

1. アプリケーションを **IDP** 開始モードで構成する場合は、Azure Portal の **[LinkedIn Sales Navigator のドメインと URL]** セクションで次の手順を実行します。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. **[識別子]** テキストボックスに、LinkedIn ポータルからコピーした**エンティティ ID** を入力します 

    b. **[応答 URL]** テキストボックスに、LinkedIn ポータルからコピーした **Assertion Consumer Access (ACS) URL** を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにします。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    **[サインオン URL]** ボックスに、`https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator` のパターンを使用して値を入力します。

1. **LinkedIn Sales Navigator** アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングをSAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、例を示しています。 **[ユーザー識別子]** の既定値は **user.userprincipalname** ですが、LinkedIn Sales Navigator はこれがユーザーの電子メール アドレスにマップされることを想定します。 一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。 

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/updateusermail.png)
    
1. **[ユーザー属性]** セクションで、**[その他のすべてのユーザー属性を表示および編集する]** をクリックし、属性を設定します。 ユーザーは **email**、**department**、**firstname**、**lastname** という名前の 4 つの要求を追加する必要があり、値はそれぞれ **user.mail**、**user.department**、**user.givenname**、**user.surname** にマップします。

    | 属性名 | 属性値 |
    | --- | --- |    
    | email| User.mail |
    | department| user.department |
    | firstname| User.givenname |
    | lastname| User.surname |
    
    ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. **[属性の追加]** をクリックして [属性の追加] ダイアログを開きます。
    
    ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[OK]** をクリックします。

1. **name** 属性で以下の手順を実行します。

    a. 属性をクリックして、**[属性の編集]** ウィンドウを開きます。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/url_update.png)

    b. **namespace** から URL の値を削除します。
    
    c. **[OK]** をクリックして設定を保存します。

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![シングル サインオンの構成](./media/linkedinsalesnavigator-tutorial/tutorial_general_400.png)

1. **[LinkedIn Admin Settings (LinkedIn 管理者設定)]** セクションに移動します。 **[Upload XML file (XML ファイルのアップロード)]** をクリックして、Azure Portal からダウンロードしたメタデータ XML ファイルをアップロードします。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. **[ON (オン)]** をクリックして SSO を有効にします。 SSO の状態が **[Not Connected (未接続)]** から **[Connected (接続済み)]** に変更されます

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。
    
    ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>LinkedIn Sales Navigator テスト ユーザーの作成

Linked Sales Navigator アプリケーションでは、ジャストインタイム (JIT) ユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーション内に自動的に作成されます。 **[Automatically assign licenses (ライセンスを自動的に割り当てる)]** をアクティブ化して、ユーザーにライセンスを割り当てます。
   
   ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LinkedIn Sales Navigator へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を LinkedIn Sales Navigator に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[LinkedIn Sales Navigator]** を選択します。

    ![Configure single sign-on](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [LinkedIn Sales Navigator] タイルをクリックすると、個人用の LinkedIn アカウントの詳細を指定した [Organizational (組織)] ページにリダイレクトします。 個人アカウントが LinkedIn ビジネス アカウントにリンクされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事をご覧ください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_203.png

