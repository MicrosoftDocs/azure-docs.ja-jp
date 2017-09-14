---
title: "チュートリアル: Azure Active Directory と SAP Cloud for Customer の統合 | Microsoft Docs"
description: "Azure Active Directory と SAP Cloud for Customer の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e4d945525a45704f34e1d9e742220928a516f341
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>チュートリアル: Azure Active Directory と SAP Cloud for Customer の統合

このチュートリアルでは、SAP Cloud for Customer と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAP Cloud for Customer と Azure AD の統合には、次の利点があります。

- SAP Cloud for Customer にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SAP Cloud for Customer にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAP Cloud for Customer の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SAP Cloud for Customer でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから SAP Cloud for Customer を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>ギャラリーから SAP Cloud for Customer を追加する
Azure AD への SAP Cloud for Customer の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Cloud for Customer を追加する必要があります。

**ギャラリーから SAP Cloud for Customer を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **SAP Cloud for Customer**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

5. 結果パネルで **[SAP Cloud for Customer]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAP Cloud for Customer で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAP Cloud for Customer ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP Cloud for Customer の関連ユーザーの間で、リンク関係が確立されている必要があります。

SAP Cloud for Customer で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

SAP Cloud for Customer で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SAP Cloud for Customer のテスト ユーザーの作成](#creating-a-sap-cloud-for-customer-test-user)** - SAP Cloud for Customer で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SAP Cloud for Customer アプリケーションでシングル サインオンを構成します。

**SAP Cloud for Customer で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SAP Cloud for Customer** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

3. **[SAP Cloud for Customer のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. **[サインオン URL]** ボックスに、`https://<server name>.crm.ondemand.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<server name>.crm.ondemand.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[SAP Cloud for Customer クライアント サポート チーム](https://www.sap.com/about/agreements.sap-cloud-services-customers.html)に連絡してください。 

4. **[User Attributes (ユーザー属性)]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. **[ユーザー識別子]** リストで、**[ExtractMailPrefix()]** 関数を選択します。

    b. **[メール]** 一覧から、実装で使用するユーザー属性を選択します。
    たとえば、一意のユーザー識別子として EmployeeID を使用し、その属性値を ExtensionAttribute2 に保存している場合、[user.extensionattribute2] を選択します。  

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_400.png)

7. **[SAP Cloud for Customer Configuration (SAP Cloud for Customer 構成)]** セクションで、**[Configure SAP Cloud for Customer (SAP Cloud for Customer を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

8. 構成された SSO を取得するには、次の手順を実行します。
   
    a. SAP Cloud for Customer ポータルに管理者権限でログインします。
   
    b. **[Application and User Management Common Task (アプリケーションとユーザー管理の共通タスク)]** に移動し、**[ID プロバイダー]** タブをクリックします。
   
    c. **[New Identity Provider (新しい ID プロバイダー)]** をクリックし、Azure Portal からダウンロードしたメタデータの XML ファイルを選択します。 メタデータをインポートすることによって、必要な署名証明書と暗号化証明書が自動的にアップロードされます。
   
    ![Configure Single Sign-On](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure Active Directory は、SAML 要求で Assertion Consumer Service URL 要素を必要とするため、**[Include Assertion Consumer Service URL (Assertion Consumer Service URL を含める)]** チェック ボックスをオンにします。
   
    e. **[Activate Single Sign-On (シングル サインオンを有効にする)]** をクリックします。
   
    f. 変更を保存します。
   
    g. **[My System (自分のシステム)]** タブをクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. **[Azure AD Sign On URL (Azure AD サインオン URL)]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. ユーザー ID とパスワードでログオンするか、SSO でログオンするかを従業員が手動で選択できるかどうかを、**[Manual Identity Provider Selection (ID プロバイダーの手動選択)]** を選択して指定します。
   
    j. **[SSO URL]** セクションには、従業員がシステムへのサインオンに使用する URL を指定します。 
    **[URL Sent to Employee (従業員に送信する URL)]** 一覧で、次のオプションを選択できます。
   
    **[Non-SSO URL (非 SSO URL)]**
   
    従業員に送信されるのは、システムの通常の URL のみです。 従業員のログオンに SSO は使用できず、パスワードまたは証明書を使用する必要があります。
   
    **[SSO URL (SSO の URL)]** 
   
    従業員に送信されるのは、SSO の URL のみです。 従業員は SSO を使用してログオンすることができます。 認証要求は IdP を介してリダイレクトされます。
   
    **[Automatic Selection (自動選択)]**
   
    SSO が有効ではない場合、システムの通常の URL が従業員に送信されます。 SSO が有効である場合は、従業員がパスワードを持っているかどうかがシステムによってチェックされます。 パスワードを持っていた場合は、SSO の URL と非 SSO の URL の両方が従業員に送信されます。 一方、従業員がパスワードを持っていない場合は、SSO の URL だけが従業員に送信されます。
   
    k. 変更を保存します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>SAP Cloud for Customer のテスト ユーザーの作成

このセクションでは、SAP Cloud for Customer で Britta Simon というユーザーを作成します。 SAP Cloud for Customer プラットフォームにユーザーを追加する方法については、[SAP Cloud for Customer サポート チーム](https://www.sap.com/about/agreements.sap-cloud-services-customers.html)にお問い合わせください。 

> [!NOTE]
> NameID 値は必ず、SAP Cloud for Customer プラットフォームのユーザー名フィールドと一致させてください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP Cloud for Customer へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**SAP Cloud for Customer に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SAP Cloud for Customer]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAP Cloud for Customer] タイルをクリックすると、自動的に SAP Cloud for Customer アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_203.png


