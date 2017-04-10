---
title: "チュートリアル: Azure Active Directory と Kudos の統合 | Microsoft Docs"
description: "Azure Active Directory で Kudos を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ea39ca12135fded44a8e70ec87616f79007e0200
ms.openlocfilehash: 79915c69e2d40529ec5ad96a632e2e5b2ac4dd5e
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>チュートリアル: Azure Active Directory と Kudos の統合
このチュートリアルの目的は、Azure と Kudos の統合を紹介することです。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Kudos テナント

このチュートリアルを完了すると、Kudos に割り当てた Azure AD ユーザーは、Kudos 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Kudos のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-kudos-tutorial/IC787799.png "Scenario")

## <a name="enabling-the-application-integration-for-kudos"></a>Kudos のアプリケーション統合の有効化
このセクションでは、Kudos のアプリケーション統合を有効にする方法について説明します。

**Kudos のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-kudos-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-kudos-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-kudos-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Kudos**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-kudos-tutorial/IC787800.png "Application Gallery")
7. 結果ウィンドウで **[Kudos]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Kudos](./media/active-directory-saas-kudos-tutorial/IC787801.png "Kudos")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Kudos に対する認証を行えるようにする方法を説明します。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Kudos** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-kudos-tutorial/IC787802.png "Configure single sign-on")
2. **[ユーザーの Kudos へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-kudos-tutorial/IC787803.png "Configure single sign-on")
3. **[アプリ URL の構成]** ページの **[Kudos サインオン URL]** ボックスに、"**https://company.kudosnow.com**" というパターンの URL を入力して、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-kudos-tutorial/IC787804.png "Configure App URL")
4. **[Kudos でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-kudos-tutorial/IC787805.png "Configure single sign-on")
5. 別の Web ブラウザーのウィンドウで、Kudos の企業サイトに管理者としてログインします。
6. 上部のメニューで **[Settings]**をクリックします。
   
   ![設定](./media/active-directory-saas-kudos-tutorial/IC787806.png "Settings")
7. **[Integrations] \> [SSO]** をクリックします。
8. **[SSO]** セクションで、次の手順に従います。
   
   ![SSO](./media/active-directory-saas-kudos-tutorial/IC787807.png "SSO")
   
   1. Azure クラシック ポータルの **[Kudos でのシングル サインオンの構成]** ダイアログ ページで、**シングル サインオン サービス URL** の値をコピーし、**[シングル サインオン サービス URL]** テキストボックスに貼り付けます。
   2. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。 
   
      >[!TIP]
      >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o) 
      > 
   3. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[X.509 証明書]** テキストボックスに貼り付けます。
   4. Azure クラシック ポータルの **[Kudos でのシングル サインオンの構成]** ダイアログ ページで、**シングル サインアウト サービス URL** の値をコピーし、**[ログアウト要求 URL]** テキストボックスに貼り付けます。
   5. **[Your Kudos URL (Kudos の URL)]** テキストボックスに、企業名を入力します。
   6. **[Save]**をクリックします。
9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-kudos-tutorial/IC787808.png "Configure single sign-on")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Kudos にログインできるようにするには、そのユーザーを Kudos にプロビジョニングする必要があります。 

* Kudos の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Kudos** の企業サイトに管理者としてログインします。
2. 上部のメニューで **[Settings]**をクリックします。
   
   ![設定](./media/active-directory-saas-kudos-tutorial/IC787806.png "Settings")
3. **[ユーザー管理者]**をクリックします。
4. **[ユーザー]** タブをクリックして、**[ユーザーの追加]** をクリックします。
   
   ![User Admin](./media/active-directory-saas-kudos-tutorial/IC787809.png "User Admin")
5. **[ユーザーの追加]** セクションで、次の手順に従います。
   
   ![Add a User](./media/active-directory-saas-kudos-tutorial/IC787810.png "Add a User")
   
  1. プロビジョニングする有効な Azure Active Directory アカウントの**[名]**、**[姓]**、**[メール]**、その他の詳細を該当するボックスに入力します。
   2. **[Create User]**をクリックします。

>[!NOTE]
>Kudos から提供されている他の Canvas ユーザー アカウント作成ツールや API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Kudos に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Kudos** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-kudos-tutorial/IC787811.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-kudos-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


