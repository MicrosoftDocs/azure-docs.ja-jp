---
title: "チュートリアル: Azure Active Directory と Rally Software の統合 | Microsoft Docs"
description: "Azure Active Directory で Rally Software を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 504a5723f025d7383dbec78cdd268b7c1b94a1e5


---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>チュートリアル: Azure Active Directory と Rally Software の統合
このチュートリアルでは、Azure と Rally Software の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Rally Software テナント

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Rally Software のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scenario")

## <a name="enabling-the-application-integration-for-rally-software"></a>Rally Software のアプリケーション統合の有効化
このセクションでは、Rally Software のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>Rally Software のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**rally**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Application Gallery")

7. 結果ウィンドウで **[Rally Software]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Rally Software に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、証明書を Rally Software にアップロードする必要があります。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Rally Software** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configure single sign-on")

2. **[ユーザーの Rally Software へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Microsoft Azure AD Single Sign-On](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD Single Sign-On")

3. **[アプリケーション URL の構成]** ページで、**[Rally Software テナント URL]** ボックスに、"*https://\<テナント名\>.rally.com*" のパターンを使用して URL を入力し、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configure App URL")

4. **[Rally Software でのシングル サインオンの構成]** ページで、[メタデータのダウンロード] をクリックしてメタデータをダウンロードし、コンピューターに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configure single sign-on")

5. **Rally Software** テナントにログインします。

6. 上部にあるツールバーの **[Setup]** をクリックし、**[Subscription]** を選択します。
   
    ![Subscription](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Subscription")

7. 上部にあるツールバーの右側の **[Action]** ボタンをクリックし、**[Edit Subscription]** を選択します。

8. **[Subscription]** ダイアログ ページで次の手順に従った後、**[Save & Close]** をクリックします。
   
    ![Authentication](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Authentication")
   
    1. Authentication のドロップダウン リストから、 **[Rally or SSO authentication]** を選びます。
 
    2. Azure クラシック ポータルの **[Rally Software でのシングル サインオンの構成]** ダイアログ ページで、**[ID プロバイダーの識別]** の値をコピーし、**[Identity Provider URL]** ボックスに貼り付けます。

    3. Azure クラシック ポータルで、**[Rally Software でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーします。

9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configure single sign-on")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

AAD ユーザーがサインインできるように、Azure Active Directory ユーザー名を使用して、Rally Software アプリケーションにユーザーをプロビジョニングする必要があります。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. Rally Software テナントにログインします。

2. **[Setup] \> [USERS]** に移動し、**[+ Add New]** をクリックします。
   
    ![ユーザー](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Users")

3. New User テキストボックスに名前を入力し、 **[Add with Details]**をクリックします。

4. **[Create User]** セクションで、次の手順に従います。
   
    ![Create User](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Create User")
   
    1. **[User Name]** ボックスに、プロビジョニングする Azure AD ユーザーの名前を入力します。

    2. **[Email Address]** ボックスに、プロビジョニングする Azure AD ユーザーのメール アドレスを入力します。

    3. **[Save & Close]** をクリックします。

> [!NOTE]
> 他の Rally Software ユーザー アカウントの作成ツールまたは Rally Software から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>ユーザーを Rally Software に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Rally Software** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Jan17_HO1-->


