---
title: "チュートリアル: Azure Active Directory と Zendesk の統合 | Microsoft Docs"
description: "Azure Active Directory で Zendesk を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c4983024e5f5902620c0be7b422bc3d1eaf4a36f
ms.openlocfilehash: ac22518bb36cc9d4c7a581dab8aca22ffde986e5
ms.lasthandoff: 02/11/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>チュートリアル: Azure Active Directory と Zendesk の統合
このチュートリアルでは、Azure と Zendesk の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Zendesk テナント

このチュートリアルを完了すると、Zendesk に割り当てた Azure AD ユーザーは、Zendesk 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Zendesk のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scenario")

## <a name="enabling-the-application-integration-for-zendesk"></a>Zendesk のアプリケーション統合の有効化
このセクションでは、Zendesk のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-zendesk-perform-the-following-steps"></a>Zendesk のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure 管理ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**Zendesk**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Application Gallery")

7. 結果ウィンドウで **[Zendesk]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Zendesk に対する認証を行うことができるようにする方法を説明します。  
Zendesk にシングル サインオンを構成するには、証明書からサムプリント値を取得する必要があります。  
この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書のサムプリント値を取得する方法)](http://youtu.be/YKQF266SAxI)」を参照してください。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順に従います。
1. Azure AD ポータルの **[Zendesk]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオン](./media/active-directory-saas-zendesk-tutorial/IC773086.png "シングル サインオン")

2. **[ユーザーの Zendesk へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configure single sign-on")

3. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configure app URL")
   
    a. **[Zendesk サインイン URL]** ボックスに、`https://<tenant-name>.zendesk.com` という形式で URL を入力します。
   
    b. **[次へ]**をクリックします。

4. **[Zendesk でのシングル サインオンの構成]** ページで **[証明書のダウンロード]** をクリックし、証明書ファイルをローカル コンピューターに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configure single sign-on")

5. 別の Web ブラウザー ウィンドウで、Zendesk 企業サイトに管理者としてログインします。

6. **[Admin]**をクリックします。

7. 左側のナビゲーション ウィンドウで、**[設定]**、**[セキュリティ]** の順にクリックします。
   
    ![Security (セキュリティ)](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Security")

8. **[セキュリティ]** ページで、**[管理者とエージェント]** タブをクリックします。

9. **[シングルサインオン (SSO) と SAML]** を選択し、**[SAML]** を選択します。

10. Azure Portal で、**[Zendesk でのシングル サインオンの構成]** ダイアログ ページの **[SAML SSO URL]** の値をコピーし、**[SAML SSO URL]** テキストボックスに貼り付けます。

11. Azure Portal で、**[Zendesk でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[リモート ログアウト URL]** テキストボックスに貼り付けます。
    
    ![シングル サインオン](./media/active-directory-saas-zendesk-tutorial/IC773090.png "シングル サインオン")

12. エクスポートした証明書から **[拇印]** の値をコピーし、**[証明書の指紋]** ボックスに貼り付けます。
    
    > [!TIP]
    > 詳細については、「 [How to retrieve a certificate's thumbprint value (証明書のサムプリント値を取得する方法)](http://youtu.be/YKQF266SAxI)
    > 
    > 

13. **[保存]**をクリックします。

14. Azure AD ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Azure AD ユーザーが **Zendesk** にログインできるようにするには、ユーザーを **Zendesk** にプロビジョニングする必要があります。  
**Zendesk**の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-account-to-zendesk-perform-the-following-steps"></a>ユーザー アカウントを Zendesk にプロビジョニングするには、次の手順に従います。
1. **Zendesk** テナントにログインします。

2. **[顧客リスト]** タブを選択します。

3. **[ユーザー]** タブを選択し、**[追加]** をクリックします。
   
    ![ユーザーの追加](./media/active-directory-saas-zendesk-tutorial/IC773632.png "ユーザーの追加")
4. プロビジョニングする既存の Azure AD アカウントの電子メール アドレスを入力し、 **[追加]**をクリックします。
   
    ![New user](./media/active-directory-saas-zendesk-tutorial/IC773633.png "New user")

> [!NOTE]
> Zendesk から提供されている他の Zendesk ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-zendesk-perform-the-following-steps"></a>ユーザーを Zendesk に割り当てるには、次の手順を実行します。
1. Azure AD ポータルで、テスト アカウントを作成します。

2. **Zendesk** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


