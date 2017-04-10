---
title: "チュートリアル: Azure Active Directory と Sciforma の統合 | Microsoft Docs"
description: "Azure Active Directory で Sciforma を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: abbfb5ac-7687-4153-b263-8090102dae37
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 166524147c050512a84c1c0340de93960d8b7632


---
# <a name="tutorial-azure-ad-integration-with-sciforma"></a>チュートリアル: Azure AD と Sciforma の統合
このチュートリアルでは、Azure と Sciforma の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Sciforma テナント

このチュートリアルを完了すると、Sciforma に割り当てた Azure AD ユーザーは、Sciforma 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Sciforma のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-sciforma-tutorial/IC777369.png "Scenario")

## <a name="enabling-the-application-integration-for-sciforma"></a>Sciforma のアプリケーション統合の有効化
このセクションでは、Sciforma のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-sciforma-perform-the-following-steps"></a>Sciforma のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-sciforma-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-sciforma-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-sciforma-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**Sciforma**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-sciforma-tutorial/IC777370.png "Application Gallery")

7. 結果ウィンドウで **[Sciforma]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Sciforma](./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Sciforma に対する認証を行えるようにする方法について説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Sciforma** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configure single sign-on")

2. **[ユーザーの Sciforma へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configure single sign-on")

3. **[アプリケーション URL の構成]** ページで、**[Sciforma サインイン URL]** ボックスに、"*https://\<テナント名\>.Sciforma.com*" のパターンを使用して URL を入力し、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configure app URL")

4. **[Sciforma でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、データ ファイルを **c:\\SciformaMetaData.xml** としてローカルに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configure single sign-on")

5. メタデータ ファイルを Sciforma サポート チームに転送します。 サポート チームが、シングル サインオンを構成します。

6. [シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configure single sign-on")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Sciforma へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  
割り当て済みユーザーがアクセス パネルを使用して Sciforma にログインしようとすると、そのユーザーが存在するかどうかが Sciforma によって確認されます。  
使用可能なユーザー アカウントがない場合、ユーザー アカウントは Sciforma により自動的に作成されます。

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-sciforma-perform-the-following-steps"></a>ユーザーを Sciforma に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Sciforma** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-sciforma-tutorial/IC777377.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-sciforma-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Jan17_HO1-->


