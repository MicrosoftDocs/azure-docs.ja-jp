---
title: "チュートリアル: Azure Active Directory と SCC LifeCycle の統合 | Microsoft Docs"
description: "Azure Active Directory で SCC LifeCycle を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 86228225275c4486852c17803a99a194458196b7
ms.openlocfilehash: b2cf981e6a655f5c7f0d429714e0efb8e88e178c
ms.lasthandoff: 01/05/2017


---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>チュートリアル: Azure Active Directory と SCC LifeCycle の統合
このチュートリアルでは、Azure と SCC LifeCycle の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* SCC LifeCycle でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、SCC LifeCycle に割り当てた Azure AD ユーザーは、SCC LifeCycle 企業サイト (サービス プロバイダーが開始したサインオン) または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. SCC LifeCycle のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Scenario")

## <a name="enabling-the-application-integration-for-scc-lifecycle"></a>SCC LifeCycle のアプリケーション統合の有効化
このセクションでは、SCC LifeCycle のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-scc-lifecycle-perform-the-following-steps"></a>SCC LifeCycle のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**SCC LifeCycle**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Application Gallery")

7. 結果ウィンドウで **[SCC LifeCycle]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで SCC LifeCycle に対する認証を行えるようにする方法について説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **SCC LifeCycle** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configure Single Sign-On")

2. **[ユーザーの SCC LifeCycle へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configure Single Sign-On")

3. **[アプリケーション URL の構成]** ページの **[サインオン URL]** ボックスに、"*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" のパターンを使用して、ユーザーが SCC LifeCycle アプリケーションへのサインオンに使用する URL を入力し、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configure App URL")

4. **[SCC LifeCycle でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configure Single Sign-On")

5. このメタデータ ファイルを SCC LifeCycle サポート チームに転送します。
   
    > [!NOTE]
    > SCC LifeCycle サポート チームがシングル サインオンを有効にする必要があります。
    > 
    > 

6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが SCC LifeCycle にログインできるようにするには、ユーザーを SCC LifeCycle にプロビジョニングする必要があります。

SCC LifeCycle へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
割り当て済みユーザーが SCC LifeCycle にログインしようとすると、必要に応じて SCC LifeCycle アカウントが自動的に作成されます。

> [!NOTE]
> AAD ユーザー アカウントをプロビジョニングするには、他の SCC LifeCycle ユーザー アカウント作成ツールまたは SCC LifeCycle から提供されている API を使用できます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-scc-lifecycle-perform-the-following-steps"></a>ユーザーを SCC LifeCycle に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **SCC LifeCycle** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


