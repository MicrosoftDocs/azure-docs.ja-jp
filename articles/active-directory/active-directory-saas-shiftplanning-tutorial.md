---
title: "チュートリアル: Azure Active Directory と ShiftPlanning の統合 | Microsoft Docs"
description: "Azure Active Directory で ShiftPlanning を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 6104bd7e22d855c4c8737ef1080bfd445b29eafb


---
# <a name="tutorial-azure-active-directory-integration-with-shiftplanning"></a>チュートリアル: Azure Active Directory と ShiftPlanning の統合
このチュートリアルの目的は、Azure と ShiftPlanning の統合を示すことです。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* ShiftPlanning でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、ShiftPlanning に割り当てた Azure AD ユーザーは、ShiftPlanning 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. ShiftPlanning のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Scenario")

## <a name="enabling-the-application-integration-for-shiftplanning"></a>ShiftPlanning のアプリケーション統合の有効化
このセクションでは、ShiftPlanning のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-shiftplanning-perform-the-following-steps"></a>ShiftPlanning のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**ShiftPlanning**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Application Gallery")

7. 結果ウィンドウで **[ShiftPlanning]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ShiftPlanning でAzure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。  
この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  
この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **ShiftPlanning** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configure Single Sign-On")

2. **[ユーザーの ShiftPlanning へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configure Single Sign-On")

3. **[アプリケーション URL の構成]** ページで、**[ShiftPlanning サインオン URL]** ボックスに、"*https://company.shiftplanning.com/includes/saml/*" のパターンを使用して URL を入力し、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configure App URL")

4. **[ShiftPlanning でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configure Single Sign-On")

5. 別の Web ブラウザーのウィンドウで、 **ShiftPlanning** 企業サイトに管理者としてログインします。
6. 上部のメニューで **[Admin]**をクリックします。
   
    ![管理](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

7. **[Integration]** の **[Single Sign-On]** をクリックします。
   
    ![シングル サインオン](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Single Sign-On")

8. **[Single Sign-On Options]** セクションで、次の手順に従います。
   
    ![シングル サインオン](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Single Sign-On")
   
    1. **[SAML Enabled]**を選択します。

    2. **[Allow Password Login]**

    3. Azure クラシック ポータルで、**[ShiftPlanning でのシングル サインオンの構成]** ページの **[リモート ログイン URL]** の値をコピーし、**[SAML Issuer URL]** ボックスに貼り付けます。

    4. Azure クラシック ポータルで、**[ShiftPlanning でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[Remote Logout URL]** ボックスに貼り付けます。

    5. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。  
       
    > [!TIP]
    > 詳細については、 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 

    6. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[X.509 Certificate]** テキスト ボックスに貼り付けます。

    7. **[設定の保存]**をクリックします。

9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが ShiftPlanning にログインできるようにするには、ユーザーを ShiftPlanning にプロビジョニングする必要があります。  
ShiftPlanning の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。
1. **ShiftPlanning** 企業サイトに管理者としてログインします。

2. **[Admin]**をクリックします。
   
    ![管理](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

3. **[Staff]**をクリックします。
   
    ![Staff](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Staff")

4. **[Actions]** の **[Add Employee]** をクリックします。
   
    ![Add Employees](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Add Employees")

5. **[Add Employees]** セクションで、次の手順に従います。
   
    ![Save Employees](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Save Employees")
   
    1. プロビジョニングする有効な AAD アカウントの**姓**、**名**、**電子メール アドレス**を、対応するテキスト ボックスに入力します。

    2. **[Save Employees]**をクリックします。

> [!NOTE]
> ShiftPlanning から提供されている他の ShiftPlanning ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-shiftplanning-perform-the-following-steps"></a>ユーザーを ShiftPlanning に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **ShiftPlanning** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Yes")
 
シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Jan17_HO1-->


