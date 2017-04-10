---
title: "チュートリアル: Azure Active Directory と Clever の統合 | Microsoft Docs"
description: "Azure Active Directory で Clever を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ee53124019cbee9c42f94cde411a9b653413011a
ms.openlocfilehash: a219a63f4de17c18d58bfd529e8372b99ccb4e41
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>チュートリアル: Azure Active Directory と Clever の統合
このチュートリアルでは、Azure と Clever の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Clever テナント

このチュートリアルを完了すると、Clever に割り当てた Azure AD ユーザーは、Clever 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Clever のアプリケーション統合の有効化
* シングル サインオンの構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-clever-tutorial/IC798977.png "Scenario")

## <a name="enable-the-application-integration-for-clever"></a>Clever のアプリケーション統合の有効化
このセクションでは、Clever のアプリケーション統合を有効にする方法について説明します。

**Clever のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-clever-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-clever-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-clever-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Clever**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-clever-tutorial/IC798978.png "Application Gallery")
7. 結果ウィンドウで **[Clever]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Clever に対する認証を行えるようにする方法を説明します。  

Clever アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを **SAML トークン属性** の構成に追加する必要があります。  

次のスクリーンショットはその例です。

![属性](./media/active-directory-saas-clever-tutorial/IC798980.png "Attributes")

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Clever** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-clever-tutorial/IC784682.png "Configure Single Sign-On")
2. **[ユーザーの Clever へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-clever-tutorial/IC798981.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページの **[Clever のサインオン URL]** ボックスに、ユーザーが Clever アプリケーションにサインオンするときに使用する URL (例: *https://clever.com/in/azsandbox*) を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-clever-tutorial/IC798982.png "Configure App URL")
4. **[Clever でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、コンピューターでメタデータ ファイルをローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-clever-tutorial/IC798983.png "Configure Single Sign-On")
5. 別の Web ブラウザー ウィンドウで、Clever 企業サイトに管理者としてログインします。
6. ツールバーで、 **[インスタント ログイン]**をクリックします。
   
   ![Instant Login](./media/active-directory-saas-clever-tutorial/IC798984.png "Instant Login")
7. **[インスタント ログイン]** ページで、次の手順を実行します。
   
   ![Instant Login](./media/active-directory-saas-clever-tutorial/IC798985.png "Instant Login")
   
   1. **ログイン URL**を入力します。  
   
      >[!NOTE]
      >**ログイン URL** はカスタム値です。 実際の値は、SClever サポート チームから取得できます。
      > 
   2. **[ID システム]** として、**[ADFS]** を選択します。
   3. **[Save]**をクリックします。
8. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-clever-tutorial/IC798986.png "Configure Single Sign-On")
9. 上部のメニューで、 **属性** to open the **SAML Token 属性** ダイアログを開きます。
   
   ![属性](./media/active-directory-saas-clever-tutorial/IC795920.png "Attributes")
10. 必要な属性のマッピングを追加するには、次の手順を実行します。
    
    ![saml token attributes](./media/active-directory-saas-clever-tutorial/IC795921.png "saml token attributes")
    
    | 属性名 | 属性値 |
    | --- | --- |
    | clever.student.credentials.district\_ユーザー名 |User.userprincipalname |
    
    1. 上記の表の各データ行で、 **[ユーザー属性の追加]**をクリックします。
    2. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    3. **[属性値]** ボックスで、その行に対して表示される属性値を選択します。
    4. **[完了]** をクリックします。
11. **[変更の適用]**をクリックします。

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
Azure AD ユーザーが Clever にログインできるようにするには、そのユーザーを Clever にプロビジョニングする必要があります。

Clever の場合、プロビジョニングは Clever サポート チームが実行する必要のある手動のタスクです。

>[!NOTE]
>Clever から提供されている他の Clever ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Clever に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Clever** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-clever-tutorial/IC798987.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-clever-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


