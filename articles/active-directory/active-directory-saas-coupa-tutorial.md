---
title: "チュートリアル: Azure Active Directory と Coupa の統合 | Microsoft Docs"
description: "Azure Active Directory で Coupa を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d1660c9ea528741ea02e6bbae7b1f8fd7afea0eb
ms.openlocfilehash: c952975919cfd948f1b9ea93ff2ac2641a53f923
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>チュートリアル: Azure Active Directory と Coupa の統合
このチュートリアルでは、Azure と Coupa の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Coupa でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、Coupa に割り当てた Azure AD ユーザーは、「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Coupa のアプリケーション統合の有効化
* シングル サインオンの構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-coupa-tutorial/IC791897.png "Scenario")

## <a name="enable-the-application-integration-for-coupa"></a>Coupa のアプリケーション統合の有効化
このセクションでは、Coupa のアプリケーション統合を有効にする方法について説明します。

**Coupa のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-coupa-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-coupa-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-coupa-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Coupa**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-coupa-tutorial/IC791898.png "Application Gallery")
7. 結果ウィンドウで **[Coupa]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Coupa に対する認証を行うことができるようにする方法を説明します。  

Coupa にシングル サインオンを構成するには、証明書から拇印の値を取得する必要があります。 この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Coupa 企業サイトに管理者としてサインオンします。
2. **[Setup]\>[Security Control]** の順に移動します。
   
   ![Security Controls](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")
3. コンピューターに Coupa のメタデータ ファイルをダウンロードするには、 **[SP メタデータのダウンロードとインポート]**をクリックします。
   
   ![Coupa SP metadata](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP metadata")
4. 別のブラウザー ウィンドウで、Azure クラシック ポータルにサインオンします。
5. **Coupa** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-coupa-tutorial/IC791902.png "Configure Single Sign-On")
6. **[ユーザーの Coupa へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-coupa-tutorial/IC791903.png "Configure Single Sign-On")
7. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-coupa-tutorial/IC791904.png "Configure App URL")   
   1. **[サインオン URL]** ボックスに、ユーザーが Coupa アプリケーションへのサインオンに使用する URL (例: "*http://company.Coupa.com*") を入力します。
   2. ダウンロードした Coupa メタデータ ファイルを開き、 **AssertionConsumerService index/URL**をコピーします。
   3. **[Coupa 応答 URL]** ボックスに、**AssertionConsumerService index/URL** 値を貼り付けます。
   4. **[次へ]**をクリックします。
8. **[Coupa でのシングル サインオンの構成]** ページで、メタデータ ファイルをダウンロードするには、**[メタデータのダウンロード]** をクリックし、メタデータ ファイルをコンピューターにローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-coupa-tutorial/IC791905.png "Configure Single Sign-On")
9. Coupa 企業サイトで、**[設定]\>[セキュリティ コントロール]** の順にクリックします。
   
   ![Security Controls](./media/active-directory-saas-coupa-tutorial/IC791900.png "Security Controls")
10. **[Coupa 資格情報を使用してログイン]** セクションで、次の手順を実行します。  

   ![Log in using Coupa credentials](./media/active-directory-saas-coupa-tutorial/IC791906.png "Log in using Coupa credentials") 
   1. **[SAML を使用してログイン]**を選択します。
   2. **[参照]** をクリックして、ダウンロードした Azure Active メタデータ ファイルをアップロードします。
   3. [ **Save**] をクリックします。
11. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
   ![シングル サインオンの構成](./media/active-directory-saas-coupa-tutorial/IC791907.png "Configure Single Sign-On")
    
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Coupa にログインできるようにするには、そのユーザーを Coupa にプロビジョニングする必要があります。  

* Coupa の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **Coupa** 企業サイトに管理者としてログインします。
2. 上部のメニューで、**[設定]**、**[ユーザー]** の順にクリックします。
   
   ![ユーザー](./media/active-directory-saas-coupa-tutorial/IC791908.png "Users")
3. **[作成]**をクリックします。
   
   ![Create Users](./media/active-directory-saas-coupa-tutorial/IC791909.png "Create Users")
4. **[ユーザーを作成]** セクションで、次の手順を実行します。
   
   ![ユーザーの詳細](./media/active-directory-saas-coupa-tutorial/IC791910.png "User Details")
   
   1. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの属性として、**ログイン**、**名**、**姓**、**シングル サインオン ID**、**電子メール**を入力します。
   2. **[作成]**をクリックします。   
   >[!NOTE]
   >Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。 
   > 

>[!NOTE]
>Coupa から提供されている他の Coupa ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Coupa に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Coupa** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-coupa-tutorial/IC791911.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-coupa-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


