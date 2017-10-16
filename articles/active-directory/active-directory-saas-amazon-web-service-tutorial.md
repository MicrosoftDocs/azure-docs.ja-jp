---
title: "チュートリアル: Azure Active Directory と Amazon Web Services (AWS) の統合 | Microsoft Docs"
description: "Azure Active Directory と Amazon Web Services (AWS) の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 0fb9c8f428368271b548e3f174726fa01ea910c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>チュートリアル: Azure Active Directory と Amazon Web Services (AWS) の統合

このチュートリアルでは、Amazon Web Services (AWS) と Azure Active Directory (Azure AD) を統合する方法について説明します。

Amazon Web Services (AWS) と Azure AD の統合には、次の利点があります。

- Amazon Web Services (AWS) にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Amazon Web Services (AWS) にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

<!--## Overview

To enable single sign-on with Amazon Web Services (AWS), it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Amazon Web Services (AWS).

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>前提条件

Amazon Web Services (AWS) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Amazon Web Services (AWS) でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Amazon Web Services (AWS) の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>ギャラリーからの Amazon Web Services (AWS) の追加
Azure AD への Amazon Web Services (AWS) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amazon Web Services (AWS) を追加する必要があります。

**ギャラリーから Amazon Web Services (AWS) を追加するには、次の手順を実行します:** 

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Amazon Web Services (AWS)**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_search.png)

5. 結果ウィンドウで **[Amazon Web Services (AWS)]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Amazon Web Services (AWS) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Amazon Web Services (AWS) ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと Amazon Web Services (AWS) の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Amazon Web Services (AWS) の **[Username]** の値として割り当てることで確立されます。

Amazon Web Services (AWS) との Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Amazon Web Services テスト ユーザーの作成](#creating-an-amazon-web-services-test-user)** - Amazon Web Services (AWS) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、Amazon Web Services (AWS) アプリケーションへのシングル サインオンを構成します。

**Amazon Web Services (AWS) との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Amazon Web Services (AWS)** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択してシングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_samlbase.png)

3. アプリは Azure と事前に統合済みであるため、**[Amazon Web Services (AWS) のドメインと URL]** セクションで特に手順を実施する必要はありません。

    ![[シングル サインオンの構成]](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_url.png)

4. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。
    
    ![Configure Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_certificate.png)

