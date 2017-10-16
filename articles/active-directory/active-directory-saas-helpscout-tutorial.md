---
title: "チュートリアル: Azure Active Directory と Help Scout の統合 | Microsoft Docs"
description: "Azure Active Directory と Help Scout の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: fe8775bd39173b2adf1f82d32f5e851ef1c19931
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>チュートリアル: Azure Active Directory と Help Scout の統合

このチュートリアルでは、Help Scout と Azure Active Directory (Azure AD) を統合する方法について説明します。

Help Scout と Azure AD の統合には、次の利点があります。

- Help Scout にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Help Scout にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Help Scout と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Help Scout でのシングル サインオンが有効なサブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Help Scout の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-help-scout-from-the-gallery"></a>ギャラリーからの Help Scout の追加
Azure AD への Help Scout の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Help Scout を追加する必要があります。

**ギャラリーから Help Scout を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Help Scout**」と入力し、結果パネルで **Help Scout** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Help Scout で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Help Scout ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Help Scout の関連ユーザーの間で、リンク関係が確立されている必要があります。

Help Scout ではログインにメール アドレスが使用されます。したがって、リンク関係を確立するには、Azure AD で、その**メール アドレス**を**ユーザー名**として使用します。

Help Scout で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Help Scou テスト ユーザーの作成](#create-a-help-scout-test-user)** - Help Scout で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Help Scout アプリケーションでシングル サインオンを構成します。

**Help Scout で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Help Scout** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. アプリケーションを **IDP** 開始モードで構成する場合は、**[Help Scout のドメインと URL]** セクションで次の手順を実行します。

    ![[Help Scout のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

    a. **識別子**は、Help Scout の "**対象ユーザー URI (サービス プロバイダーのエンティティ ID)**" で、先頭は `urn:` です

    b. **応答 URL** は、Help Scout の "**ポスト バック URL (Assertion Consumer Service URL)**" で、先頭は `https://` です 

    > [!NOTE] 
    > これらの URL の値は、単なる例です。 これらの値は、実際の応答 URL と識別子で更新する必要があります。 この値は、[認証] セクションの **[シングル サインオン]** タブから取得します。これについては後で説明します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにします。

    ![[Help Scout のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)

    **[サインオン URL]** ボックスに、「`https://secure.helpscout.net/members/login/`」と入力します。
     
5. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)


7. **[Help Scout 構成]** セクションで、**[Help Scout の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-helpscout-tutorial/config.png) 

8. 別の Web ブラウザー ウィンドウで、Help Scout 企業サイトに管理者としてログインします。

9. ログインしたら、上部のメニューで **[管理]** をクリックし、ドロップダウン メニューから **[会社]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-helpscout-tutorial/settings1.png) 
 
10. 左側のメニューで **[認証]** を選択します。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-helpscout-tutorial/settings2.png) 

11. [SAML 設定] セクションが表示されます。ここで次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-helpscout-tutorial/settings3.png) 
 
    a. **[Post-back URL (Assertion Consumer Service URL)]\(ポスト バック URL (Assertion Consumer Service URL)\)** の値をコピーし、Azure Portal の **[Help Scout のドメインと URL]** セクションの **[応答 URL]** ボックスに貼り付けます。
    
    b. **[Audience URI (Service Provider Entity ID)]\(対象ユーザー URI (サービス プロバイダーのエンティティ ID)\)** の値をコピーし、Azure Portal の **[Help Scout のドメインと URL]** セクションの **[識別子]** ボックスに貼り付けます。

12. **[SAML を有効にする]** をオンにして、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-helpscout-tutorial/settings4.png) 
 
    a. **[シングル サインオン URL]** ボックスに、Azure Portal からコピーした**シングル サインオン サービス URL** の値を貼り付けます。
    
    b. **[証明書のアップロード]** をクリックして、Azure Portal からダウンロードした**証明書 (Base64)** をアップロードします。

    c. **[メール ドメイン]** ボックスに、組織のメール ドメイン (例: `contoso.com`) を入力します。 複数のドメインを指定する場合は、コンマで区切ります。 Help Scout ユーザーまたは管理者が [Help Scout ログイン ページ](https://secure.helpscout.net/members/login/)で特定のドメインを入力すると必ず、その資格情報で認証するために ID プロバイダーにルーティングされます。

    d. 最後に、ユーザーがこの方法以外で Help Scout にログオンできないようにする場合は、**[Force SAML Sign-on]\(強制 SAML サインオン\)** の設定を切り替えてオンにします。 Help Scout 資格情報でも引き続きサインインできるようにする場合は、この設定をオフのままにします。 これを有効にしても、アカウント所有者は、いつでも自身のアカウント パスワードで Help Scout にログインにします。

    e. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-help-scout-test-user"></a>Help Scout テスト ユーザーの作成

このセクションの目的は、Help Scout で Britta Simon というユーザーを作成することです。 Help Scout では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Help Scout に存在しない場合は、Help Scout にアクセスしようとしたときに新しいユーザーが作成されます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Help Scout へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Help Scout に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Help Scout]** を選択します。

    ![アプリケーションの一覧の Help Scout のリンク](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Help Scout] タイルをクリックすると、自動的に Help Scout アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png

