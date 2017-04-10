---
title: "チュートリアル: Azure Active Directory と BlueJeans の統合 | Microsoft Docs"
description: "Azure Active Directory で BlueJeans を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: e8df27b17ac0e839efdd302ffa40fafe688a22b4
ms.openlocfilehash: aaa1c06da8b53a45a1e675f175dd3adcee20e910
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-ad-integration-with-bluejeans"></a>チュートリアル: Azure AD と BlueJeans の統合
このチュートリアルでは、Azure と BlueJeans の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* BlueJeans でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、BlueJeans に割り当てた Azure AD ユーザーは、BlueJeans 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* BlueJeans のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scenario")

## <a name="enable-the-application-integration-for-bluejeans"></a>BlueJeans のアプリケーション統合の有効化
このセクションでは、BlueJeans のアプリケーション統合を有効にする方法について説明します。

**BlueJeans のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**BlueJeans**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Application Gallery")
7. 結果ウィンドウで **[BlueJeans]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで BlueJeans に対する認証を行えるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **BlueJeans** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Configure single sign-on")
2. **[ユーザーの BlueJeans へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configure Single Sign-On")
3. **[アプリ URL の構成]** ページの **[BlueJeans サインオン URL]** ボックスに、"*https://company.BlueJeans.com*" というパターンの URL を入力して、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configure App URL")
4. **[BlueJeans でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configure Single Sign-On")
5. 別の Web ブラウザー ウィンドウで、 **BlueJeans** 企業サイトに管理者としてログインします。
6. **[管理] \> [グループ設定] \> [セキュリティ]** の順にクリックします。
   
   ![管理](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")
7. **[セキュリティ]** セクションで、次の手順を実行します。
   
   ![SAML シングル サインオン](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign On")   
   1. **[SAML シングル サインオン]**を選択します。
   2. **[自動プロビジョニングの有効化]**を選択します。
8. 次の手順を実行します。
   
   ![証明書パス](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Certificate Path")
   
   1. **[ファイルの選択]**をクリックし、ダウンロードした証明書をアップロードします。
   2. Azure クラシック ポータルで、**[BlueJeans でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[ログイン URL]** ボックスに貼り付けます。
   3. Azure クラシック ポータルで、**[BlueJeans でのシングル サインオンの構成]** ダイアログ ページの **[パスワード変更 URL]** の値をコピーし、**[パスワード変更の URL]** ボックスに貼り付けます。
   4. Azure クラシック ポータルで、**[BlueJeans でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[ログアウト URL]** ボックスに貼り付けます。
9. 次の手順を実行します。
   
   ![変更を保存](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Save Changes")
   1. **[User ID]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name** と入力します。
   2. **[メール]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name** と入力します。
   3. **[変更を保存]**をクリックします。
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
   ![シングル サインオンの構成](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configure Single Sign-On")
    
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが BlueJeans にログインできるようにするには、ユーザーを BlueJeans にプロビジョニングする必要があります。  

* BlueJeans の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **BlueJeans** 企業サイトに管理者としてログインします。
2. **[管理] \> [ユーザーの管理] \> [ユーザーの追加]** の順にクリックします。
   
   ![管理](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")
   
   >[!IMPORTANT]
   >**[ユーザーの追加]** タブは、**[セキュリティ]** タブの **[自動プロビジョニングの有効化]** がオフになっている場合にのみ使用できます。 
   > 
3. **[ユーザーの追加]** セクションで、次の手順を実行します。
   
  ![ユーザーの追加](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Add User")
   
  1. 対応するテキスト ボックスに、プロビジョニングする有効な AAD アカウントの **BlueJeans ユーザー名**、**メール アドレス**、**BlueJeans ミーティング ID**、**モデレーター パスコード**、**フル ネーム**、**会社**を入力します。
  2. **[ユーザーの追加]**をクリックします。

>[!NOTE]
>BlueJeans から提供されている他の BlueJeans ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを BlueJeans に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **BlueJeans** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


