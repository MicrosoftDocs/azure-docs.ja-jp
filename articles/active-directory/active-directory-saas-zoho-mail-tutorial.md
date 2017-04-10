---
title: "チュートリアル: Azure Active Directory と Zoho Mail の統合 | Microsoft Docs"
description: "Azure Active Directory で Zoho Mail を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: fd79688a66c2b3919b11c0b06d268b60e1d93a8f
ms.openlocfilehash: 60165d0cd7fea3cd0861f36d9a4a245cedabe07a
ms.lasthandoff: 02/24/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zoho-mail"></a>チュートリアル: Azure Active Directory と Zoho Mail の統合
このチュートリアルでは、Azure と Zoho Mail の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Zoho Mail テナント

このチュートリアルを完了すると、Zoho Mail に割り当てた Azure AD ユーザーは、Zoho Mail 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Zoho Mail のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Scenario")

## <a name="enabling-the-application-integration-for-zoho-mail"></a>Zoho Mail のアプリケーション統合の有効化
このセクションでは、Zoho Mail のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-zoho-mail-perform-the-following-steps"></a>Zoho Mail のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**Zoho Mail**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Application Gallery")

7. 結果ウィンドウで **[Zoho Mail]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Zoho Mail に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  
この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **[Zoho Mail]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configure Single Sign-On")

2. **[ユーザーの Zoho Mail へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configure Single Sign-On")

3. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configure App URL")
   
    a. **[Zoho Mail サインオン URL]** ボックスに、`http://<company name>.ZohoMail.com` という形式で URL を入力します。
   
    b. **[次へ]**をクリックします。

4. **[Zoho Mail でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configure Single Sign-On")

5. 別の Web ブラウザー ウィンドウで、Zoho Mail 企業サイトに管理者としてログインします。

6. **コントロール パネル**に移動します。
   
    ![コントロール パネル](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Control Panel")

7. **[SAML 認証]** タブをクリックします。
   
    ![SAML Authentication](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "SAML Authentication")

8. **[SAML 認証の詳細]** セクションで、次の手順に従います。
   
    ![SAML 認証の詳細](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "SAML Authentication Details")
   
    a. Azure クラシック ポータルの **[Zoho Mail でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログイン URL]** の値をコピーして、**[ログイン URL]** ボックスに貼り付けます。
   
    b. Azure クラシック ポータルの **[Zoho Mail でのシングル サインオンの構成]** ダイアログ ページで **[リモート ログアウト URL]** の値をコピーして、**[ログアウト URL]** ボックスに貼り付けます。
   
    c. Azure クラシック ポータルの **[Zoho Mail でのシングル サインオンの構成]** ダイアログ ページで **[パスワード変更 URL]** の値をコピーして、**[パスワード変更 URL]** ボックスに貼り付けます。
   
    d. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。  
      
    > [!TIP]
    > 詳細については、 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    e. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[公開キー]** ボックスに貼り付けます。
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[アルゴリズム]** として **[RSA]** を選択します。
   
    g. **[OK]**をクリックします。

9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Azure AD ユーザーが Zoho Mail にログインできるようにするには、そのユーザーを Zoho Mail にプロビジョニングする必要があります。  
Zoho Mail の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。
1. **Zoho Mail** 企業サイトに管理者としてログインします。

2. **[コントロール パネル] \> [メールとドキュメント]** に移動します。

3. **[ユーザーの詳細] \> [ユーザーの追加]** の順に移動します。
   
    ![ユーザーの追加](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Add User")

4. **[ユーザーの追加]** ダイアログで、次の手順を実行します。
   
    ![ユーザーの追加](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Add User")
   
    a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**名**、**姓**、**電子メール ID**、**パスワード**を入力します。
   
    b. **[OK]**をクリックします。  
      
    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    > 
    > 

> [!NOTE]
> Zoho Mail から提供されている他の Zoho Mail ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-zoho-mail-perform-the-following-steps"></a>ユーザーを Zoho Mail に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Zoho Mail** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


