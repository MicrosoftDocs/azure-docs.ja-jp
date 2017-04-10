---
title: "チュートリアル: Azure Active Directory と FM:Systems の統合 | Microsoft Docs"
description: "Azure Active Directory で FM:Systems を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf5588885de9c280eb70712dbf800efe509ee912
ms.openlocfilehash: e92f8cb6e980a0552b8ff836ed521e069ba811bb


---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>チュートリアル: Azure Active Directory と FM:Systems の統合
このチュートリアルでは、Azure と FM: Systems の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* FM:Systems でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、FM:Systems に割り当てた Azure AD ユーザーは、FM:Systems 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. FM:Systems のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Scenario")

## <a name="enabling-the-application-integration-for-fmsystems"></a>FM:Systems のアプリケーション統合の有効化
このセクションでは、FM:Systems のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>FM:Systems のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**FM:Systems**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Application Gallery")

7. 結果ウィンドウで **[FM:Systems]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![FM:Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM:Systems")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで FM:Systems に対する認証を行うことができるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **FM:Systems** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configure Single Sign-On")

2. **[ユーザーの FM:Systems へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configure Single Sign-On")

3. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configure App URL")
   
    a. **[FM:Systems サインオン URL]** ボックスで、お使いの FM:Systems の **[応答 URL]** (例: *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*) を入力します。  
      
    > [!NOTE]
    > この値は、FM:Systems サポート チームから入手できます。
    > 
    > 
   
    b. **[次へ]**

4. **[FM: Systems でのシングル サインオンの構成]** ページで、メタデータをダウンロードするには、**[メタデータのダウンロード]** をクリックし、メタデータをコンピューターに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configure Single Sign-On")

5. ダウンロードしたメタデータ ファイルを FM:Systems サポート チームに送信します。
   
    > [!NOTE]
    > FM:Systems サポート チームが、実際に SSO を構成する必要があります。
    > ご使用のサブスクリプションで SSO が有効になると通知が届きます。
    > 
    > 
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが FM:Systems にログインできるようにするには、そのユーザーを FM:Systems にプロビジョニングする必要があります。  
FM:Systems の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. Web ブラウザー ウィンドウで、FM:Systems 企業サイトに管理者としてログインします。

2. **[システム管理] \> [セキュリティの管理] \> [ユーザー] \> [ユーザー一覧]** の順にクリックします。
   
    ![System Administration](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "System Administration")

3. **[新しいユーザーの作成]**をクリックします。
   
    ![Create New User](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Create New User")

4. **[Create User]** セクションで、次の手順に従います。
   
    ![Create User](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Create User")
   
    a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントのユーザー名、パスワード、確認パスワード、電子メール アドレス、および従業員 ID を入力します。
   
    b. **[次へ]**をクリックします。
 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>ユーザーを FM:Systems に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **FM:Systems ** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Feb17_HO2-->


