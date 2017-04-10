---
title: "チュートリアル: Azure Active Directory と RightAnswers の統合 | Microsoft Docs"
description: "Azure Active Directory で RightAnswers を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7f09e25a-a716-41e1-8ca3-fd00e3d1b8cc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 9251f3f9311e1cd4b1d57c611cc1783855d8d2af


---
# <a name="tutorial-azure-active-directory-integration-with-rightanswers"></a>チュートリアル: Azure Active Directory と RightAnswers の統合
このチュートリアルでは、Azure と RightAnswers の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* RightAnswers でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、RightAnswers に割り当てた Azure AD ユーザーは、「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. RightAnswers のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Scenario")

## <a name="enabling-the-application-integration-for-rightanswers"></a>RightAnswers のアプリケーション統合の有効化
このセクションでは、RightAnswers のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-rightanswers-perform-the-following-steps"></a>RightAnswers のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**RightAnswers**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Application Gallery")

7. 結果ウィンドウで **[RightAnswers]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで RightAnswers に対する認証を行えるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **RightAnswers** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configure Single Sign-On")

2. **[ユーザーの RightAnswers へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configure Single Sign-On")

3. **[アプリケーション設定の構成]** ページの **[サインオン URL]** ボックスに、ユーザーが RightAnswers アプリケーションへのサインオンに使用する URL (例: *https://fortify.rightanswers.com/portal/ss/*) を入力し、**[次へ]** をクリックします。
   
    ![アプリケーションの設定の構成](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Configure App Settings")

4. **[RightAnswers でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、メタデータ ファイルをコンピューターのローカルに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configure Single Sign-On")

5. ダウンロードしたメタデータ ファイルを RightAnswers サポート チームに送信します。
   
    > [!NOTE]
    > RightAnswers サポート チームが、実際に SSO を構成する必要があります。
    > ご使用のサブスクリプションで SSO が有効になると通知が届きます。
    > 
    > 

6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが RightAnswers にログインできるようにするには、ユーザーを RightAnswers にプロビジョニングする必要があります。  
RightAnswers の場合、プロビジョニングは自動化されています。  
アイテムを操作することはありません。

最初のシングル サインオンの試行中に、必要に応じてユーザーが自動的に作成されます。

> [!NOTE]
> RightAnswers から提供されている他の RightAnswers ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-rightanswers-perform-the-following-steps"></a>ユーザーを RightAnswers に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **RightAnswers** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Jan17_HO1-->


