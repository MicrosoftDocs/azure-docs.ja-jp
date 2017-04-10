---
title: "チュートリアル: Azure Active Directory と SugarCRM の統合 | Microsoft Docs"
description: "Azure Active Directory で Sugar CRM を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: acd11de9f2b987c3c0bc6d74cff8019aca92f437
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-integration-with-sugarcrm"></a>チュートリアル: Azure Active Directory と Sugar CRM の統合
このチュートリアルでは、Azure と Sugar CRM の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Sugar CRM でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、Sugar CRM に割り当てた Azure AD ユーザーは、SSO を使って Sugar CRM 企業サイト (サービス プロバイダーが開始したサインオン) で、または「[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにサインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Sugar CRM のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "Scenario")

## <a name="enable-the-application-integration-for-sugar-crm"></a>Sugar CRM のアプリケーション統合の有効化
このセクションでは、Sugar CRM のアプリケーション統合を有効にする方法を説明します。

**Sugar CRM のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に「**Sugar CRM**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "Application Gallery")

7. 結果ウィンドウで **[Sugar CRM]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Sugar CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "Sugar CRM")

## <a name="configure-single-sign-on"></a>Configure single sign-on
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、Sugar CRM で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。  

この手順の途中で、base-64 でエンコードされた証明書を Sugar CRM テナントにアップロードする必要があります。  

この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **[Sugar CRM]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "Configure Single Sign-On")

2. **[ユーザーの Sugar CRM へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "Configure Single Sign-On")

3. **[アプリケーション URL の構成]** ページの **[サインオン URL]** テキストボックスに、ユーザーが Sugar CRM アプリケーションにサインオンするときに使用する URL (例: "*http://company.sugarondemand.com*") を入力し、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "Configure App URL")

4. **[Sugar CRMでのシングル サインオンの構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "Configure Single Sign-On")

5. 別の Web ブラウザー ウィンドウで、Sugar CRM 企業サイトに管理者としてログインします。

6. **[Admin]**に移動します。
   
    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")

7. **[Administration]** セクションで、**[Password Management]** をクリックします。
   
    ![Administration](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "Administration")

8. **[Enable SAML Authentication]**を選択します。
   
    ![Administration](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "Administration")

9. **[SAML Authentication]** セクションで、次の手順に従います。
   
    ![SAML Authentication](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "SAML Authentication")   
  1. Azure クラシック ポータルの **[Sugar CRM でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーし、**[Login URL (ログイン URL)]** テキスト ボックスに貼り付けます。
  2. Azure クラシック ポータルの **[Sugar CRM でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーし、**[SLO URL]** テキスト ボックスに貼り付けます。
  3. ダウンロードした証明書から **Base-64 でエンコードされた** ファイルを作成します。
      
     >[!TIP]
     >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。 
     > 

  4. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、証明書全体を **[X.509 Certificate]** テキスト ボックスに貼り付けます。
  5. **[Save]**をクリックします。

10. Azure クラシック ポータルの **[Sugar CRM でのシングル サインオンの構成]** ダイアログ ページで、シングル サインオンの構成確認を選択し、**[完了]** をクリックします。
    
    ![シングル サインオンの構成](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "Configure Single Sign-On")

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
Azure AD ユーザーが Sugar CRM にログインできるようにするには、そのユーザーを Sugar CRM にプロビジョニングする必要があります。

Sugar CRM の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Spring CM** 企業サイトに管理者としてログインします。
2. **[Admin]**に移動します。
   
    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "Admin")

3. **[Administration]** セクションで、**[User Management]** をクリックします。
   
    ![Administration](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "Administration")

4. **[Users]\>[Create New User]** の順に選択します。
   
    ![Create New User](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "Create New User")

5. **[User Profile]** タブで、次の手順に従います。
   
    ![New User](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "New User")
  * 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントのユーザー名、姓、および電子メール アドレスを入力します。
6. **[Status]** として、**[Active]** を選択します。

7. [Password] タブで、次の手順に従います。
   
    ![New User](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "New User")
  1. 該当するテキスト ボックスにパスワードを入力します。
  2. **[保存]**をクリックします。

>[!NOTE]
>Sugar CRM から提供されている他の Sugar CRM ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Sugar CRM に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Sugar CRM** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


