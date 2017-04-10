---
title: "チュートリアル: Azure Active Directory と TalentLMS の統合 | Microsoft Docs"
description: "Azure Active Directory で TalentLMS を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: a9de3004a1968f514227f0ba5dfde0f562a2b392
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>チュートリアル: Azure Active Directory と TalentLMS の統合
このチュートリアルでは、Azure と TalentLMS の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* TalentLMS テナント

このチュートリアルを完了すると、TalentLMS に割り当てた Azure AD ユーザーは、TalentLMS 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. TalentLMS のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Scenario")

## <a name="enable-the-application-integration-for-talentlms"></a>TalentLMS のアプリケーション統合の有効化
このセクションでは、TalentLMS のアプリケーション統合を有効にする方法について説明します。

**TalentLMS のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**TalentLMS**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Application Gallery")

7. 結果ウィンドウで **[TalentLMS]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

## <a name="configure-single-sign-on"></a>Configure single sign-on
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで TalentLMS に対する認証を行うことができるようにする方法を説明します。

TalentLMS の SSO を構成するには、証明書からサムプリント値を取得する必要があります。  

この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **TalentLMS** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configure single sign-on")

2. **[ユーザーの TalentLMS へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページで、**[TalentLMS サインイン URL]** テキスト ボックスに、"*https://\<テナント名\>.TalentLMSapp.com*" というパターンで URL を入力し、**[次へ]** をクリックします。
   
    ![Sign on URL](./media/active-directory-saas-talentlms-tutorial/IC777294.png "Sign on URL")

4. **[TalentLMS でのシングル サインオンの構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、証明書ファイルを **c:\\TalentLMS.cer** としてローカルに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configure Single Sign-On")

5. 別の Web ブラウザー ウィンドウで、TalentLMS 企業サイトに管理者としてログインします。

6. **[Account & Settings]** セクションで、**[Users]** タブをクリックします。
   
    ![Account & Settings](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Account & Settings")

7. **[Single Sign-On (SSO)]**をクリックします。

8. [Single Sign-On] セクションで、次の手順に従います。
   
    ![シングル サインオン](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Single Sign-On")   
  1. **[SSO integration type]** ボックスの一覧から **[SAML 2.0]** を選択します。
  2. Azure クラシック ポータルの **[TalentLMS でのシングル サインオンの構成]** ダイアログ ページで、**[ID プロバイダーの ID]** 値をコピーして、**[Identity provider (IdP)]** テキスト ボックスに貼り付けます。
  3. エクスポートした証明書から **[拇印]** の値をコピーし、**[証明書の指紋]** ボックスに貼り付けます。
      
     >[!TIP]
     >詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。 
     >    

  4. Azure クラシック ポータルの **[TalentLMS でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーし、**[Remote sign-in URL]** テキスト ボックスに貼り付けます。 
  5. Azure クラシック ポータルの **[TalentLMS でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログアウト URL]** の値をコピーし、**[Remote sign-out URL]** テキスト ボックスに貼り付けます。
  6. 次の入力を行います。 
    * **[TargetedID]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name** と入力します。
    * **[First name]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** と入力します。
    * **[Last name]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** と入力します。
    * **[Email]** ボックスに、**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** と入力します。
  7. **[Save]**をクリックします。

9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configure Single Sign-On")

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
Azure AD ユーザーが TalentLMS にログインできるようにするには、そのユーザーを TalentLMS にプロビジョニングする必要があります。  

* TalentLMS の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **TalentLMS** テナントにログインします。

2. **[Users]** をクリックし、**[Add User]** をクリックします。

3. **[Add user]** ダイアログ ページで、次の手順に従います。
   
    ![ユーザーの追加](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Add User")   
  1. Azure AD のユーザー アカウントに関連する属性の値を次のテキスト ボックスに入力します。**[First name]**、**[Last name]**、**[Email address]**。
  2. **[ユーザーの追加]**をクリックします。

>[!NOTE]
>TalentLMS から提供されている他の TalentLMS ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを TalentLMS に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **TalentLMS **アプリケーション統合ページで、**[ユーザーの割り当て**] をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


