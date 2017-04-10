---
title: "チュートリアル: Azure Active Directory と Mindflash の統合 | Microsoft Docs"
description: "Azure Active Directory で Mindflash を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bdf91993-aaaa-4598-89b7-77ef8ca065d5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 57a6428315a4942a0c6566fef5a4db4ee66cb55a
ms.openlocfilehash: 89bd515fa988e0347508b739dd0676c5eeb1f44d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-mindflash"></a>チュートリアル: Azure Active Directory と Mindflash の統合
このチュートリアルでは、Azure と Mindflash の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Mindflash でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、Mindflash に割り当てた Azure AD ユーザーは、Mindflash 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Mindflash のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-mindflash-tutorial/IC787132.png "Scenario")

## <a name="enabling-the-application-integration-for-mindflash"></a>Mindflash のアプリケーション統合の有効化
このセクションでは、Mindflash のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-mindflash-perform-the-following-steps"></a>Mindflash のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-mindflash-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-mindflash-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-mindflash-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Mindflash**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-mindflash-tutorial/IC787133.png "Application Gallery")
7. 結果ウィンドウで **[Mindflash]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Mindflash に対する認証を行うことができるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **[Mindflash]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-mindflash-tutorial/IC787135.png "Configure Single Sign-On")
2. **[ユーザーの Mindflash へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-mindflash-tutorial/IC787136.png "Configure Single Sign-On")
3. **[アプリ URL の構成]** ページの **[サインオン URL]** ボックスに、"*http://company.mindflash.com*" というパターンの URL を入力して、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-mindflash-tutorial/IC787137.png "Configure App URL")
4. **[Mindflash でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-mindflash-tutorial/IC787138.png "Configure Single Sign-On")
5. Mindflash サポート チームに、メタデータ ファイルを送信します。
   
   > [!NOTE]
   > シングル サインオンの構成は、Mindflash サポート チームが実行する必要があります。 構成が完了すると、サポート チームから通知が届きます。
   > 
   > 
6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-mindflash-tutorial/IC787139.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Mindflash にログインできるようにするには、そのユーザーを Mindflash にプロビジョニングする必要があります。 Mindflash の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。
1. **Mindflash** の企業サイトに管理者としてログインします。
2. **[ユーザーの管理]**に移動します。
   
   ![Manage Users](./media/active-directory-saas-mindflash-tutorial/IC787140.png "Manage Users")
3. **[ユーザーの追加]** をクリックし、**[新規]** をクリックします。
4. **[新しいユーザーの追加]** セクションで、次の手順に従います。
   
   ![Add New Users](./media/active-directory-saas-mindflash-tutorial/IC787141.png "Add New Users")
   
   1. プロビジョニングする有効な AAD アカウントの**姓**、**名**、**電子メール アドレス**を、対応するテキスト ボックスに入力します。
   2. **[追加]**をクリックします。

>[!NOTE]
>Mindflash から提供されている他の Mindflash ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-mindflash-perform-the-following-steps"></a>ユーザーを Mindflash に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Mindflash** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-mindflash-tutorial/IC787142.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-mindflash-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


