---
title: "チュートリアル: Azure Active Directory と ArcGIS Online の統合 | Microsoft Docs"
description: "Azure Active Directory と ArcGIS Online の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: jeedes
ms.openlocfilehash: df72270ca6443b456c079b22425f1660aa522389
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>チュートリアル: Azure Active Directory と ArcGIS Online の統合

このチュートリアルでは、ArcGIS Online と Azure Active Directory (Azure AD) を統合する方法について説明します。

ArcGIS Online と Azure AD の統合には、次の利点があります。

- Azure AD で誰が ArcGIS Online にアクセスできるかを制御できます
- ユーザーが自分の Azure AD アカウントで ArcGIS Online に自動的にサインオンされる (シングル サインオン) ようにできます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

<!--## Overview

To enable single sign-on with ArcGIS Online, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in ArcGIS Online.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>前提条件

Azure AD と ArcGIS Online の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- ArcGIS Online でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ArcGIS Online の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-arcgis-online-from-the-gallery"></a>ギャラリーからの ArcGIS Online の追加
ArcGIS Online の Azure AD への統合を構成するには、ArcGIS Online をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

**ギャラリーから ArcGIS Online を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックして新しいアプリケーションを追加します。

    ![アプリケーション][3]

4. 検索ボックスに「**ArcGIS Online**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_search.png)

5. 結果ウィンドウで **[ArcGIS Online]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、ArcGIS Online で Azure AD のシングル サインオンを構成およびテストします。

シングル サインオンを機能させるには、Azure AD が Azure AD のユーザーに対応する ArcGIS Online のユーザーを認識している必要があります。 つまり、Azure AD ユーザーと ArcGIS Online の関連するユーザーの間のリンク関係が確立されている必要があります。

このリンク関係は、Azure AD での **[ユーザー名]** の値を ArcGIS Online での **[ユーザー名]** の値として割り当てることによって確立されます。

ArcGIS Online で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ArcGIS Online テスト ユーザーの作成](#creating-an-arcgis-online-test-user)** - Azure AD でのユーザーにリンクされた、ArcGIS Online での Britta Simon の対応するユーザーを作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、ArcGIS Online アプリケーションでシングル サインオンを構成します。

**ArcGIS Online で Azure AD のシングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **[ArcGIS Online]** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. **[ArcGIS Online Domain and URLs] \(ArcGIS Online のドメインと URL)** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_url.png)

    **[サインオン URL]** ボックスに、`https://<company>.maps.arcgis.com` のパターンを使用して値を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[ArcGIS Online クライアント サポート チーム](http://support.esri.com/)に問い合わせてください。 

4. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-arcgis-tutorial/tutorial_general_400.png)

6. 別の Web ブラウザーのウィンドウで、管理者として ArcGIS 企業サイトにログインします。

7. **[EDIT SETTINGS] \(設定の編集)** をクリックします。

    ![Edit Settings (設定の編集)](./media/active-directory-saas-arcgis-tutorial/ic784742.png "Edit Settings")

8. **[セキュリティ]**をクリックします。

    ![Security (セキュリティ)](./media/active-directory-saas-arcgis-tutorial/ic784743.png "Security")

9. **[Enterprise Logins] [エンタープライズ ログイン]** で、**[SET IDENTITY PROVIDER] \(ID プロバイダーの設定)** をクリックします。

    ![Enterprise Logins (エンタープライズ ログイン)](./media/active-directory-saas-arcgis-tutorial/ic784744.png "Enterprise Logins")

10. **[Set Identity Provider]** 構成ページで、次の手順に従います。
   
    ![Set Identity Provider (ID プロバイダーの設定)](./media/active-directory-saas-arcgis-tutorial/ic784745.png "Set Identity Provider")
   
    a. **[名前]** テキスト ボックスに組織の名前を入力します。

    b. **[Metadata for the Enterprise Identity Provider will be supplied using]** で、**[A File]** を選択します。

    c. ダウンロードしたメタデータ ファイルをアップロードするには、 **[Choose file]**をクリックします。

    d. **[SET IDENTITY PROVIDER] \(ID プロバイダーの設定)** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-arcgis-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-arcgis-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-arcgis-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-arcgis-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-an-arcgis-online-test-user"></a>ArcGIS Online テスト ユーザーの作成

Azure AD ユーザーが ArcGIS Online にログインできるようにするには、そのユーザーを ArcGIS Online にプロビジョニングする必要があります。  
ArcGIS Online の場合、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **ArcGIS** テナントにログインします。

2. **[INVITE MEMBERS] \(メンバーの招待)** をクリックします。
   
    ![Invite Members (メンバーの招待)](./media/active-directory-saas-arcgis-tutorial/ic784747.png "Invite Members")

3. **[Add members automatically without sending an email] \(電子メールを送信せずにメンバーを自動的に追加する)** を選択し、**[NEXT] \(次へ)** をクリックします。
   
    ![メンバーの自動追加](./media/active-directory-saas-arcgis-tutorial/ic784748.png "Add Members Automatically")

4. **[Members]** ダイアログ ページで、次の手順に従います。
   
     ![追加とレビュー](./media/active-directory-saas-arcgis-tutorial/ic784749.png "Add and review")
    
     a. プロビジョニングする有効な AAD アカウントの **[電子メール]**、**[名]**、および **[姓]** を入力します。
  
     b. **[ADD AND REVIEW] \(追加とレビュー)** をクリックします。
5. 入力したデータを確認してから、**[ADD MEMBERS] \(メンバーの追加)** をクリックします。
   
    ![メンバーの追加](./media/active-directory-saas-arcgis-tutorial/ic784750.png "Add member")
        
    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ArcGIS Online へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を ArcGIS Online に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[ArcGIS Online]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-arcgis-tutorial/tutorial_arcgisonline_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [ArcGIS Online] タイルをクリックすると、ArcGIS Online アプリケーションに自動的にサインオンされます。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-arcgis-tutorial/tutorial_general_203.png

