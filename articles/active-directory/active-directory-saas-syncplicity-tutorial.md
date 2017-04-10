---
title: "チュートリアル: Azure Active Directory と Syncplicity の統合 | Microsoft Docs"
description: "Azure Active Directory で Syncplicity を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 044769ca5a74b90843cb29510b66d9ce7e90566c
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>チュートリアル: Azure Active Directory と Syncplicity の統合
このチュートリアルでは、Azure Active Directory (Azure AD) と Syncplicity の間でのシングル サインオンを設定する方法を説明します。

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Syncplicity テナント

このチュートリアルを完了すると、Syncplicity に割り当てた Azure AD ユーザーは、Syncplicity 企業サイト (サービス プロバイダーが開始したサインオン) で、または Azure AD アクセス パネルを使用して、アプリケーションにシングル サインオンできるようになります。

1. Syncplicity のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Scenario")

## <a name="enable-the-application-integration-for-syncplicity"></a>Syncplicity のアプリケーション統合の有効化
このセクションでは、Syncplicity のアプリケーション統合を有効にする方法を説明します。

**Syncplicity のアプリ統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に「**Syncplicity**」と入力します。
   
    ![Syncplicity application gallery](./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Syncplicity application gallery")

7. 結果ウィンドウで **[Syncplicity]** を選び、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

## <a name="configure-single-sign-on"></a>Configure single sign-on
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、Syncplicity で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Syncplicity** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-syncplicity-tutorial/IC769534.png "Configure single sign-on")

2. **[ユーザーの Syncplicity へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Microsoft Azure AD Single Sign-On](./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Microsoft Azure AD Single Sign-On")

3. **[アプリケーション URL の構成]** ページの **[Syncplicity のサインイン URL]** テキスト ボックスに、ユーザーが Syncplicity アプリケーションにサインオンするときに使用する URL を入力し、**[次へ]** をクリックします。 
   
    アプリ の URL は、Syncplicity テナントの URL です (例: *http://company.Syncplicity.com*):
   
    ![アプリケーション URL の構成](./media/active-directory-saas-syncplicity-tutorial/IC769536.png "Configure app URL")

4. **[Syncplicity でのシングル サインオン構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-syncplicity-tutorial/IC769543.png "Configure single sign-on")

5. **Syncplicity** テナントにサインインします。

6. 上部のメニューで **[admin]** をクリックし、**[settings]** を選択し、**[Custom domain and single sign-on]** をクリックします。
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7. **[Single Sign-On (SSO)]** ダイアログ ページで、次の手順に従います。
   
    ![Single Sign-On \(SSO\)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Single Sign-On \\\(SSO\\\)")   
  1. **[Custom Domain]** テキスト ボックスに、ドメインの名前を入力します。
  2. **[Single Sign-On Status]** として **[Enabled]** を選択します。
  3. Azure クラシック ポータルの **[Syncplicity でのシングル サインオンの構成]** ダイアログ ページで **[エンティティ ID]** の値をコピーし、**[Entity Id]** テキスト ボックスに貼り付けます。
  4. Azure クラシック ポータルの **[Syncplicity でのシングル サインオンの構成]** ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[サインイン ページ URL]** ボックスに貼り付けます。
  5. Azure クラシック ポータルの **[Syncplicity でのシングル サインオンの構成]** ページで、**[リモート ログアウト URL]** 値をコピーして、**[Logout page URL]** テキスト ボックスに貼り付けます。
  6. **[ID プロバイダー証明書]** で **[ファイルの選択]** をクリックし、Azure クラシック ポータルからダウンロードした証明書をアップロードします。 
  7. **[変更を保存]**をクリックします。

8. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![Confirmation](./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Confirmation")

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
AAD ユーザーがサインインできるようにするには、ユーザーを Syncplicity アプリケーションにプロビジョニングする必要があります。 このセクションでは、Syncplicity で AAD ユーザー アカウントを作成する方法について説明します。

**ユーザー アカウントを Syncplicity にプロビジョニングするには、次の手順に従います。**

1. **Syncplicity** テナント (例: *https://company.Syncplicity.com*) にログインします。

2. **[Admin]** をクリックし、**[user accounts]** を選択します。

3. **[Add a user]**をクリックします。
   
    ![Manage Users](./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Manage Users")

4. プロビジョニングする AAD アカウントの**電子メール アドレス**を入力し、**[Role]** として **[User]** を選択し、**[次へ]** をクリックします。
   
    ![Account Information](./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Account Information")
   
    >[!NOTE]
    >AAD アカウントの所有者にアカウントの確認およびアクティブ化用のリンクを含む電子メールが送信されます。 
    > 

5. 新しいユーザーがそのメンバーになる社内のグループを選択し、 **[次へ]**をクリックします。
   
    ![Group Membership](./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Group Membership")
   
    >[!NOTE]
    >表示されるグループがない場合は、 **[次へ]**だけをクリックします。 
    > 

6. ユーザーのコンピューターで Syncplicity の制御下に置くフォルダーを選択し、 **[次へ]**をクリックします。
   
    ![Syncplicity Folders](./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Syncplicity Folders")

>[!NOTE]
>Syncplicity から提供されている他の Syncplicity ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Syncplicity に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Syncplicity** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


