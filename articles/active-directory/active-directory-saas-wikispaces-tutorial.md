---
title: "チュートリアル: Azure Active Directory と Wikispaces の統合 | Microsoft Docs"
description: "Azure Active Directory で Wikispaces を使用してシングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 665b95aa-f7f5-4406-9e2a-6fc299a1599c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1cef7ff21a8d076c89688f1fe75cebdb7c468199
ms.openlocfilehash: c7569177db0821b36e49439ec54224e1aeb9ad9d
ms.lasthandoff: 02/11/2017


---
# <a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>チュートリアル: Azure Active Directory と Wikispaces の統合
このチュートリアルでは、Azure と Wikispaces の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Wikispaces でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Wikispaces に割り当てた Azure AD ユーザーは、Wikispaces 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Wikispaces のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "シナリオ")

## <a name="enabling-the-application-integration-for-wikispaces"></a>Wikispaces のアプリケーション統合の有効化
このセクションでは、Wikispaces のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>Wikispaces のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に「**Wikispaces**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Application Gallery")

7. 結果ウィンドウで **[Wikispaces]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Wikispaces に対する認証を行うことができるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **[Wikispaces]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configure Single Sign-On")

2. **[ユーザーの Wikispaces へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configure Single Sign-On")

3. **[アプリケーション URL の構成]** ページの **[Wikispaces サインオン URL]** ボックスに、" *http://company.wikispaces.net* " というパターンの URL を入力し、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configure App URL")

4. **[Wikispaces でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configure Single Sign-On")

5. Wikispaces サポート チームに、メタデータ ファイルを送信します。
   
    > [!NOTE]
    > シングル サインオンの構成は、Wikispaces サポート チームが実行する必要があります。 構成が完了すると、サポート チームから通知が届きます。
    > 
    > 

6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Azure AD ユーザーが Wikispaces にログインできるようにするには、そのユーザーを Wikispaces にプロビジョニングする必要があります。  
Wikispaces の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。
1. **Wikispaces** 企業サイトに管理者としてログインします。

2. **[メンバー]**に移動します。
   
    ![メンバー](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "メンバー")

3. **[ユーザーの招待]**をクリックします。
   
    ![ユーザーの招待](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "ユーザーの招待")

4. **[ユーザーの招待]** セクションで、次の手順を実行します。
   
    ![ユーザーの招待](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "ユーザーの招待")
   
    a. プロビジョニングする有効な AAD アカウントの関連するテキスト ボックスに、 **名前または電子メール アドレス** を入力します。
   
    b. [ **送信**] をクリックします。  
      
    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    > 
    > 

> [!NOTE]
> Wikispaces から提供されている他の Wikispaces ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>ユーザーを Wikispaces に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Wikispaces** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


