---
title: "チュートリアル: Azure での Azure Active Directory と Google Apps の統合 | Microsoft Docs"
description: "Azure Active Directory と Google Apps の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 065841d6b4fe50e953f01bba4d3f23de82b82726
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>チュートリアル: Azure Active Directory と Google Apps の統合

このチュートリアルでは、Google Apps と Azure Active Directory (Azure AD) を統合する方法について説明します。

Google Apps と Azure AD の統合には、次の利点があります。

- Google Apps にアクセスする Azure AD ユーザーを制御できます。
- ユーザーは自分の Azure AD アカウントで自動的に Google Apps にサインオン (シングル サインオン) できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Google Apps と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Google Apps でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="video-tutorial"></a>ビデオ チュートリアル
2 分間で Google Apps へのシングル サインオンを有効にする方法:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]

## <a name="frequently-asked-questions"></a>よく寄せられる質問
1. **Q: Chromebook とその他の Chrome デバイスは、Azure AD シングル サインオンと互換性がありますか。**
   
    A: はい。ユーザーは Azure AD の資格情報を使用して、Chromebook デバイスにサインインすることができます。 ユーザーに資格情報の入力を求めるメッセージが 2 回表示される場合がある理由については、[Google Apps のこちらのサポート記事](https://support.google.com/chrome/a/answer/6060880)をご覧ください。

2. **Q: シングル サインオンを有効にした場合、ユーザーは Google Classroom、GMail、Google Drive、YouTube などの Google 製品にサインインするために Azure AD 資格情報を使用できますか。**
   
    A: はい。[Google アプリ](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)ごとに、組織で有効にするか無効にするかを選択します。

3. **Q: Google Apps ユーザーの一部だけに対して、シングル サインオンを有効にできますか。**
   
    A: いいえ。シングル サインオンを有効にすると、直ちにすべての Google Apps ユーザーが Azure AD 資格情報での認証を要求されるようになります。 Google Apps は複数の ID プロバイダーをサポートしていないため、Google Apps 環境の ID プロバイダーは Azure AD か Google であり、同時に両方を設定することはできません。

4. **Q: Windows でサインインしたユーザーは、パスワードの入力を求められることなく、自動的に Google Apps に認証されますか。**
   
    A: このシナリオを有効にするには、2 つのオプションがあります。 まず、ユーザーは [Azure Active Directory 参加](active-directory-azureadjoin-overview.md)を通じて Windows 10 デバイスにサインインできます。 また、ユーザーは、 [Active Directory フェデレーション サービス (AD FS)](active-directory-aadconnect-user-signin.md) デプロイを通じて Azure AD へのシングル サインオンが有効になっているオンプレミスの Active Directory にドメイン参加している Windows デバイスにサインインすることもできます。 どちらのオプションでも、以下のチュートリアルの手順に従って、Azure AD と Google Apps との間のシングル サインオンを有効にする必要があります。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Google Apps の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-google-apps-from-the-gallery"></a>ギャラリーからの Google Apps の追加
Azure AD への Google Apps の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから Google Apps を追加する必要があります。

**ギャラリーから Google Apps を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Google Apps**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_search.png)

5. 結果ウィンドウで **[Google Apps]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Google Apps で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Google Apps ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Google Apps の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値に Google Apps の **[ユーザー名]** の値を割り当てます。

Google Apps で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Google Apps テスト ユーザーの作成](#creating-a-google-apps-test-user)** - Google Apps で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Google Apps アプリケーションでシングル サインオンを構成します。

**Google Apps で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Google Apps** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_samlbase.png)

3. **[Google Apps Domain and URLs]\(Google Apps のドメインと URL\)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_url.png)

    **[サインオン URL]** ボックスに、`https://mail.google.com/a/<yourdomain>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 [Google サポート チーム](https://www.google.com/contact/)にお問い合わせください。
 
4. **[SAML 署名証明書]** セクションで、**[証明書]** をクリックし、コンピューターに証明書を保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-google-apps-tutorial/tutorial_general_400.png)

6. **[Google Apps Configuration]\(Google Apps 構成\)** セクションで、**[Configure Google Apps]\(Google Apps を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL、SAML シングル サインオン サービス URL、パスワードの URL の変更**をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_configure.png) 

7. ブラウザーで新しいタブを開き、管理者アカウントを使用して、 [Google Apps の管理コンソール](http://admin.google.com/) にサインインします。

8. **[セキュリティ]**をクリックします。 このリンクが表示されていない場合、画面下部の **[その他の設定]** に隠れていることがあります。
   
    ![Click Security.][10]

9. **[セキュリティ]** ページで、**[シングル サインオン (SSO) の設定]** をクリックします。
   
    ![Click SSO.][11]

10. 次の構成の変更を実行します。
   
    ![Configure SSO][12]
   
    a. **[Setup SSO with third-party identity provider]\(サード パーティの ID プロバイダーで SSO を設定する\)** を選択します。

    b. Google Apps の **[サインイン ページの URL]** フィールドに、Azure Portal からコピーした **シングル サインオン サービス URL** の値を貼り付けます。

    c. Google Apps の **[サインアウト ページの URL]** フィールドに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。 

    d. Google Apps の **[パスワードの URL の変更]** フィールドに、Azure Portal からコピーした **パスワードの URL の変更** の値を貼り付けます。 

    e. Google Apps の **[検証証明書]** に、Azure Portal からダウンロードした証明書をアップロードします。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[変更を保存]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
 
### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-google-apps-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-google-apps-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-google-apps-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-google-apps-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある「**[Create]**」を参照してください。
 
### <a name="creating-a-google-apps-test-user"></a>Google Apps のテスト ユーザーの作成

このセクションの目的は、Google Apps ソフトウェアで Britta Simon というユーザーを作成することです。 Google Apps では、自動プロビジョニングがサポートされており、これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Google Apps ソフトウェアに存在しない場合は、Google Apps ソフトウェアにアクセスしようとしたときに新しいユーザーが作成されます。

>[!NOTE] 
>ユーザーを手動で作成する必要がある場合は、[Google サポート チーム](https://www.google.com/contact/)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Google Apps へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Google Apps に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Google Apps]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、シングル サインオンの設定をテストするために、アクセス パネル ([https://myapps.microsoft.com](active-directory-saas-access-panel-introduction.md)) を開き、テスト アカウントにサインインし、アクセス パネルで **[Google Apps]** タイルをクリックします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [ユーザー プロビジョニングの構成](active-directory-saas-google-apps-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_203.png

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png

[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png
