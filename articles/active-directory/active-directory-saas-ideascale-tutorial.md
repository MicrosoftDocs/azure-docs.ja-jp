---
title: "チュートリアル: Azure Active Directory と IdeaScale の統合 | Microsoft Docs"
description: "Azure Active Directory で IdeaScale を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2695483b7dad4418bf24f21ebe808dd145fb9503
ms.openlocfilehash: 6362e5b6232a3a42fdbad3cf0e5a36a2e4b178b8
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>チュートリアル: Azure Active Directory と IdeaScale の統合
このチュートリアルでは、Azure と IdeaScale の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* IdeaScale でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、IdeaScale に割り当てた Azure AD ユーザーは、「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

*  IdeaScale のアプリケーション統合の有効化
*  シングル サインオンの構成
*  ユーザー プロビジョニングの構成
*  ユーザーの割り当て

![シナリオ](./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scenario")

## <a name="enable-the-application-integration-for-ideascale"></a>IdeaScale のアプリケーション統合の有効化
このセクションでは、IdeaScale のアプリケーション統合を有効にする方法を説明します。

**IdeaScale のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-ideascale-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に「**IdeaScale**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-ideascale-tutorial/IC790841.png "Application Gallery")
7. 結果ウィンドウで **[IdeaScale]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで IdeaScale に対する認証を行うことができるようにする方法を説明します。  

IdeaScale のシングル サインオンを構成するには、証明書からサムプリント値を取得する必要があります。 この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **[IdeaScale]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configure Single Sign-On")
2. **[ユーザーの IdeaScale へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページの **[IdeaScale のサインオン URL]** ボックスに、ユーザーが IdeaScale アプリケーションにサインオンするときに使用する URL (例: "*https://company.IdeaScale.com*") を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configure App URL")
4. **[IdeaScale でのシングル サインオンの構成]** ページで、メタデータをダウンロードするために、**[メタデータのダウンロード]** をクリックし、コンピューターでメタデータ ファイルをローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configure Single Sign-On")
5. 別の Web ブラウザーのウィンドウで、IdeaScale 企業サイトに管理者としてログインします。
6. **[コミュニティの設定]**に移動します。
   
   ![Community Settings](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
7. **[セキュリティ] \> [シングル サインオン設定]** の順にクリックします。
   
   ![Single Signon Settings](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Single Signon Settings")
8. **[シングル サインオンのタイプ]** で **[SAML 2.0]** を選択します。
   
   ![Single Signon Type](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Single Signon Type")
9. **[シングル サインオンの設定]** ダイアログで、次の手順を実行します。
   
   ![Single Signon Settings](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Single Signon Settings")
   
   1. Azure クラシック ポータルで、**[IdeaScale でのシングル サインオンの構成]** ダイアログ ページの **[エンティティ ID]** の値をコピーし、**[SAML IdP エンティティ ID]** ボックスに貼り付けます。
   2. ダウンロードしたメタデータ ファイルの内容をコピーし、 **[SAML IdP メタデータ]** ボックスに貼り付けます。
   3. Azure クラシック ポータルで、**[IdeaScale でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[ログアウト成功 URL]** ボックスに貼り付けます。
   4. **[変更を保存]**をクリックします。
10. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configure Single Sign-On")
    
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが IdeaScale にログインできるようにするには、そのユーザーを IdeaScale にプロビジョニングする必要があります。 IdeaScale の場合、プロビジョニングは手動で行います。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. **IdeaScale** 企業サイトに管理者としてログインします。
2. **[コミュニティの設定]**に移動します。
   
   ![Community Settings](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
3. **[基本設定] \> [メンバ管理]** の順にクリックします。
4. **[Add Member]**をクリックします。
   
   ![Member Management](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Member Management")
5. [新しいメンバーの追加] セクションで、次の手順を実行します。
   
   ![Add New Member](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Add New Member")
   
   1. **[電子メール アドレス]** ボックスに、プロビジョニングする有効な AAD アカウントの電子メール アドレスを入力します。
   2. **[変更を保存]**をクリックします。 
   
    >[!NOTE]
    >Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    >  

>[!NOTE]
>IdeaScale から提供されている他の IdeaScale ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを IdeaScale に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **IdeaScale** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-ideascale-tutorial/IC790854.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-ideascale-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


