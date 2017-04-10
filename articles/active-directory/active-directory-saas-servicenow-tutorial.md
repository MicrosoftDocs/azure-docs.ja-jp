---
title: "チュートリアル: Azure Active Directory と ServiceNow の統合 | Microsoft Docs"
description: "Azure Active Directory と ServiceNow および ServiceNow Express の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 1d8eb99e-8ce5-4ba4-8b54-5c3d9ae573f6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c1e28c5ad7fd3236899ac0ffe9f3d43241061897
ms.openlocfilehash: 5f253c3a10e5bfdfa9fe36912b5ebcbc9abae3eb
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>チュートリアル: Azure Active Directory と ServiceNow の統合
このチュートリアルでは、ServiceNow および ServiceNow Express と Azure Active Directory (Azure AD) を統合する方法について説明します。

ServiceNow および ServiceNow Express と Azure AD の統合には、次の利点があります。

* ServiceNow および ServiceNow Express にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで ServiceNow および ServiceNow Express に自動的にサインオン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
ServiceNow および ServiceNow Express と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* ServiceNow の場合は、Calgary バージョン以降の ServiceNow のインスタンスまたはテナント
* ServiceNow Express の場合は、Helsinki バージョン以降の ServiceNow Express のインスタンス
* ServiceNow のテナントで [Multiple Provider Single Sign On プラグイン](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)が有効になっている必要があります。 このプラグインを有効にするには、[サービス要求を送信してください](https://hi.service-now.com)。 

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。
> 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

1. ギャラリーからの ServiceNow の追加
2. ServiceNow または ServiceNow Express 向けの Azure AD シングル サインオンの構成とテスト

## <a name="adding-servicenow-from-the-gallery"></a>ギャラリーからの ServiceNow の追加
Azure AD への ServiceNow または ServiceNow Express の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ServiceNow を追加する必要があります。 

**ギャラリーから ServiceNow を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。 
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**ServiceNow**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)
7. 結果ウィンドウで **[ServiceNow]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ServiceNow または ServiceNow Express で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ServiceNow ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ServiceNow の関連ユーザーの間で、リンク関係が確立されている必要があります。
このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を ServiceNow の **[Username (ユーザー名)]** の値として割り当てます。 ServiceNow で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[ServiceNow 向け Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on-for-servicenow)** - ユーザーがこの機能を使用できるようにします。
2. **[ServiceNow Express 向け Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - ユーザーがこの機能を使用できるようにします。
3. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[ServiceNow のテスト ユーザーの作成](#creating-a-servicenow-test-user)** - ServiceNow で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
6. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

> [!NOTE]
> ServiceNow を構成する場合は、手順 2. を省略します。 同様に、ServiceNow Express を構成する場合は、手順 1. を省略します。
> 
> 

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>ServiceNow 向け Azure AD シングル サインオンの構成
1. Azure AD クラシック ポータルの **ServiceNow** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configure single sign-on")

2. **[ユーザーの ServiceNow へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configure single sign-on")

3. **[アプリケーション設定の構成]** ページで、次の手順を実行します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configure app URL")
   
    a. **[ServiceNow Sign On URL (ServiceNow サインオン URL)]** ボックスに、`https://<instance-name>.service-now.com` というパターンで、ServiceNow アプリケーションへのサインオンにユーザーが使用する URL を入力します。
   
    b. **[識別子]** ボックスに、`https://<instance-name>.service-now.com` というパターンで、ServiceNow アプリケーションへのサインオンにユーザーが使用する URL を入力します。
   
    c. **[次へ]**

4. SAML ベースの認証用に Azure AD で ServiceNow を自動的に構成するには、 **[シングル サインオンの自動構成]** フォームに ServiceNow のインスタンス名、管理者のユーザー名、管理者パスワードを入力し、 *[構成]*をクリックします。 ここで指定する管理者のユーザー名には、ServiceNow で **security_admin** ロールが割り当てられている必要があります。 Azure AD を SAML ID プロバイダーとして使用するよう ServiceNow を手動で構成するには、**[シングル サインオン用にこのアプリケーションを手動で構成する]** をクリックして **[次へ]** をクリックし、次の手順を完了します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configure app URL")

5. **[ServiceNow でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをローカル コンピューターに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configure single sign-on")

6. ServiceNow アプリケーションに管理者としてサインオンします。

7. *[Integration - Multiple Provider Single Sign-On Installer (統合 - 複数プロバイダーのシングル サインオン インストーラー)]* プラグインをアクティブ化するには、次の手順に従います。
   
    a. 左側のナビゲーション ウィンドウで **[System Definition (システム定義)]** セクションに移動し、**[Plugins (プラグイン)]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "プラグインのアクティブ化")
   
    b. "*Integration - Multiple Provider Single Sign-On Installer*" を検索します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "プラグインのアクティブ化")
   
    c. プラグインを選択します。 右クリックして **[Activate/Upgrade (アクティブ化/アップグレード)]** を選択します。
   
    d. **[Activate (アクティブ化)]** ボタンをクリックします。

8. 左側のナビゲーション ウィンドウで、 **[Properties]**をクリックします。  
   
    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configure app URL")

9. **[Multiple Provider SSO Properties]** ダイアログで、次の手順を実行します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configure app URL")
   
    a. **[Enable multiple provider SSO]** で **[Yes]** を選択します。
   
    b. **[Enable debug logging got the multiple provider SSO integration]** で **[Yes]** を選択します。
   
    c. **[The field on the user table that...]** ボックスに、「**user_name**」と入力します。
   
    d. [ **Save**] をクリックします。

10. 左側のナビゲーション ウィンドウで、 **[x509 Certificates]**をクリックします。
    
     ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "Configure single sign-on")

11. **[x509 Certificates]** ダイアログで、**[New]** をクリックします。
    
     ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Configure single sign-on")

12. **[X.509 Certificates]** ダイアログで、次の手順を実行します。
    
     ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configure single sign-on")
    
     a. **[新規]**をクリックします。
    
     b. **[Name]** ボックスに、構成の名前を入力します (例: **TestSAML2.0**)。
    
     c. **[アクティブ]**を選択します。
    
     d. **[Format]** で **[PEM]** を選択します。
    
     e. **[Type]** で **[Trust Store Cert]** を選択します。
    
     f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Base64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[PEM Certificate (PEM 証明書)]** ボックスに貼り付けます。
    
     g. **[Update]**をクリックします。

13. 左側のナビゲーション ウィンドウで、 **[Identity Providers]**をクリックします。
    
     ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "Configure single sign-on")

