---
title: "チュートリアル: Azure Active Directory と Infor Retail - Information Management の統合 | Microsoft Docs"
description: "Azure Active Directory と Infor Retail - Information Management の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5ff49168-ef81-4169-8e5e-dc86e24dd5e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ad9c78483af88a85a1b790269231683155760d76
ms.openlocfilehash: aef560c2b05a80c59743c4cc6d68d6d2c14bdc3f
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-infor-retail--information-management"></a>チュートリアル: Azure Active Directory と Infor Retail - Information Management の統合

このチュートリアルでは、Infor Retail - Information Management と Azure Active Directory (Azure AD) を統合する方法について説明します。

Infor Retail - Information Management と Azure AD の統合には、次の利点があります。

- Infor Retail - Information Management にアクセスできるユーザーを Azure AD で制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Infor Retail - Information Management にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Infor Retail - Information Management と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Infor Retail - Information Management でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の試用版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの Infor Retail - Information Management の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-infor-retail--information-management-from-the-gallery"></a>ギャラリーからの Infor Retail - Information Management の追加
Azure AD への Infor Retail - Information Management の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Infor Retail – Information Management を追加する必要があります。

**ギャラリーから Infor Retail - Information Management を追加するには、次の手順を実行します。**

1. **[Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Infor Retail - Information Management**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_001.png)

5. 結果ウィンドウで **[Infor Retail - Information Management]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Infor Retail - Information Management で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Infor Retail - Information Management ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Infor Retail - Information Management 内の対応するユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Infor Retail - Information Management の **[Username]** の値として割り当てます。

Infor Retail – Information Management で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD シングル サインオンをテストします。
3. **[Infor Retail - Information Management テスト ユーザーの作成](#creating-an-infor-retail---information-management-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Infor Retail - Information Management で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、Infor Retail - Information Management アプリケーションでシングル サインオンを構成します。

**Infor Retail - Information Management との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure 管理ポータルの **Infor Retail - Information Management** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログ ページで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_01.png)

3. **[Infor Retail - Information Management のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_02.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[識別子]** ボックスに、`https://<company name>.mingle.infor.com` の形式で URL を入力します。
    
    b. **[応答 URL]** ボックスに、`https://<company name>.mingle.infor.com/sp/ACS.saml2` のパターンを使用して URL を入力します。
    
4. **SP 開始モード**でアプリケーションを構成する場合は、**[Infor Retail - Information Management のドメインと URL]** セクションで、次の手順を実行します。
    
    ![Configure Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_03.png)

    a. **[詳細な URL 設定の表示]** をクリックします。

    b. **[サインオン URL]** ボックスに、`https://<company name>.mingle.infor.com/<company code>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際のサインオン URL、識別子、および応答 URL で値を更新する必要があります。 [Infor Retail – Information Management サポート チーム](mailto:innovate@infor.com)に問い合わせて、これらの値を入手してください。

5. **[SAML 署名証明書]** セクションで、**[新しい証明書の作成]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_04.png)     

6. **[新しい証明書の作成]** ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_300.png)

7. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_05.png)

8. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_400.png)

9. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_06.png) 

10. アプリケーション用に構成された SSO を入手するには、[Infor Retail - Information Management サポート チーム](mailto:innovate@infor.com)に連絡して、ダウンロードした**メタデータ**を提供してください。
  

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-inforretailinformationmanagement-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-an-infor-retail--information-management-test-user"></a>Infor Retail - Information Management テスト ユーザーの作成

このセクションでは、Infor Retail - Information Management で Britta Simon というユーザーを作成します。 [Infor Retail - Information Management サポート チーム](mailto:innovate@infor.com)と連携し、Infor Retail - Information Management プラットフォームにユーザーを追加してください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Infor Retail - Information Management へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Infor Retail - Information Management に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[Infor Retail – Information Management]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Infor Retail - Information Management] タイルをクリックすると、自動的に Infor Retail - Information Management アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-inforretailinformationmanagement-tutorial/tutorial_general_203.png
