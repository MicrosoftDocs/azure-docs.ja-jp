---
title: "チュートリアル: Azure Active Directory と 15Five の統合 | Microsoft Docs"
description: "Azure Active Directory で 15Five を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2fb301c2-7d7a-4046-8ee1-7dc9e7684806
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: ce98338e6b21eb35a17f0183f409dd54d1123bb9


---
# <a name="tutorial-azure-active-directory-integration-with-15five"></a>チュートリアル: Azure Active Directory と 15Five の統合
このチュートリアルの目的は、Azure と 15Five の統合を紹介することです。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* 15Five でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、15Five に割り当てた Azure AD ユーザーは、15Five 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. 15Five のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-15five-tutorial/IC784667.png "Scenario")

## <a name="enabling-the-application-integration-for-15five"></a>15Five のアプリケーション統合の有効化
このセクションでは、15Five のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>15Five のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-15five-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-15five-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-15five-tutorial/IC749322.png "Add an application from gallerry")
6. **[検索] ボックス**に、「**15Five**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-15five-tutorial/IC784668.png "Application Gallery")
7. 結果ウィンドウで **[15Five]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで 15Five に対する認証を行えるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **15Five** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-15five-tutorial/IC784670.png "Configure single sign-on")
2. **[ユーザーの 15Five へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-15five-tutorial/IC784671.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページで、**[15Five サインイン URL]** ボックスに、"*https://company.15Five.com*" のパターンを使用して URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-15five-tutorial/IC784672.png "Configure App URL")
4. **[15Five でのシングルサインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、メタ データファイルを 15Five サポート チームに転送します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-15five-tutorial/IC784673.png "Configure single sign-on")
   
   > [!NOTE]
   > 15Five サポート チームがシングル サインオンを有効にする必要があります。
   > 
   > 
5. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-15five-tutorial/IC784674.png "Configure single sign-on")
   

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが 15Five にログインできるようにするには、そのユーザーを 15Five にプロビジョニングする必要があります。  
15Five の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. **15Five** 企業サイトに管理者としてログインします。
2. [ **会社の管理**] に移動します。
   
   ![会社の管理](./media/active-directory-saas-15five-tutorial/IC784675.png "Manage Company")
3. **[ユーザー] \> [ユーザーを追加]** の順にクリックします。
   
   ![ユーザー](./media/active-directory-saas-15five-tutorial/IC784676.png "People")
4. [新しいユーザーの追加] セクションで、次の手順に従います。
   
   ![新しいユーザーの追加](./media/active-directory-saas-15five-tutorial/IC784677.png "Add New Person")
   
   1. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**名**、**姓**、**役職**、**電子メール アドレス**を入力します。
   2. **[Done]**をクリックします。
   
   > [!NOTE]
   > アカウントがアクティブになる前に、Azure AD アカウント所有者に、アカウント確認用のリンクを含む電子メールが送信されます。
   > 
   > 



## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-15five-perform-the-following-steps"></a>ユーザーを 15Five に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **15Five** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-15five-tutorial/IC784678.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-15five-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Dec16_HO5-->