14. **[Identity Providers]** で、**[New]** をクリックします。
    
     ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Configure single sign-on")

15. **[Identity Providers]** ダイアログで、**[SAML2 Update1?]** をクリックします。
    
     ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Configure single sign-on")

16. [SAML2 Update1 Properties] ダイアログで、次の手順を実行します。
    
     ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Configure single sign-on")

    a. **[Name]** ボックスに、構成の名前を入力します (例: **SAML 2.0**)。

    b. ServiceNow のデプロイでユーザーを一意に識別するためのフィールドに応じて、**[User Field]** ボックスに「**email**」または「**user_id**」と入力します。 

    > [!NOTE] 
    > SAML トークンの一意の識別子として Azure AD ユーザー ID (ユーザーのプリンシパル名) か電子メール アドレスを出力するように Azure AD を構成できます。そのためには、Azure クラシック ポータルで **[ServiceNow]、[属性]、[シングル サインオン]** セクションの順に移動し、目的のフィールドを **nameidentifier** 属性にマッピングします。 Azure AD に格納される選択した属性 (ユーザー プリンシパル名など) の値と、ServiceNow に格納される入力したフィールド (user_name など) の値が一致している必要があります。

    c. Azure AD クラシック ポータルで、**[プロバイダー ID の識別]** の値をコピーし、**[Identity Provider URL]** ボックスに貼り付けます。

    d. Azure AD クラシック ポータルで、**[認証要求 URL]** の値をコピーし、**[Identity Provider's AuthnRequest]** ボックスに貼り付けます。

    e. Azure AD クラシック ポータルで、**[シングル サインアウト サービス URL]** の値をコピーし、**[Identity Provider's SingleLogoutRequest]** ボックスに貼り付けます。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[ServiceNow Homepage]** ボックスに ServiceNow インスタンス ホームページの URL を入力します。

    > [!NOTE] 
    > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

    g. **[Entity ID / Issuer]** ボックスに、ServiceNow テナントの URL を入力します。

    h. **[Audience URL]** ボックスに、ServiceNow テナントの URL を入力します。 

    i. **[Protocol Binding for the IDP's SingleLogoutRequest]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**」と入力します。

    j. [NameID Policy] ボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**」と入力します。

    k. **[Create an AuthnContextClass]** をオフにします。

    l. **[AuthnContextClassRef Method (AuthnContextClassRef メソッド)]** に「`http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`」と入力します。 これは、クラウドのみの組織である場合のみ必要です。 オンプレミスの ADFS または MFA での認証を行っている場合、この値は構成しないでください。 

    m. **[Clock Skew]** ボックスに「**60**」と入力します。

    n. **[Single Sign On Script]** で **[MultiSSO_SAML2_Update1]** を選択します。

    o. **[x509 Certificate]** で、前の手順で作成した証明書を選択します。

    p. **[Submit]**をクリックします。 

1. Azure AD クラシック ポータルで、[single sign-on configuration confirmation] \(シングル サインオンの構成の確認) を選択し、 **[次へ]**をクリックします。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configure single sign-on")

2. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configure single sign-on")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>ServiceNow Express 向け Azure AD シングル サインオンの構成
1. Azure AD クラシック ポータルの **ServiceNow** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Configure single sign-on")

2. **[ユーザーの ServiceNow へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Configure single sign-on")

3. **[アプリケーション設定の構成]** ページで、次の手順を実行します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configure app URL")
   
    a. **[ServiceNow Sign On URL (ServiceNow サインオン URL)]** ボックスに、`https://<instance-name>.service-now.com` というパターンで、ServiceNow アプリケーションへのサインオンにユーザーが使用する URL を入力します。
   
    b. **[発行者の URL]** ボックスに、`https://<instance-name>.service-now.com` というパターンで、ServiceNow アプリケーションへのサインオンにユーザーが使用する URL を入力します。
   
    c. **[次へ]**

4. **[シングル サインオン用にこのアプリケーションを手動で構成する]** をクリックし、**[次へ]** をクリックして、次の手順を実行します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configure app URL")

5. **[ServiceNow でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターのローカルに保存し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Configure single sign-on")

6. ServiceNow Express アプリケーションに管理者としてサインオンします。

7. 左側にあるナビゲーション ウィンドウで、**[Single Sign-On (シングル サインオン)]** をクリックします。  
   
    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configure app URL")

8. **[Single Sign-On (シングル サインオン)]** ダイアログで右上の構成アイコンをクリックし、次のプロパティを設定します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configure app URL")
   
    a. **[Enable multiple provider SSO (複数プロバイダー SSO を有効にする)]** を右に切り替えます。
   
    b. **[Enable debug logging for the multiple provider SSO integration (複数プロバイダー SSO 統合でのデバッグ ログの有効化)]** を右に切り替えます。
   
    c. **[The field on the user table that...]** ボックスに、「**user_name**」と入力します。
9. **[Single Sign-On (シングル サインオン)]** ダイアログで、**[Add New Certificate (新しい証明書の追加)]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "Configure single sign-on")
10. **[X.509 Certificates]** ダイアログで、次の手順を実行します。
    
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Configure single sign-on")
    
    a. **[Name]** ボックスに、構成の名前を入力します (例: **TestSAML2.0**)。
    
    b. **[アクティブ]**を選択します。
    
    c. **[Format]** で **[PEM]** を選択します。
    
    d. **[Type]** で **[Trust Store Cert]** を選択します。
    
    e. ダウンロードした証明書から Base64 でエンコードされたファイルを作成します。
    
    > [!NOTE]
    > 詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。
    > 
    > 
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Base64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[PEM Certificate (PEM 証明書)]** ボックスに貼り付けます。
    
    g. **[Update]**をクリックします。
11. **[Single Sign-On (シングル サインオン)]** ダイアログで、**[Add New IdP (新しい IdP の追加)]** をクリックします。
    
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "Configure single sign-on")
12. **[Add New Identity Provider (新しい ID プロバイダーの追加)]** ダイアログの **[Configure Identity Provider (ID プロバイダーの構成)]** で、次の手順に従います。
    
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "Configure single sign-on")

    a. **[Name (名前)]** ボックスに、構成の名前を入力します (例: **SAML 2.0**)。

    b. Azure AD クラシック ポータルで、**[プロバイダー ID の識別]** の値をコピーし、**[Identity Provider URL]** ボックスに貼り付けます。

    c. Azure AD クラシック ポータルで、**[認証要求 URL]** の値をコピーし、**[Identity Provider's AuthnRequest]** ボックスに貼り付けます。

    d. Azure AD クラシック ポータルで、**[シングル サインアウト サービス URL]** の値をコピーし、**[Identity Provider's SingleLogoutRequest]** ボックスに貼り付けます。

    e. **[Identity Provider Certificate (ID プロバイダー証明書)]** で、前の手順で作成した証明書を選択します。


1. **[Advanced Settings (詳細設定)]** をクリックし、**[Additional Identity Provider Properties (追加の ID プロバイダーのプロパティ)]** で、次の手順に従います。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "Configure single sign-on")
   
    a. **[Protocol Binding for the IDP's SingleLogoutRequest]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**」と入力します。
   
    b. **[NameID Policy (NameID ポリシー)]** ボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**」と入力します。    
   
    c. **[AuthnContextClassRef Method]** に、「**http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**」と入力します。
   
    d. **[Create an AuthnContextClass]** をオフにします。

2. **[Additional Service Provider Properties (追加のサービス プロバイダーのプロパティ)]** で、次の手順に従います。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "Configure single sign-on")
   
    a. **[ServiceNow Homepage]** ボックスに ServiceNow インスタンス ホームページの URL を入力します。
   
    > [!NOTE]
    > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。
    > 
    > 
   
    b. **[Entity ID / Issuer]** ボックスに、ServiceNow テナントの URL を入力します。
   
    c. **[Audience URI (対象ユーザー URI)]** ボックスに、ServiceNow テナントの URL を入力します。 
   
    d. **[Clock Skew]** ボックスに「**60**」と入力します。
   
    e. ServiceNow のデプロイでユーザーを一意に識別するためのフィールドに応じて、**[User Field]** ボックスに「**email**」または「**user_id**」と入力します。
   
    > [!NOTE]
    > SAML トークンの一意の識別子として Azure AD ユーザー ID (ユーザーのプリンシパル名) か電子メール アドレスを出力するように Azure AD を構成できます。そのためには、Azure クラシック ポータルで **[ServiceNow]、[属性]、[シングル サインオン]** セクションの順に移動し、目的のフィールドを **nameidentifier** 属性にマッピングします。 Azure AD に格納される選択した属性 (ユーザー プリンシパル名など) の値と、ServiceNow に格納される入力したフィールド (user_name など) の値が一致している必要があります。
    > 
    > 
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 [ **Save**] をクリックします。 

3. Azure AD クラシック ポータルで、[single sign-on configuration confirmation] \(シングル サインオンの構成の確認) を選択し、 **[次へ]**をクリックします。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Configure single sign-on")

4. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
このセクションでは、Active Directory のユーザー アカウントのプロビジョニングを ServiceNow に対して有効にする方法について説明します。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. Azure 管理クラシック ポータルの **ServiceNow** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックします。 
   
    ![ユーザー プロビジョニング](./media/active-directory-saas-servicenow-tutorial/IC769498.png "ユーザー プロビジョニング")

2. **[自動ユーザー プロビジョニングを有効にするための ServiceNow の資格情報を入力してください]** ページで、以下の構成設定を入力します。
   
     a. **[ServiceNow インスタンス名]** ボックスに、ServiceNow インスタンス名を入力します。
   
     b. **[ServiceNow 管理ユーザー名]** ボックスに、ServiceNow 管理者アカウントの名前を入力します。
   
     c. **[ServiceNow 管理パスワード]** ボックスに、このアカウントのパスワードを入力します。
   
     d. **[検証]** をクリックして構成を確認します。
   
     e. **[次へ]** をクリックして、**[次のステップ]** ページを開きます。
   
     f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 このアプリケーションにすべてのユーザーをプロビジョニングする場合は、**[ディレクトリ内のすべてのユーザー アカウントをこのアプリケーションに自動的にプロビジョニングする]** をオンにします。 
   
    ![次のステップ](./media/active-directory-saas-servicenow-tutorial/IC698804.png "次のステップ")
   
     g. **[次のステップ]** ページで、**[完了]** をクリックして構成を保存します。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   
    b. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   
    c. **[次へ]**をクリックします。

6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。
   
   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 
   
   a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[名]** ボックスに「**Britta**」と入力します。  
   
   b. **[姓]** ボックスに「**Simon**」と入力します。
   
   c. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   
   d. **[ロール]** 一覧で **[ユーザー]** を選択します。
   
   e. **[次へ]**をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 
   
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **[新しいパスワード]** の値を書き留めます。
   
    b. **[完了]** をクリックします。   

### <a name="creating-a-servicenow-test-user"></a>ServiceNow のテスト ユーザーの作成
このセクションでは、ServiceNow で Britta Simon というユーザーを作成します。 このセクションでは、ServiceNow で Britta Simon というユーザーを作成します。 ServiceNow または ServiceNow Express アカウントにユーザーを追加する方法がわからない場合は、ServiceNow サポート チームに問い合わせてください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に ServiceNow へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を ServiceNow に割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[ServiceNow]** を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203] 

4. [すべてのユーザー] の一覧で **[Britta Simon]**を選択します。

5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト
このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで [ServiceNow] タイルをクリックすると、自動的に ServiceNow アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png

