---
title: "チュートリアル: Azure Active Directory と Work.com の統合 | Microsoft Docs"
description: "Azure Active Directory で Work.com を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 86742dc7ee1429a0cdb23f4da3fa58341468afbc
ms.openlocfilehash: 8d75ee95bcaf506c2378bb9cf34da576fb1c83e1
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>チュートリアル: Azure Active Directory と Work.com の統合
このチュートリアルでは、Azure と Work.com の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Work.com でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Work.com に割り当てた Azure AD ユーザーは、Work.com 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Work.com のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-work-com-tutorial/IC794105.png "Scenario")

## <a name="enabling-the-application-integration-for-workcom"></a>Work.com のアプリケーション統合の有効化
このセクションでは、Work.com のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>Work.com のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-work-com-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-work-com-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-work-com-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**Work.com**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-work-com-tutorial/IC794106.png "Application Gallery")

7. 結果ウィンドウで **[Work.com]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Work.com に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、証明書を Work.com.com にアップロードする必要があります。

> [!NOTE]
> シングル サインオンを構成する場合でも、カスタムの Work.com ドメイン名をセットアップする必要があります。 少なくともドメイン名を定義し、ドメイン名をテストしてから、組織全体にデプロイする必要があります。
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順に従います。
1. 管理者として Work.com テナントにログインします。
2. **[セットアップ]**に移動します。
   
    ![Setup](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")

3. 左側のナビゲーション ウィンドウの **[管理]** セクションで、**[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックして **[マイ ドメイン]** ページを開きます。 
   
    ![My Domain](./media/active-directory-saas-work-com-tutorial/IC767825.png "My Domain")

4. ドメインが正しく設定されていることを確認するには、“**ステップ 4 ユーザーへのデプロイ**” で “**マイ ドメインの設定**” を確認します。
   
    ![Doman Deployed to User](./media/active-directory-saas-work-com-tutorial/IC784377.png "Doman Deployed to User")

5. 別の Web ブラウザー ウィンドウで、Azure クラシック ポータルにログインします。

6. **Work.com** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configure Single Sign-On")

7. **[ユーザーの Work.com へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configure Single Sign-On")

8. **[アプリケーション URL の構成]** ページの **[Work.com サインオン URL]** テキストボックスに、ユーザーが Work.com アプリケーションにサインオンするときに使用する URL (例: ”*http://company.my.salesforce.com*”) を入力し、**[次へ]** をクリックします。 
   
    ![アプリケーション URL の構成](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configure App URL")

9. **[Work.com でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、コンピューターで証明書ファイルをローカルに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configure Single Sign-On")

10. Work.com テナントにログインします。

11. **[セットアップ]**に移動します。
    
    ![Setup](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")

12. **[セキュリティ制御]** メニューを展開表示し、**[シングル サインオンの設定]** をクリックします。
    
    ![Single Sign-On Settings](./media/active-directory-saas-work-com-tutorial/IC794113.png "Single Sign-On Settings")

13. **[シングル サインオンの設定]** ダイアログ ページで、次の手順を実行します。
    
    ![SAML 有効](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML Enabled")
    
    a. **[SAML Enabled]**を選択します。
    
    b. **[新規]**をクリックします。

14. **[SAML シングル サインオンの設定]** セクションで、次の手順に従います。
    
    ![SAML Single Sign-On Setting](./media/active-directory-saas-work-com-tutorial/IC794114.png "SAML Single Sign-On Setting")
    
    a. **[名前]** テキスト ボックスに、構成の名前を入力します。  
       
    > [!NOTE]
    > **[名前]** の値を指定すると、**[API 名]** ボックスが自動的に入力されます。
    > 
    > 
    
    b. Azure クラシック ポータルの **[Work.com でのシングル サインオンの構成]** ダイアログ ページで、**[発行者の URL]** の値をコピーし、**[発行者]** ボックスに貼り付けます。
    
    c. **[参照]**をクリックして、ダウンロードした証明書をアップロードします。
    
    d. **[エンティティ ID]** ボックスに、「**https://salesforce-work.com**」と入力します。
    
    e. **[SAML ID の種類]** として、**[アサーションにはユーザー オブジェクトからのフェデレーション ID が含まれます]** を選択します。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[SAML ID の場所]** として、**[ID を Subject ステートメントの NameIdentifier 要素にする]** チェックボックスをオンにします。
    
    g. Azure クラシック ポータルで、**[Work.com でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[ID プロバイダー ログイン URL]** ボックスに貼り付けます。
    
    h. Azure クラシック ポータルで、**[Work.com でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[ID プロバイダー ログアウト URL]** ボックスに貼り付けます。
    
    i. **[サービス プロバイダーが開始した要求のバインド]** で **[HTTP POST]** を選択します。
    
    j. [ **Save**] をクリックします。

15. Work.com クラシック ポータルで、左側のナビゲーション ウィンドウの **[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックして **[マイ ドメイン]** ページを開きます。 
    
    ![My Domain](./media/active-directory-saas-work-com-tutorial/IC794115.png "My Domain")

16. **[マイ ドメイン]** ページの **[Login Page Branding (ログイン ページのブランド)]** をクリックして、**[編集]** をクリックします。
    
    ![Login Page Branding](./media/active-directory-saas-work-com-tutorial/IC767826.png "Login Page Branding")

17. **[Login Page Branding (ログイン ページのブランド)]** ページの **[認証サービス]** セクションに、**[SAML SSO 設定]** の名前が表示されます。 それを選んで、 **[保存]**をクリックします。
    
    ![Login Page Branding](./media/active-directory-saas-work-com-tutorial/IC784366.png "Login Page Branding")

18. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Azure Active Directory ユーザーがサインインできるように、Azure Active Directory ユーザーを Work.com に対してプロビジョニングする必要があります。  
Work.com の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. Work.com 企業サイトに管理者としてサインオンします。

2. **[セットアップ]**に移動します。
   
    ![Setup](./media/active-directory-saas-work-com-tutorial/IC794108.png "Setup")
3. **[ユーザーの管理]\>[ユーザー]** に移動します。
   
    ![Manage Users](./media/active-directory-saas-work-com-tutorial/IC784369.png "Manage Users")

4. **[新しいユーザー]**をクリックします。
   
    ![All Users](./media/active-directory-saas-work-com-tutorial/IC794117.png "All Users")

5. [ユーザーの編集] セクションで、次の手順を実行します。
   
    ![User Edit](./media/active-directory-saas-work-com-tutorial/IC794118.png "User Edit")
   
    a. 関連するテキストボックスにプロビジョニングする有効な Azure Active Directory アカウントの **[姓]**、**[エイリアス]**、**[電子メール]**、**[ユーザー名]**、**[ニックネーム]** の属性を入力します。
   
    b. **[ロール]**、**[ユーザー ライセンス]**、**[プロファイル]** を選択します。
   
    c. [ **Save**] をクリックします。  
      
    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    > 
    > 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-workcom-perform-the-following-steps"></a>ユーザーを Work.com に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. Work.com アプリケーション統合ページで、 **[ユーザーの割り当て]**をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-work-com-tutorial/IC794119.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-work-com-tutorial/IC767830.png "Yes")

ここで 10 分間待機し、アカウントが Work.com に同期されたことを確認します。

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


