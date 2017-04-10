---
title: "チュートリアル: Azure Active Directory と Citrix ShareFile の統合 | Microsoft Docs"
description: "Azure Active Directory で Citrix ShareFile を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 62f9c25a4c1f34cad266c9948cdf82320f5e7011
ms.openlocfilehash: 0c46640d258a18ecbd3b0f8683412544272c63e0
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>チュートリアル: Azure Active Directory と Citrix ShareFile の統合
このチュートリアルでは、Azure と Citrix ShareFile の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Citrix ShareFile テナント

このチュートリアルを完了すると、Citrix ShareFile に割り当てた Azure AD ユーザーは、Citrix ShareFile 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Citrix ShareFile のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scenario")

## <a name="enable-the-application-integration-for-citrix-sharefile"></a>Citrix ShareFile のアプリケーション統合の有効化
このセクションでは、Citrix ShareFile のアプリケーション統合を有効にする方法を説明します。

**Citrix ShareFile のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
=   ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Citrix ShareFile**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Application Gallery")
7. 結果ウィンドウで **[Citrix ShareFile]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Citrix ShareFile に対する認証を行えるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Citrix ShareFile** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの有効化](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "シングル サインオンの有効化")
2. **[ユーザーの Citrix ShareFile へのアクセスを設定してください]** ページで、**[Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページで、**[Citrix ShareFile サインオン URL]** ボックスに、`https://<tenant-name>.shareFile.com` のパターンで URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configure App URL")
4. **[Citrix ShareFile でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。
   
   ![ConfigureSingle Sign-On](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle Sign-On")
5. 別の Web ブラウザー ウィンドウで、 **Citrix ShareFile** 企業サイトに管理者としてログインします。
6. 上部のツールバーの **[Admin]**をクリックします。
7. 左側のナビゲーション ウィンドウで、 **[Configure Single Sign-On]**を選択します。
   
   ![Account Administration](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Account Administration")
8. **[Single Sign-On/ SAML 2.0 Configuration]** ダイアログ ページの **[基本設定]** で、次の手順を実行します。
   
   ![シングル サインオン](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "シングル サインオン")
   1. **[Enable SAML]**をクリックします。
   2. Azure クラシック ポータルで、**[Citrix ShareFile でのシングル サインオンの構成]** ダイアログ ページの **[エンティティ ID]** の値をコピーし、**[Your IDP Issuer/ Entity ID (IDP 発行者/エンティティ ID)]** ボックスに貼り付けます。
   3. Azure クラシック ポータルで、**[Citrix ShareFile でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[ログイン URL]** ボックスに貼り付けます。
   4. Azure クラシック ポータルで、**[Citrix ShareFile でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[ログアウト URL]** ボックスに貼り付けます。
   5. **[X.509 Certificate]** フィールドの横の **[変更]** をクリックし、Azure AD クラシック ポータルからダウンロードした証明書をアップロードします。 
   
      ![Basic Settings](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Basic Settings")
9. Citrix ShareFile 管理ポータルで **[Save]** をクリックします。
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Configure single sign-on")
    
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Citrix ShareFile にログインできるようにするには、ユーザーを Citrix ShareFile にプロビジョニングする必要があります。  

* Citrix ShareFile の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Citrix ShareFile** テナントにログインします。
2. **[ユーザーの管理]\>[Manage Users Home (ユーザーの管理: ホーム)]\>[+ Create Employee (従業員の作成)]** の順にクリックします。
   
   ![Create Employee](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Create Employee")
3. プロビジョニングする有効な Azure AD アカウントの**メール**、**名**、**姓**を入力します。
   
   ![Basic Information](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Basic Information")
4. **[ユーザーの追加]**をクリックします。
   >[!NOTE]
   >AAD アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。 
   > 

>[!NOTE]
>Citrix ShareFile から提供されている他の Citrix ShareFile ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Citrix ShareFile に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Citrix ShareFile** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