5. Amazon Web Services (AWS) アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。

    ![[シングル サインオンの構成]](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名  | 属性値 | 名前空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | 役割            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >AWS コンソールからすべてのロールをフェッチするには、Azure AD でユーザー プロビジョニングを構成する必要があります。 次のプロビジョニングの手順を参照してください。

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure Single Sign-On](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    c. **[値]** 一覧から、その行に対して表示される値を入力します。 上記の Namespace 値を追加します。
    
    d. **[OK]**をクリックします。

7. **[保存]** ボタンをクリックして、設定を Azure に保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. 別の Web ブラウザーのウィンドウで、管理者として Amazon Web Services (AWS) 企業サイトにサインオンします。

9. **[Console Home]**をクリックします。
   
    ![Configure Single Sign-On][11]

10. **[Security, Identity & Compliance (セキュリティ、ID、およびコンプライアンス)]** サービスから **[IAM]** をクリックします。
   
    ![[シングル サインオンの構成]][12]

11. **[Identity Providers]**、**[Create Provider]** の順にクリックします。
   
    ![[シングル サインオンの構成]][13]

12. **[Configure Provider]** ダイアログ ページで、次の手順を実行します。
   
    ![Configure Single Sign-On][14]
 
    a. **[Provider Type]** として **[SAML]** を選択します。

    b. **[Provider Name]** ボックスにプロバイダー名を入力します (例: *WAAD*)。

    c. ダウンロードしたメタデータ ファイルをアップロードするには、 **[Choose file]**をクリックします。

    d. ページの下部にある **[Next Step]**」を参照してください。

13. **[Verify Provider Information]** ダイアログ ボックスで、**[Create]** をクリックします。 
    
    ![[シングル サインオンの構成]][15]

14. **[Roles]** をクリックしてから **[Create New Role]** をクリックします。 
    
    ![Configure Single Sign-On][16]

15. **[Set Role Name]** ダイアログで、次の手順を実行します。 
    
    ![[シングル サインオンの構成]][17] 

    a. **[Role Name]** ボックスにロール名を入力します (例: *TestUser*)。 

    b. ページの下部にある **[Next Step]**」を参照してください。

16. **[Select Role Type]** ダイアログで、次の手順を実行します。 
    
    ![[シングル サインオンの構成]][18] 

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[Role For Identity Provider Access]** を選択します。 

    b. **[Grant Web Single Sign-On (WebSSO) access to SAML providers]** セクションで、**[Select]** をクリックします。

17. **Establish Trust** ダイアログで、次の手順を実行します。  
    
    ![[シングル サインオンの構成]][19] 

    a. SAML プロバイダーとして、先ほど作成した SAML プロバイダーを選択します (例: *WAAD*)。
  
    b. ページの下部にある **[Next Step]**」を参照してください。

18. **[Verify Role Trust]** ダイアログで、**[Next Step]** をクリックします。
    
    ![[シングル サインオンの構成]][32]

19. **[Attach Policy]** ダイアログで、**[Next Step]** をクリックします。
    
    ![[シングル サインオンの構成]][33]

20. **[Review]** ダイアログで、次の手順を実行します。
    
    ![[シングル サインオンの構成]][34]
 
    a. **[Create Role]** をクリックします。

    b. 必要な数の役割ロールを作成し、それらを ID プロバイダーにマップします。

21. 次に、AWS からすべてのロールをフェッチするようにユーザー プロビジョニングを構成します。

    a. AWS コンソールでルート アカウントを使用してログインします。

    b. 右上隅の自分の名前をクリックし、**[My Security Credentials (自分のセキュリティ資格情報)]** オプションをクリックします。 これにより、画面が警告メッセージとして開きます。 **[Security Credentials (セキュリティ資格情報)]** ボタンをクリックして、この画面を通過します。
        
       ![[シングル サインオンの構成]][36]

       ![Configure Single Sign-On][37]

    c. [Access Keys (アクセス キー)] セクションで、**[Create New Access Key (新しいアクセス キーの作成)]** ボタンをクリックします。 これにより、アクセス キー ID とトークン値が生成されます。
    
       ![Configure Single Sign-On][38]

    d. この 2 つの値をコピーしたうえでダウンロードも行って、値が失われないようにします。

    e. Azure Portal のAmazon Web Services (AWS) アプリケーション統合ページで、**[プロビジョニング]** をクリックします。
        
       ![Configure Single Sign-On][35]

    f. [プロビジョニング モード] を **[自動]** に設定します。
        
       ![[シングル サインオンの構成]][39]

    g. 次に、**[clientsecret]** と **[シークレット トークン]** に、AWS コンソールからコピーした対応する値を貼り付けます。
    
    h. **[接続テスト]** ボタンをクリックして接続をテストできます。 正常に接続されたら、プロビジョニング コネクタを開始できます。
       
       ![[シングル サインオンの構成]][40]

    i. 次に、プロビジョニング状態を **[On]** にします。 これにより、アプリケーションからのロールのフェッチが開始されます。

       ![[シングル サインオンの構成]][41]

    > [!NOTE]
    > AWS からフェッチしたロールを同期するための Azure AD プロビジョニング サービスの実行は、しばらく時間をおいてから開始されます。 AWS ロールにアタッチされているすべての ID プロバイダーが Azure AD に表示されるため、アプリケーションをユーザーまたはグループに割り当てるときにそれらを使用できます。

<!--### Next steps

To ensure users can sign-in to Amazon Web Services (AWS) after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Amazon Web Services (AWS) prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Amazon Web Services (AWS) in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Amazon Web Services (AWS) users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-an-amazon-web-services-test-user"></a>Amazon Web Services テスト ユーザーの作成

Azure AD ユーザーが Amazon Web Services (AWS) にログインできるようにするには、ユーザーを Amazon Web Services (AWS) にプロビジョニングする必要があります。 Amazon Web Services (AWS) の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として **Amazon Web Services (AWS)** 企業サイトにログインします。

2. **[Console Home (コンソール ホーム)]** アイコンをクリックします。 
   
    ![[シングル サインオンの構成]][11]

3. [Identity and Access Management] をクリックします。 
   
    ![[シングル サインオンの構成]][28]

4. Dashboard (ダッシュボード) で、**[Users (ユーザー)]**、**[Create New Users (新しいユーザーの作成)]** の順にクリックします。 
   
    ![[シングル サインオンの構成]][29]

5. [Create User] ダイアログで、次の手順に従います。 
   
    ![[シングル サインオンの構成]][30]   
    
    a. **[Enter User Names]** ボックスに、Brita Simon の Azure AD でのユーザー名 (userprincipalname) を入力します。

    b. **[Create (作成)]** をクリックします。
        
### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Amazon Web Services (AWS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Amazon Web Services (AWS) に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、 **[Amazon Web Services (AWS)]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[ロールの選択]** タブで、ユーザーに割り当てる適切なロールを選択します。 すべてのロールは、ロール名と ID プロバイダー名付きで表示されます。 これにより、AWS からフェッチされたロールを簡単に特定できます。

8. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Amazon Web Services (AWS)] タイルをクリックすると、自動的に Amazon Web Services (AWS) アプリケーションにサインオンします。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png

[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795037.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
