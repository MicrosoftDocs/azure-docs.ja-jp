---
title: "チュートリアル: Azure Active Directory と Picturepark の統合 | Microsoft Docs"
description: "Azure Active Directory で Picturepark を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: e24bd626cd950f6e6d9474d1bd5f97c3ea4fb925
ms.openlocfilehash: 626bdc99702b38c127beba54979add9b1cbd633f


---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>チュートリアル: Azure Active Directory と Picturepark の統合
このチュートリアルでは、Azure と Picturepark の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Picturepark テナント

このチュートリアルを完了すると、Picturepark に割り当てた Azure AD ユーザーは、Picturepark 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Picturepark のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scenario")

## <a name="enabling-the-application-integration-for-picturepark"></a>Picturepark のアプリケーション統合の有効化
このセクションでは、Picturepark のアプリケーション統合を有効にする方法を説明します。

**Picturepark のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-picturepark-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-picturepark-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-picturepark-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に「**Picturepark**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-picturepark-tutorial/IC795056.png "Application Gallery")
7. 結果ウィンドウで **[Picturepark]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")


## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Picturepark に対する認証を行えるようにする方法を説明します。  

Picturepark にシングル サインオンを構成するには、証明書の拇印の値を取得する必要があります。  

この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value （証明書の拇印の値を取得する方法）](http://youtu.be/YKQF266SAxI)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Picturepark** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configure Single Sign-On")
2. **[ユーザーの Picturepark へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページで、**[Picturepark サインオン URL]** ボックスに、"*http://company.picturepark.com*" のパターンを使用して URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configure App URL")
4. **[Picturepark でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configure Single Sign-On")
5. 別の Web ブラウザーのウィンドウで、Picturepark 企業サイトに管理者としてログインします。
6. 上部にあるツールバーで **[Administrative tools]**、**[Management Console]** の順にクリックします。
   
   ![管理コンソール](./media/active-directory-saas-picturepark-tutorial/IC795062.png "管理コンソール")
7. **[Authentication]**、**[Identity providers]** の順にクリックします。
   
   ![Authentication](./media/active-directory-saas-picturepark-tutorial/IC795063.png "Authentication")
8. **[Identity provider configuration]** セクションで、次の手順に従います。
   
   ![ID プロバイダーの構成](./media/active-directory-saas-picturepark-tutorial/IC795064.png "ID プロバイダーの構成")
   
   1. **[追加]**をクリックします。
   2. 構成の名前を入力します。
   3. **[Set as default]**を選びます。
   4. Azure クラシック ポータルの **[Picturepark でのシングル サインオンの構成]** ダイアログ ページで、**[SAML SSO URL]** の値をコピーし、**[Issuer URI]** ボックスに貼り付けます。
   5. エクスポートした証明書から **[拇印]** の値をコピーし、**[Trusted Issuer Thumb Print]** ボックスに貼り付けます。  
      
      > [!TIP]
      > 詳細については、「 [How to retrieve a certificate's thumbprint value (証明書のサムプリント値を取得する方法)](http://youtu.be/YKQF266SAxI)
      > 

9. **[JoinDefaultUsersGroup]**をクリックします。
10. **[Claim]** ボックスに **Emailaddress** 属性を設定するには、「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**」と入力します。

      ![構成](./media/active-directory-saas-picturepark-tutorial/IC795065.png "構成") a. **[保存]**をクリックします。
11. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Azure AD ユーザーが Picturepark にログインできるようにするには、ユーザーを Picturepark にプロビジョニングする必要があります。  
Picturepark の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Picturepark** テナントにログインします。
2. 上部にあるツールバーで **[Administrative tools]**、**[Users]** の順にクリックします。
   
   ![ユーザー](./media/active-directory-saas-picturepark-tutorial/IC795067.png "Users")
3. **[Users overview]** タブで、**[New]** をクリックします。
   
   ![ユーザー管理](./media/active-directory-saas-picturepark-tutorial/IC795068.png "ユーザー管理")
4. **[Create User]** ダイアログで、次の手順に従います。
   
   ![Create User](./media/active-directory-saas-picturepark-tutorial/IC795069.png "Create User")
   
  1. プロビジョニングする有効な Azure Active Directory ユーザーの情報を、**[Email Address (電子メール アドレス)]**、**[Password (パスワード)]**、**[Confirm Password (パスワードの確認)]**、**[First Name (名)]**、**[Last Name (姓)]**、**[Company (会社)]**、**[Country (国)]**、**[ZIP (郵便番号)]**、**[City (都市)]** の各ボックスに入力します。
  2. **[Language]**を選択します。
  3. **[作成]**をクリックします。

> [!NOTE]
> Picturepark から提供されている他の Picturepark ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Picturepark に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Picturepark** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-picturepark-tutorial/IC795070.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-picturepark-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Feb17_HO1-->


