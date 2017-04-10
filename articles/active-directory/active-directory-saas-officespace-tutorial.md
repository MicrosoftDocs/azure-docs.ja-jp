---
title: "チュートリアル: Azure Active Directory と OfficeSpace Software の統合 | Microsoft Docs"
description: "Azure Active Directory と OfficeSpace Software の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: fb61a2d622b0d829a134b6ce5dfef6e9fb44fa1e
ms.lasthandoff: 03/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>チュートリアル: Azure Active Directory と OfficeSpace Software の統合

このチュートリアルでは、OfficeSpace Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

OfficeSpace Software と Azure AD の統合には、次の利点があります。

- OfficeSpace Software にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に OfficeSpace Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

OfficeSpace Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン対応の OfficeSpace Software サブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの OfficeSpace Software の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-officespace-software-from-the-gallery"></a>ギャラリーからの OfficeSpace Software の追加
Azure AD への OfficeSpace Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OfficeSpace Software を追加する必要があります。

**ギャラリーから OfficeSpace Software を追加するには、次の手順に従います。**

1. **[Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**OfficeSpace Software**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_001.png)

5. 結果ウィンドウで **[OfficeSpace Software]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、OfficeSpace Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する OfficeSpace Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと OfficeSpace Software の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を OfficeSpace Software の **[Username (ユーザー名)]** の値として割り当てます。

OfficeSpace Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[OfficeSpace Software のテスト ユーザーの作成](#creating-an-officespace-software-test-user)** - OfficeSpace Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、OfficeSpace Software アプリケーションでシングル サインオンを構成します。

**OfficeSpace Software で Azure AD のシングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **OfficeSpace Software** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure Single Sign-On][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_01.png)

3. **[OfficeSpace Software のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_02.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://<company name>.officespacesoftware.com/users/sign_in/saml` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`<company name>.officespacesoftware.com` の形式で値を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子でこれらの値を更新する必要があります。 これの値の取得については、[OfficeSpace Software サポート チーム](mailto:support@officespacesoftware.com)にお問い合わせください。 

4. OfficeSpace Software アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_03.png)

5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、**[ユーザー識別子]** として **[user.mai]l** を選択し、以下の表に示す行ごとに、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | --- | --- |    
    | 電子メール | User.mail |
    | name | user.displayname |
    | first_name | User.givenname |
    | last_name | User.surname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

6. **[SAML 署名証明書]** セクションで、**[Certificate (base64) (証明書 (base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_08.png) 

7. **[Save]**をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_general_400.png)

8. **[OfficeSpace Software Configuration (OfficeSpace Software 構成)]** セクションで **[Configure OfficeSpace Software (OfficeSpace Software の構成)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_09.png) 

    ![[シングル サインオンの構成]](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_10.png)

9. 別の Web ブラウザーのウィンドウで、管理者として OfficeSpace Software テナントにログインします。

10. **[設定]** に移動して、**[コネクタ]**をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

11. **[SAML 認証]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

12. **[SAML Authentication]** セクションで、次の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. **[Logout provider url (ログアウト プロバイダー URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[サインアウト URL]** の値を入力します。

    b. **[Client idp target url (クライアント idp ターゲット URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-on Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    c. ダウンロードした証明書から **[拇印]** の値をコピーして、**[Client idp cert fingerprint (クライアント IdP 証明書の指紋)]** ボックスに貼り付けます。 

    d. **[設定の保存]**をクリックします。

    > [!NOTE]
    > 詳細については、「 [How to retrieve a certificate's thumbprint value (証明書のサムプリント値を取得する方法)](http://youtu.be/YKQF266SAxI) 
  

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-an-officespace-software-test-user"></a>OfficeSpace Software のテスト ユーザーの作成

このセクションの目的は、OfficeSpace Software で Britta Simon というユーザーを作成することです。 OfficeSpace Software では、ジャスト イン タイム プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーが存在しない場合は、OfficeSpace Software へのアクセスを試みたときに、新しいユーザーが自動的に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[OfficeSpace Software サポート チーム](mailto:support@officespacesoftware.com)にお問い合わせください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に OfficeSpace Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**OfficeSpace Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[OfficeSpace Software]** を選択します。

    ![Configure Single Sign-On](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [OfficeSpace Software] タイルをクリックすると、自動的に OfficeSpace Software アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png
