---
title: "チュートリアル: Azure Active Directory と Workday の統合 | Microsoft Docs"
description: "Azure Active Directory で Workday を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: e6b1c8cbf45fea4f5af16231003ccc07a2a53233
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>チュートリアル: Azure Active Directory と Workday の統合
このチュートリアルでは、Azure と Workday の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Workday のテナント

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Workday のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザー プロビジョニングの構成

![シナリオ](./media/active-directory-saas-workday-tutorial/IC782919.png "Scenario")

## <a name="enabling-the-application-integration-for-workday"></a>Workday のアプリケーション統合の有効化
このセクションでは、Workday のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Workday のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-workday-tutorial/IC700994.png "Applications")

4. **アプリケーション ギャラリー**を開くには、**[アプリケーションの追加]**、**[組織で使用するアプリケーションを追加]** の順にクリックします。
   
    ![実行する作業を選択してください。](./media/active-directory-saas-workday-tutorial/IC700995.png "実行する作業を選択してください。")

5. **検索ボックス**に、「**Workday**」と入力します。
   
    ![Workday](./media/active-directory-saas-workday-tutorial/IC701021.png "Workday")

6. 結果ウィンドウで **[Workday]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Workday](./media/active-directory-saas-workday-tutorial/IC701022.png "Workday")

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Workday に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、Base-64 でエンコードされた証明書を作成する必要があります。  
この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. **[Workday]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-workday-tutorial/IC782920.png "Configure single sign-on")

2. **[ユーザーの Workday へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-workday-tutorial/IC782921.png "Configure single sign-on")

3. **[アプリケーション URL の構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-workday-tutorial/IC782957.png "Configure App URL")
   
    a. **[サインオン URL]** ボックスに、ユーザーが Workday へのサインオンに使用する URL を `https://impl.workday.com/<tenant>/login-saml2.htmld` という形式で入力します。
   
    b.  **[Workday 応答 URL]** ボックスに、`https://impl.workday.com/<tenant>/login-saml.htmld` という形式で Workday 応答 URL を入力します。
   
    > [!NOTE]
    > 応答 URL には必ずサブドメインを入れます (例: www、wd2、wd3、wd3-impl、wd5、wd5-impl)。 " *http://www.myworkday.com* " のようなものは動作しますが、" *http://myworkday.com* " は動作しません。 
    > 
    > 

