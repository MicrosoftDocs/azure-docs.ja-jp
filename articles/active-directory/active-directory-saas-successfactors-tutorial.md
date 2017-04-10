---
title: "チュートリアル: Azure Active Directory と SuccessFactors の統合 | Microsoft Docs"
description: "Azure Active Directory で SuccessFactors を使用してシングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: a0a101c34ffd4adb6816d9db8313d10b0ffbb3ae
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>チュートリアル: Azure Active Directory と SuccessFactors の統合
このチュートリアルの目的は、SuccessFactors と Azure Active Directory (Azure AD) を統合する方法を説明することです。

SuccessFactors と Azure AD の統合には、次の利点があります。

* SuccessFactors にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に SuccessFactors にサインオン (シングル サインオン) できるように、設定が可能です。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
SuccessFactors と Azure AD の統合を構成するには、次のものが必要です。

* 有効な Azure サブスクリプション
* SuccessFactors のテナント

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルの目的は、テスト環境で Azure AD のシングル サインオンをテストできるようにすることです。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの SuccessFactors の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-successfactors-from-the-gallery"></a>ギャラリーからの SuccessFactors の追加
Azure AD への SuccessFactors の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SuccessFactors を追加する必要があります。

**ギャラリーから SuccessFactors を追加するには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![シングル サインオンの構成][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![シングル サインオンの構成][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![シングル サインオンの構成][4]
6. **検索ボックス**に、「**SuccessFactors**」と入力します。
   
    ![シングル サインオンの構成][5]
7. 結果ウィンドウで **[SuccessFactors]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![シングル サインオンの構成][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションの目的は、"Britta Simon" というテスト ユーザーに基づいて、SuccessFactors で Azure AD のシングル サインオンを構成し、テストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SuccessFactors ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SuccessFactors の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、SuccessFactors の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

SuccessFactors で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SuccessFactors のテスト ユーザーの作成](#creating-a-successfactors-test-user)** - SuccessFactors で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、SuccessFactors アプリケーションでシングル サインオンを構成します。

**SuccessFactors で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **SuccessFactors** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成][7]
2. **[ユーザーの SuccessFactors へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成][8]
3. **[アプリケーション URL の構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成][9]
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[サインオン URL]** ボックスに、次のいずれかの形式で URL を入力します。 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
   
    b. **[応答 URL]** ボックスに、次のいずれかの形式で URL を入力します。 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
    | `https://<company name>.sapsf.eu/<company name>` |
   
    c. **[次へ]**をクリックします。 

    > [!NOTE]
    > これは実際の値ではないので注意してください。 実際のサインオン URL および応答 URL で値を更新する必要があります。 これらの値を取得するには、 [SuccessFactors サポート チーム](https://www.successfactors.com/en_us/support.html)に問い合わせてください。

1. **[SuccessFactors でのシングル サインオンの構成]** ページで **[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターにローカルで保存します。
   
    ![シングル サインオンの構成][10]

2. 別の Web ブラウザー ウィンドウで、 **SuccessFactors 管理者ポータル** に管理者としてログインします。

3. **[アプリケーション セキュリティ]** で **[Single Sign On Feature (シングル サインオン機能)]** に移動します。 

4. **[Reset Token (リセット トークン)]** に任意の値を入力し、**[Save Token (トークンの保存)]** をクリックして、SAML SSO を有効にします。
   
    ![Configuring single sign-on on app side][11]

    > [!NOTE] 
    > この値は、単にオン/オフのスイッチとして使用されます。 任意の値を保存すると、SAML SSO はオンになります。 値が空白のまま保存すると、SAML SSO はオフになります。

1. 次のスクリーンショットの画面に移動して次の操作を実行します。
   
    ![Configuring single sign-on on app side][12]
   
    a. **[SAML v2 SSO]** オプションをクリックします。
   
    b. [SAML Asserting Party Name (SAML アサーティング パーティ名)] を設定します (例: SAML 発行者 + 会社名)。
   
    c. **[SAML Issuer (SAML 発行者)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[発行者の URL]** の値を入力します。
   
    d. **[Require Mandatory Signature (必須の署名の要求)]** で **[Response(Customer Generated/IdP/AP) (応答 (顧客生成/IdP/AP))]** を選択します。
   
    e. **[Enable SAML Flag (SAML フラグを有効にする)]** で **[Enabled (有効にする)]** を選択します。
   
    f. **[Login Request Signature(SF Generated/SP/RP) (ログイン要求署名 (SF 生成/SP/RP))]** で **[No (いいえ)]** を選択します。
   
    g. **[SAML Profile (SAML プロファイル)]** で **[Browser/Post Profile (Browser/Post プロファイル)]** を選択します。
   
    h. **[Enforce Certificate Valid Period (証明書の有効期間を適用する)]** で **[No (いいえ)]** を選択します。
   
    i. ダウンロードした証明書ファイルのコンテンツをコピーし、**[SAML Verifying Certificate (SAML で確認する証明書)]** ボックスに貼り付けます。

    > [!NOTE] 
    > この証明書には、証明書の開始タグおよび終了タグが必要です。

1. [SAML V2] に移動して、次の手順に従います。
   
    ![Configuring single sign-on on app side][13]
   
    a. **[Support SP-initiated Global Logout (SP によって開始されたグローバル ログアウトのサポート)]** で **[Yes (はい)]** を選択します。
   
    b. **[Global Logout Service URL (LogoutRequest destination) (グローバル ログアウト サービス URL (LogoutRequest の宛先))]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[リモート ログアウト URL]** の値を入力します。
   
    c. **[Require sp must encrypt all NameID element (すべての NameID 要素で SP での暗号化を要求)]** で **[No (いいえ)]** を選択します。
   
    d. **[NameID Format (NameID の形式)]** で **[unspecified (未指定)]** を選択します。
   
    e. **[Enable sp initiated login (AuthnRequest) (SP によって開始されたログインを有効にする (AuthnRequest))]** で **[Yes (はい)]** を選択します。
   
    f. **[Send request as Company-Wide issuer (全社的な発行者として要求を送信する)]** ボックスに、Azure AD アプリケーションの構成ウィザードの **[リモート ログイン URL]** の値を入力します。
2. 大文字と小文字を区別しないログイン ユーザー名を作成する場合、次の手順を実行します。
   
    a. 下部の **[Company Settings (会社設定)]** に移動します。
   
    b. **[Enable Non-Case-Sensitive Username (大文字と小文字を区別しないユーザー名)]**の横のチェック ボックスをオンにします。
   
    c. **[Save (保存)]** をクリックします。
   
    ![[シングル サインオンの構成]][29]

    > [!NOTE] 
    > これを有効にしようとすると、システムによって、重複する SAML ログイン名が作成されるかどうかが確認されます。 たとえば、顧客が User1 および user1 というユーザー名を持っている場合などです。 大文字と小文字の区別をしないと、これらの重複が発生します。 システムによってエラー メッセージが表示され、機能は有効になりません。 顧客はユーザー名の一方を、違うスペルになるように変更する必要があります。 

1. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![アプリケーション][14]
2. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。
   
    ![アプリケーション][15]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、クラシック ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][16]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成][17]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成][18]
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成][19]
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成][20]
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成][21]
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
    b. **[姓]** ボックスに「**Simon**」と入力します。
   
    c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
    d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
    e. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成][22]
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成][23]
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. ページの下部にある **[完了]**」を参照してください。  

