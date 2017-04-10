---
title: "チュートリアル: Azure Active Directory と HackerOne の統合 | Microsoft Docs"
description: "Azure Active Directory と HackerOne の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2fa331e94425673481689aefeb5393618f2471b9
ms.openlocfilehash: ced5f0fc2d9afa22f2d3ef3a81bb07482f69ec0e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>チュートリアル: Azure Active Directory と HackerOne の統合
このチュートリアルでは、HackerOne と Azure Active Directory (Azure AD) を統合します。

HackerOne と Azure AD の統合には、次の利点があります。

* HackerOne にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで自動的に HackerOne にシングル サインオン (SSO) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Azure AD と HackerOne の統合を構成するには、次のものが必要です。

* Azure サブスクリプション
* HackerOne での SSO が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。  

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

*  ギャラリーからの HackerOne の追加
*  Azure AD SSO の構成とテスト

## <a name="add-hackerone-from-the-gallery"></a>ギャラリーからの HackerOne の追加
HackerOne を Azure AD に統合するには、ギャラリーから管理対象 SaaS アプリの一覧に HackerOne を追加する必要があります。

**ギャラリーから HackerOne を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

  ![アプリケーション][4]
6. 検索ボックスに、「 **HackerOne**」と入力します。

  ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

7. 結果ウィンドウで **[HackerOne]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
次に、"Britta Simon" というテスト ユーザーに基づいて、HackerOne で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する HackerOne ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと HackerOne の関連ユーザーの間で、リンク関係が確立されている必要があります。  
このリンク関係は、Azure AD の **[ユーザー名]** の値を、HackerOne の **[Username]** の値として割り当てることで確立されます。

HackerOne で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[HackerOne テスト ユーザーの作成](#creating-a-hackerone-test-user)** - Certify で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
次に、クラシック ポータルで Azure AD のシングル サインオンを有効にして、HackerOne アプリケーションでシングル サインオンを構成します。

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**HackerOne で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **HackerOne** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの HackerOne へのアクセスを設定してください]** ページで、**[Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) 
    1. **[サインオン URL]** ボックスに、ユーザーが HackerOne アプリケーションへのサインオンに使用する URL を入力します。その際、**"https://hackerone.com/\<企業名\>/authentication"** というパターンを使用します。 
    2. テナントの URL がわからない場合は、HackerOne サポート チーム ([support@hackerone.com](mailto:support@hackerone.com)) にお問い合わせください。
    3. **[識別子]** ボックスに、テナントの URL を入力します。 
    4. **[次へ]**をクリックします。

4. **[HackerOne シングル サインオン パラメーターの構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) 
    1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
    2. **[次へ]**をクリックします。
5. HackerOne テナントに管理者としてサインオンします。
6. 上部のメニューで **[設定]**をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 
7. **[認証]** に移動し、**[Add SAML settings (SAML 設定の追加)]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 
8. **[SAML Settings (SAML の設定)]** ダイアログで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 
    1. 登録されているドメインを **[電子メール ドメイン]** ボックスに入力します。
    2. Azure クラシック ポータルで、**[シングル サインオン サービス URL]** をコピーし、[シングル サインオン URL] ボックスに貼り付けます。
    3. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。  
    
       >[!TIP] 
       >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。
       >
    4. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[X509 Certificate]** ボックスに貼り付けます。
    5. **[保存]**をクリックします。
9. [Authentication Settings] ダイアログで、次の手順を実行します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 
    1. **[テストの実行]** をクリックします。
    2. **[Status (状態)]** フィールドの値が **[Last test status: created (最後のテストの状態: 作成済み)]** と同じ場合は、HackerOne サポート チーム ([support@hackerone.com](mailto:support@hackerone.com)) に構成のレビューをご依頼ください。
10. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
   
    ![Azure AD のシングル サインオン][10]
11. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
   
    ![Azure AD のシングル サインオン][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
次に、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。  

![Azure AD ユーザーの作成][20]

**Azure AD で Secure Deliver テスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) 
    1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
    2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
    3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) 
   1. **[名]** ボックスに「**Britta**」と入力します。  
   2. **[姓]** ボックスに「**Simon**」と入力します。
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) 
    1. **[新しいパスワード]** の値を書き留めます。
    2. ページの下部にある **[完了]**」を参照してください。   

### <a name="create-a-hackerone-test-user"></a>HackerOne テスト ユーザーの作成
次に、HackerOne で Britta Simon というユーザーを作成します。 HackerOne では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 HackerOne にアクセスすると、新しいユーザーが作成されます (まだ作成されていない場合)。 [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、Certify のサポート チームにお問い合わせください。 
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
次に、Britta Simon に HackerOne へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザーの割り当て][200] 

**HackerOne に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[HackerOne]**を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
最後に、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。  

アクセス パネルで [HackerOne] タイルをクリックすると、自動的に HackerOne アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png

