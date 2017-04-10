---
title: "チュートリアル: Azure Active Directory と Salesforce Sandbox の統合 | Microsoft Docs"
description: "Azure Active Directory で Salesforce Sandbox を使用してシングル サインオンや自動化されたプロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 5d37dbcce06ac979f3cabef1220e5f1fe8e67931
ms.openlocfilehash: 6c0cd8aeaaa094a6bf046d47a96eddffc6430d44
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>チュートリアル: Azure Active Directory と Salesforce Sandbox の統合

このチュートリアルでは、Azure と Salesforce Sandbox の統合について説明します。  

>[!TIP]
>フィードバックについては、[Azure サポート ページ](http://go.microsoft.com/fwlink/?LinkId=521878)を参照してください。 
> 

Sandbox を使用すると、Salesforce 運用組織内のデータとアプリケーションを侵害することなく、多様な用途 (開発、テスト、トレーニングなど) ごとに別環境に組織の複数コピーを作成できます。  

詳細については、「 [サンドボックスの概要](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Salesforce.com のサンドボックス

まだ Salesforce.com 内に有効なサンドボックスがない場合、Salesforce に連絡する必要があります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Salesforce Sandbox のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ドメインの有効化
4. ユーザー プロビジョニングの構成
5. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scenario")

## <a name="enable-the-application-integration-for-salesforce-sandbox"></a>Salesforce Sandbox のアプリケーション統合の有効化
このセクションでは、Salesforce Sandbox のアプリケーション統合を有効にする方法について説明します。

**Salesforce Sandbox のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Applications")
4. **アプリケーション ギャラリー**を開くには、**[アプリケーションの追加]**、**[組織で使用するアプリケーションを追加]** の順にクリックします。
   
   ![実行する作業を選択してください。](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "実行する作業を選択してください。")
5. **検索ボックス**に「**Salesforce Sandbox**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Application Gallery")
6. 結果ウィンドウで **[Salesforce Sandbox]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
   
## <a name="configur-single-sign-on-sso"></a>シングル サインオン (SSO) の構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Salesforce に対する認証を行えるようにする方法について説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Salesforce Sandbox** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Configure single sign-on")
2. **[ユーザーの Salesforce Sandbox へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")
3. **[アプリケーション URL の構成]** ページで、**[サインオン URL]** ボックスに、`http://company.my.salesforce.com` パターンの URL を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configure App URL")
4. ディレクトリで別の Salesforce Sandbox インスタンスのシングル サイオンを既に構成している場合は、**[サインオン URL]** と同じ値を使用するように **[識別子]** も構成する必要があります。 
 * **[識別子]** フィールドは、ダイアログの **[アプリケーション URL の構成]** ページで **[詳細設定の表示]** チェック ボックスをオンにすることで表示できます。
5. **[Salesforce Sandbox でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configure Single Sign-On")
6. 別の Web ブラウザーのウィンドウで、Salesforce Sandbox 企業サイトに管理者としてログインします。
7. 上部のメニューで **[セットアップ]**をクリックします。
   
   ![Setup](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Setup")
8. 左側のナビゲーション ウィンドウで、**[セキュリティ コントロール]** をクリックし、**[シングル サインオンの設定]** をクリックします。
   
   ![Single Sign-On Settings](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Single Sign-On Settings")
9. [シングル サインオンの設定] セクションで、次の手順に従います。
   
   ![Single Sign-On Settings](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Single Sign-On Settings")  
 1.  **[SAML Enabled]**を選択します。 
 2.  **[新規]**をクリックします。
10. [SAML シングル サインオンの設定] セクションで、次の手順に従います。
    
    ![SAML シングル サインオンの設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML Single Sign On Settings")  
 1. [名前] ボックスに、構成の名前を入力します (例: *SPSSOWAAD\_Test*)。 
 2. Azure クラシック ポータルの **[Salesforce Sandbox でのシングル サインオンの構成]** ダイアログ ページで、**[発行者の URL]** の値をコピーし、**[発行者]** ボックスに貼り付けます。
 3. これがディレクトリに追加する最初の Salesforce Sandbox インスタンスの場合は、**[Entity Id (エンティティ ID)]** ボックスに「**https://test.salesforce.com**」と入力します。 Salesforce Sandbox のインスタンスを既に追加している場合は、**[Entity Id (エンティティ ID)]** に次の形式の**サインオン URL** を入力します。`http://company.my.salesforce.com`   
 4. **[参照]** をクリックして、ダウンロードした証明書をアップロードします。  
 5. **[SAML ID の種類]** として、**[アサーションにはユーザー オブジェクトからのフェデレーション ID が含まれます]** を選択します。 
 6. **[SAML Identity Location (SAML ID の場所)]** で、**[Identity is in the NameIdentifier element of the Subject statement (ID は Subject ステートメントの NameIdentifier 要素にあります)]** を選択します。
 7. Azure クラシック ポータルの **[Salesforce Sandbox でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログイン URL]** の値をコピーし、**[ID プロバイダーのログイン URL]** ボックスに貼り付けます。 
 8. SFDC は SAML ログアウトをサポートしていません。  回避策として、**[Identity Provider Logout URL (ID プロバイダー ログアウト URL)]** ボックスに「https://login.windows.net/common/wsfederation?wa=wsignout1.0」を貼り付けます。
 9. **[サービス プロバイダーが開始した要求のバインド]** で **[HTTP POST]** を選択します。 
 10. **[保存]**をクリックします。
11. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configure Single Sign-On")

## <a name="enable-your-domain"></a>ドメインの有効化
このセクションでは、ドメインが既に作成されていることを前提としています。  詳細については、「 [ドメイン名の定義](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)」をご覧ください。

**ドメインを有効にするには、次の手順に従います。**

1. 左側のナビゲーション ウィンドウで、**[ドメイン管理]** をクリックし、**[マイ ドメイン]** をクリックします。
   
   ![My Domain](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "My Domain")
   
   >[!NOTE]
   >ドメインが正しく構成されていることを確認します。 
   > 
2. **[Login Page Settings (ログイン ページ設定)]** セクションで、**[編集]** をクリックし、**[認証サービス]** で前のセクションの [SAML シングル サインオンの設定] の名前を選択して、最後に **[保存]** をクリックします。
   
   ![My Domain](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "My Domain")

ドメインを構成するとすぐに、ユーザーはドメインの URL を使用して Salesforce Sandbox にログインできるようになります。  

URL の値を取得するには、前のセクションで作成した SSO プロファイルをクリックします。

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
このセクションでは、Active Directory のユーザー アカウントのユーザー プロビジョニングを Salesforce Sandbox に対して有効にする方法について説明します。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. Salesforce のポータルの上部にあるナビゲーション バーで、自分の名前を選択し、ユーザー メニューを展開します。
   
   ![個人の設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "My Settings")
2. ユーザー メニューの **[個人の設定]** をクリックして、**[個人の設定]** ページを開きます。
3. 左側のウィンドウで、**[個人用]** をクリックして個人用セクションを展開し、**[個人のセキュリティ トークンのリセット]** をクリックします。
   
   ![個人の設定](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "My Settings")
4. **[個人のセキュリティ トークンのリセット]** ページで、**[セキュリティ トークンのリセット]** をクリックして、Salesforce.com のセキュリティ トークンが記載された電子メールを要求します。
   
   ![新しいトークン](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "New Token")
5. メールの受信トレイに Salesforce.com から "**salesforce.com.com security confirmation**" という件名のメールが届いていることを確認します。
6. そのメールを確認し、セキュリティ トークンの値をコピーします。
7. Azure クラシック ポータルの **Salesforce Sandbox** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックして **[ユーザー プロビジョニングの構成]** ダイアログを開きます。
   
   ![Configure user provisioning](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configure user provisioning")
8. **[自動ユーザー プロビジョニングを有効にするための Salesforce Sandbox の資格情報を入力してください]** ページで、以下の構成設定を入力します。
   
   ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")   
 1. **[Salesforce Sandbox 管理ユーザー名]** ボックスに、Salesforce.com で**システム管理者**プロファイルが割り当てられている Salesforce Sandbox アカウント名を入力します。
 2. **[Salesforce Sandbox 管理者パスワード]** ボックスに、このアカウントのパスワードを入力します。
 3. **[ユーザーのセキュリティ トークン]** ボックスに、セキュリティ トークンの値を貼り付けます。
 4. **[検証]** をクリックして構成を確認します。
 5. **[次へ]** をクリックして、**[確認]** ページを開きます。
9. **[確認]** ページで **[完了]** をクリックして構成を保存します。
   
## <a name="assigning-users"></a>ユーザーの割り当て

構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Salesforce Sandbox に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Salesforce Sandbox** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Yes")

10 分待ってから、アカウントが Salesforce Sandbox と同期できたことをご確認ください。

SSO の設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)を参照してください。


