---
title: "チュートリアル: Azure Active Directory と PlanMyLeave の統合 | Microsoft Docs"
description: "Azure Active Directory と PlanMyLeave の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 83334b1b02df214e51c86a862636c9392cd19474
ms.openlocfilehash: ba418a641b339a0d94a3c7b2596d37fbd88a30c5
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>チュートリアル: Azure Active Directory と PlanMyLeave の統合

このチュートリアルでは、PlanMyLeave と Azure Active Directory (Azure AD) を統合する方法について説明します。

PlanMyLeave と Azure AD の統合には、次の利点があります。

- PlanMyLeave にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に PlanMyLeave にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

PlanMyLeave と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- PlanMyLeave でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の試用版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの PlanMyLeave の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-planmyleave-from-the-gallery"></a>ギャラリーからの PlanMyLeave の追加
Azure AD への PlanMyLeave の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PlanMyLeave を追加する必要があります。

**ギャラリーから PlanMyLeave を追加するには、次の手順に従います。**

1. **[Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**PlanMyLeave**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_001.png)

5. 結果ウィンドウで **[PlanMyLeave]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、PlanMyLeave で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する PlanMyLeave ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと PlanMyLeave の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、PlanMyLeave の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

PlanMyLeave で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[PlanMyLeave テスト ユーザーの作成](#creating-a-planmyleave-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを PlanMyLeave で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、PlanMyLeave アプリケーションでシングル サインオンを構成します。

**PlanMyLeave で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **PlanMyLeave** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログ ページで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_01.png)

3. **[PlanMyLeave のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_02.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、`https://<company-name>.planmyleave.com/Login.aspx` のパターンを使用して URL を入力します。
    
    b. **[識別子]** ボックスに、`https://<company-name>.planmyleave.com` のパターンで URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子でこれらの値を更新する必要があります。 これらの値を取得するには、[PlanMyLeave サポート チーム](mailto:support@planmyleave.com)に問い合わせてください。

4. **[SAML 署名証明書]** セクションで、**[新しい証明書の作成]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_03.png)     

5. **[新しい証明書の作成]** ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_300.png)

6. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_04.png)

7. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_400.png)

8. **[SAML 署名証明書]** セクションで、**[Certificate (base64) (証明書 (base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_05.png) 

9. **[PlanMyLeave Configuration (PlanMyLeave 構成)]** セクションで、**[Configure PlanMyLeave (PlanMyLeave を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_06.png) 

    ![[シングル サインオンの構成]](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_07.png)

10. 別の Web ブラウザーのウィンドウで、管理者として PlanMyLeave テナントにログインします。

11. **[System Setup (システム セットアップ)]** に移動します。 次に、**[セキュリティ管理]** セクションで、**[Company SAML settings (会社の SAML 設定)]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_002.png) 

12. **[SAML 設定]** セクションで、エディターのアイコンをクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_003.png)

13. **[Update SAML Settings (SAML 設定の更新)]** セクションで、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  **[Login URL (ログイン URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-on Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    b.  ダウンロードした証明書ファイルをメモ帳で開き、その内容 (---Begin Certificate--- と ---End certificate---- の間) をクリップボードにコピーし、**[証明書]** テキスト ボックスに貼り付けます。

    c. **[Is Enable (有効)]** を **[はい]** に設定します。

    d. **[保存]**をクリックします。



### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。 



### <a name="creating-a-planmyleave-test-user"></a>PlanMyLeave テスト ユーザーの作成

このセクションの目的は、PlanMyLeave で Britta Simon というユーザーを作成することです。 PlanMyLeave では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない PlanMyLeave ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[PlanMyLeave のサポート チーム](mailto:support@planmyleave.com)にお問い合わせください。



### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に PlanMyLeave へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**PlanMyLeave に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[PlanMyLeave]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_50.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [PlanMyLeave] タイルをクリックすると、自動的に PlanMyLeave アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_203.png