4. **[Workday でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。
 
    ![シングル サインオンの構成](./media/active-directory-saas-workday-tutorial/IC782922.png "Configure single sign-on")

5. 別の Web ブラウザー ウィンドウで、Workday 企業サイトに管理者としてログインします。

6. **[メニュー]\>[ワークベンチ]** に移動します。
   
    ![ワークベンチ](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

7. **[アカウント管理]**に移動します。
   
    ![Account Administration](./media/active-directory-saas-workday-tutorial/IC782924.png "Account Administration")

8. **[テナントのセットアップの編集 – セキュリティ]**に移動します。
   
    ![テナントのセキュリティの編集](./media/active-directory-saas-workday-tutorial/IC782925.png "Edit Tenant Security")

9. **[リダイレクト URL]** セクションで、次の手順を実行します。
   
    ![リダイレクト URL](./media/active-directory-saas-workday-tutorial/IC7829581.png "Redirection URLs")
   
    a. **[行の追加]** をクリックします。
   
    b. **[ログイン リダイレクトの URL]** ボックスと **[モバイル リダイレクトの URL]** ボックスに、Azure クラシック ポータルの **[アプリケーション URL の構成]** で **[Workday テナント URL]** に入力した URL を入力します。
   
    c. Azure クラシック ポータルで、**[Workday でのシングル サインオンの構成]** ダイアログ ページの **[シングル サインアウト サービス URL]** をコピーし、**[ログアウト リダイレクト URL]** ボックスに貼り付けます。
   
    d.  **[環境]** テキスト ボックスに、環境の名前を入力します。  

    >[!NOTE]
    > [環境] 属性の値が、テナント URL の値に関連付けられます。  
    >-   Workday テナント URL のドメイン名が impl で始まる場合 (例: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*)、**[環境]** 属性を "実装" に設定する必要があります。  
    >-   ドメイン名が impl 以外で始まる場合は、Workday に問い合わせて、対応する **[環境]** の値を取得してください。

1. **[SAML 設定]** セクションで、次の手順を実行します。
   
    ![SAML のセットアップ](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML Setup")
   
    a.  **[Enable SAML Authentication]**を選択します。
   
    b.  **[行の追加]** をクリックします。

2. [SAML ID プロバイダー] セクションで、次の手順に従います。
   
    ![SAML ID プロバイダー](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML Identity Providers")
   
    a. [ID プロバイダー名] テキスト ボックスに、プロバイダー名を入力します (例: *SPInitiatedSSO*)。
   
    b. Azure クラシック ポータルで、**[Workday でのシングル サインオンの構成]** ダイアログ ページの **[ID プロバイダーの ID]** の値をコピーし、**[発行者]** テキスト ボックスに貼り付けます。
   
    c. **[Workday 始動ログアウトを有効にする]** を選択します。
   
    d. Azure クラシック ポータルで、**[Workday でのシングル サインオンの構成]** ダイアログ ページの **[シングル サインアウト サービス URL]** 値をコピーし、**[ログアウト要求 URL]** ボックスに貼り付けます。

    e. **[ID プロバイダーの公開鍵証明書]** をクリックし、**[作成]** をクリックします。 

    ![作成](./media/active-directory-saas-workday-tutorial/IC782928.png "作成")

    f. **[x509 公開鍵の作成]** をクリックします。 

    ![作成](./media/active-directory-saas-workday-tutorial/IC782929.png "作成")


1. **[x509 公開鍵の表示]** セクションで、次の手順を実行します。 
   
    ![x509 公開鍵の表示](./media/active-directory-saas-workday-tutorial/IC782930.png "x509 公開鍵の表示") 
   
    a. **[名前]** テキスト ボックスに、証明書の名前を入力します (例: *PPE\_SP*)。
   
    b. **[有効期間の開始日]** テキスト ボックスに、証明書の有効期間の開始日を示す属性の値を入力します。
   
    c.  **[有効期間の終了日]** テキスト ボックスに、証明書の有効期間の終了日を示す属性の値を入力します。
   
    > [!NOTE]
    > 有効期間の開始日と終了日は、ダウンロードした証明書をダブルクリックして確認できます。  日付は **[詳細]** タブに表示されます。
    > 
    > 
   
    d. ダウンロードした証明書から **Base-64 でエンコードされた** ファイルを作成します。  
   
    > [!TIP]
    > 詳細については、 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    e.  Base&64; でエンコードされた証明書をメモ帳で開き、その内容をコピーします。
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。  **[証明書]** テキスト ボックスに、クリップボードの内容を貼り付けます。
   
    g.  **[OK]**をクリックします。

2. 次の手順に従います。 
   
    ![SSO 構成](./media/active-directory-saas-workday-tutorial/IC7829351111.png "SSO configuration")
   
    a.  **[x509 秘密鍵のペア]** を有効にします。
   
    b.  **[サービス プロバイダー ID]** テキスト ボックスに「**http://www.workday.com**」と入力します。
   
    c.  **[SP によって開始された SAML 認証を有効にする]** を選択します。
   
    d.  Azure クラシック ポータルの **[Workday でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[IdP SSO サービス URL]** テキスト ボックスに貼り付けます。
   
    e. **[SP によって開始された認証要求を圧縮しない]** を選択します。
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[認証要求署名方法]** として **[SHA256]** を選択します。 
   
    ![認証要求署名方法](./media/active-directory-saas-workday-tutorial/IC782932.png "Authentication Request Signature Method") 
   
    g. **[OK]**をクリックします。 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

3. Azure クラシック ポータルの **[Workday でのシングル サインオンの構成]** ページで、**[次へ]** をクリックします。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-workday-tutorial/IC782934.png "Configure single sign-on")

4. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。 
   
    ![シングル サインオンの構成](./media/active-directory-saas-workday-tutorial/IC782935111.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Workday にテスト ユーザーをプロビジョニングするには、Workday のサポート チームに連絡する必要があります。  
Workday のサポート チームにユーザーを作成してもらいます。

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-workday-perform-the-following-steps"></a>ユーザーを Workday に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Workday** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-workday-tutorial/IC782935.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-workday-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


