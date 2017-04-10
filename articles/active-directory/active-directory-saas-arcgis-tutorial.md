---
title: "チュートリアル: Azure Active Directory と ArcGIS の統合 | Microsoft Docs"
description: "Azure Active Directory で ArcGIS を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 9867af8c5f18e2412e73c2ae5fa95825e367e275
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-arcgis"></a>チュートリアル: Azure Active Directory と ArcGIS の統合
このチュートリアルでは、Azure と ArcGIS の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* ArcGIS でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、ArcGIS に割り当てた Azure AD ユーザーは、ArcGIS 企業サイト (サービス プロバイダーが開始したサインオン) で、または [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* ArcGIS のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scenario")

## <a name="enable-the-application-integration-for-arcgis"></a>ArcGIS のアプリケーション統合の有効化
このセクションでは、ArcGIS のアプリケーション統合を有効にする方法について説明します。

**ArcGIS のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-arcgis-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-arcgis-tutorial/IC749322.png "Add an application from gallerry")
6. **[検索]** ボックスに、「**ArcGIS**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-arcgis-tutorial/IC784736.png "Applcation Gallery")
7. 結果ウィンドウで **[ArcGIS]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで ArcGIS に対する認証を行えるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **ArcGIS** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configure Single Sign-On")
2. **[ユーザーの ArcGIS へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し **[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページの **[ArcGIS サインオン URL]** テキスト ボックスに、"*https://company.maps.arcgis.com*" というパターンで、ArcGIS アプリケーションへのサインオンに使用する URLを入力して、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configure App URL")
4. **[ArcGIS でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、コンピューターにローカルでメタデータ ファイルを保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configure Single Sign-On")
5. 別の Web ブラウザーのウィンドウで、管理者として ArcGIS 企業サイトにログインします。
6. **[Edit Settings]**をクリックします。
   
   ![Edit Settings (設定の編集)](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Edit Settings")
7. **[セキュリティ]**をクリックします。
   
   ![Security (セキュリティ)](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Security")
8. **[Enterprise Logins]** で、**[Set Identity Provider]** をクリックします。
   
   ![Enterprise Logins (エンタープライズ ログイン)](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise Logins")
9. **[Set Identity Provider]** 構成ページで、次の手順に従います。
   
   ![Set Identity Provider (ID プロバイダーの設定)](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Set Identity Provider")
   
   1. [Name] テキスト ボックスに、組織の名前を入力します。
   2. **[Metadata for the Enterprise Identity Provider will be supplied using]** で、**[A File]** を選択します。
   3. ダウンロードしたメタデータ ファイルをアップロードするには、 **[Choose file]**をクリックします。
   4. **[Set Identity Provider]**をクリックします。
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configure Single Sign-On")
    
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが ArcGIS にログインできるようにするには、そのユーザーを ArcGIS にプロビジョニングする必要があります。

* ArcGIS の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **ArcGIS** テナントにログインします。
2. **[Invite Members]**をクリックします。
   
   ![Invite Members (メンバーの招待)](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invite Members")
3. **[Add members automatically without sending an email]** を選択して、**[Next]** をクリックします。
   
   ![メンバーの自動追加](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Add Members Automatically")
4. **[Members]** ダイアログ ページで、次の手順に従います。
   
   ![追加とレビュー](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Add and review")
   1. プロビジョニングする有効な AAD アカウントの **[First Name]**、**[Last Name]**、**[Email]** を入力します。
   2. **[Add And Review]**をクリックします。
5. 入力したデータを確認してから、 **[Add Members]**をクリックします。
   
   ![メンバーの追加](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Add member")

>[!NOTE]
>他の ArcGIS ユーザー アカウントの作成ツールまたは ArcGIS から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを ArcGIS に割り当てるには、次の手順に従います。**
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **ArcGIS** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
  ![ユーザーの割り当て](./media/active-directory-saas-arcgis-tutorial/IC784751.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
  ![はい](./media/active-directory-saas-arcgis-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