### <a name="creating-a-successfactors-test-user"></a>SuccessFactors のテスト ユーザーの作成
Azure AD ユーザーが SuccessFactors にログインできるようにするには、ユーザーを SuccessFactors にプロビジョニングする必要があります。  
SuccessFactors の場合、プロビジョニングは手動で行います。

SuccessFactors でユーザーを作成するには、 [SuccessFactors のサポート チーム](https://www.successfactors.com/en_us/support.html)に連絡する必要があります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションの目的は、Britta Simon に SuccessFactors へのアクセスを許可することで、このユーザーが Azure のシングル サインオンを使用できるようにすることです。

![ユーザーの割り当て][24]

**SuccessFactors に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][25]
2. アプリケーションの一覧で **[SuccessFactors]**を選択します。
   
    ![Configure Single Sign-On][26]
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][27]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][28]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [SuccessFactors] タイルをクリックすると、自動的に SuccessFactors アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_00.png
[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_01.png
[6]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_02.png
[7]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_03.png
[8]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_04.png
[9]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_05.png
[10]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_06.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png

[16]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_00.png
[17]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png
[18]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png
[19]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png
[20]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png
[21]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_05.png
[22]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_06.png
[23]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_07.png

[24]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_07.png
[25]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_08.png
[26]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_11.png
[27]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_09.png
[28]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_10.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

