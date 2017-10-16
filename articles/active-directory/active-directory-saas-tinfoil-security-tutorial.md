---
title: "チュートリアル: Azure Active Directory と TINFOIL SECURITY の統合 | Microsoft Docs"
description: "Azure Active Directory と TINFOIL SECURITY の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 614e4de3335574f4b56c7d641af4fcfafdb17d12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>チュートリアル: Azure Active Directory と TINFOIL SECURITY の統合

このチュートリアルでは、TINFOIL SECURITY と Azure Active Directory (Azure AD) を統合する方法について説明します。

TINFOIL SECURITY と Azure AD の統合には、次の利点があります。

- TINFOIL SECURITY にアクセスするユーザーを Azure AD で管理できます
- ユーザーが自分の Azure AD アカウントで自動的に TINFOIL SECURITY にサインオン (シングル サインオン) できるようにすることが可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

TINFOIL SECURITY と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- TINFOIL SECURITY でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの TINFOIL SECURITY の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-tinfoil-security-from-the-gallery"></a>ギャラリーからの TINFOIL SECURITY の追加
Azure AD への TINFOIL SECURITY の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TINFOIL SECURITY を追加する必要があります。

**ギャラリーから TINFOIL SECURITY を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**TINFOIL SECURITY**」と入力して、結果パネルで **TINFOIL SECURITY** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![ギャラリーからの TINFOIL SECURITY](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、TINFOIL SECURITY で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する TINFOIL SECURITY ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと TINFOIL SECURITY の関連ユーザーの間で、リンク関係が確立されている必要があります。

TINFOIL SECURITY で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

TINFOIL SECURITY で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[TINFOIL SECURITY テスト ユーザーの作成](#create-a-tinfoil-security-test-user)** - TINFOIL SECURITY で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、TINFOIL SECURITY アプリケーションでシングル サインオンを構成します。

**TINFOIL SECURITY で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **TINFOIL SECURITY** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![SAML ベースのサインオン](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. アプリは Azure と事前に統合済みであるため、**[TINFOIL SECURITY のドメインと URL]** セクションで特に手順を実施する必要はありません。

    ![[シングル サインオンの構成]](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. **[SAML 署名証明書]** セクションで、**[拇印]** の値をコピーします。

    ![[SAML 署名証明書] セクション](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. 必要な属性のマッピングを追加するには、次の手順を実行します。
    
    ![属性](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "Attributes")
    
    | 属性名    |   属性値 |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. **[ユーザー属性の追加]**をクリックします。
    
    ![属性の追加](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "属性")
    
    ![属性の追加](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "属性")
    
    b. **[属性名]** テキストボックスに、「**accountid**」と入力します。
    
    c. **[属性値]** ボックスに、後で取得するアカウント ID の値を貼り付けます。
    
    d. **[OK]**をクリックします。    

6. **[保存]** ボタンをクリックします。

    ![[保存] ボタン](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_400.png)

7. **[TINFOIL SECURITY 構成]** セクションで、**[TINFOIL SECURITY の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![TINFOIL SECURITY の構成](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. 別の Web ブラウザーのウィンドウで、TINFOIL SECURITY の企業サイトに管理者としてログインします。

9. 上部のツール バーの **[My Account]**をクリックします。
   
    ![Dashboard](./media/active-directory-saas-tinfoil-security-tutorial/ic798971.png "Dashboard")

10. **[セキュリティ]**をクリックします。
   
    ![Security (セキュリティ)](./media/active-directory-saas-tinfoil-security-tutorial/ic798972.png "Security")

11. [ **シングル サインオン** ] 構成ページで、次の手順を実行します。
   
    ![シングル サインオン](./media/active-directory-saas-tinfoil-security-tutorial/ic798973.png "Single Sign-On")
   
    a. **[Enable SAML]**を選択します。
   
    b. **[Manual Configuration]**をクリックします。
   
    c. **[SAML Post URL]\(SAML POST の URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
    d. **[SAML Certificate Fingerprint]\(SAML 証明書フィンガープリント\)** ボックスに、**[SAML 署名証明書]** セクションからコピーした**拇印**の値を貼り付けます。
  
    e. **[Your Account ID]\(アカウント ID\)** の値をコピーし、Azure Portal の **[属性の追加]** セクションの **[属性値]** ボックスに値を貼り付けます。
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] -> [すべてのユーザー] ](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![User](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="create-a-tinfoil-security-test-user"></a>TINFOIL SECURITY テスト ユーザーの作成

Azure AD ユーザーが TINFOIL SECURITY にログインできるようにするには、そのユーザーを TINFOIL SECURITY にプロビジョニングする必要があります。 TINFOIL SECURITY の場合、プロビジョニングは手動で行います。

**ユーザーをプロビジョニングするには、次の手順に従います。**

1. ユーザーがエンタープライズ アカウントに参加している場合は、[TINFOIL SECURITY サポート チームに連絡](https://www.tinfoilsecurity.com/contact)して、ユーザー アカウントを作成する必要があります。

2. 通常の TINFOIL SECURITY SaaS ユーザーは、グループ作業者をユーザーの任意のサイトに追加できます。 ここでは、新しい TINFOIL SECURITY ユーザー アカウントを作成するための招待状を指定した電子メール アドレスに送信するプロセスが起動します。

> [!NOTE]
> TINFOIL SECURITY から提供されている他の TINFOIL SECURITY ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TINFOIL SECURITY へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を TINFOIL SECURITY に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[TINFOIL SECURITY]**を選択します。

    ![TINFOIL SECURITY の選択](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [TINFOIL SECURITY] タイルをクリックすると、TINFOIL SECURITY アプリケーションに自動的にサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_203.png

