---
title: "チュートリアル: Azure Active Directory と FreshService の統合 | Microsoft Docs"
description: "Azure Active Directory で FreshService を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 64421f1b7424751332b76508ae65d009a3e1f48b
ms.openlocfilehash: df90d455f2c3bdbc59c38447fe65bf1b64979249
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>チュートリアル: Azure Active Directory と FreshService の統合
このチュートリアルでは、Azure と FreshService の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* FreshService でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、FreshService に割り当てた Azure AD ユーザーは、「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* FreshService のアプリケーション統合の有効化
* シングル サインオンの構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scenario")

## <a name="enable-the-application-integration-for-freshservice"></a>FreshService のアプリケーション統合の有効化
このセクションでは、FreshService のアプリケーション統合を有効にする方法について説明します。

**FreshService のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**FreshService**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Application Gallery")
7. 結果ウィンドウで **[FreshService]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Freshservice](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで FreshService に対する認証を行うことができるようにする方法を説明します。 

FreshService にシングル サインオンを構成するには、証明書から拇印の値を取得する必要があります。 この手順に慣れていない場合は、「 [How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **FreshService** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configure Single Sign-On")
2. **[ユーザーの FreshService へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configure Single Sign-On")
3. **[アプリケーション URL の構成]** ページの **[FreshService のサインオン URL]** ボックスに、ユーザーが FreshService アプリケーションにサインオンする際に使用する URL (例: **http://democompany.freshservice.com/**) を入力し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configure App URL")
4. **[FreshService でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configure Single Sign-On")
5. 別の Web ブラウザー ウィンドウで、FreshService 企業サイトに管理者としてログインします。
6. 上部のメニューで **[Admin]**をクリックします。
   
   ![管理](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")
7. **[カスタマー ポータル]** で **[セキュリティ]** をクリックします。
   
   ![Security (セキュリティ)](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Security")
8. **[セキュリティ]** セクションで、次の手順を実行します。
   
   ![Single Sign On](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign On")
   
   1. **[シングル サインオン]** を切り替えます。
   2. **[SAML SSO]**を選択します。
   3. Azure クラシック ポータルの **[FreshService でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログイン URL]** の値をコピーし、**[SAML ログイン URL]** ボックスに貼り付けます。
   4. Azure クラシック ポータルの **[FreshService でのシングル サインオンの構成]** ダイアログ ページで、**[リモート ログアウト URL]** の値をコピーし、**[ログアウト URL]** ボックスに貼り付けます。
   5. エクスポートした証明書から **[拇印]** の値をコピーして、**[セキュリティ証明書フィンガープリント]** ボックスに貼り付けます。
   
      >[!TIP]
      >詳細については、「 [How to retrieve a certificate's thumbprint value (証明書のサムプリント値を取得する方法)](http://youtu.be/YKQF266SAxI)
      >
9. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configure Single Sign-On")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが FreshService にログインできるようにするには、そのユーザーを FreshService にプロビジョニングする必要があります。 FreshService の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **FreshService** 企業サイトに管理者としてログインします。
2. 上部のメニューで **[Admin]**をクリックします。
   
   ![管理](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")
3. **[ユーザー管理]** セクションで、**[要求者]** をクリックします。
   
   ![Requesters](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Requesters")
4. **[新しい要求者]**をクリックします。
   
   ![New Requesters](./media/active-directory-saas-freshservice-tutorial/IC790819.png "New Requesters")
5. **[新しい要求者]** セクションで、次の手順を実行します。
   
   ![New Requester](./media/active-directory-saas-freshservice-tutorial/IC790820.png "New Requester")   
   1. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの**名**および**メール**の属性を入力します。
   2. [ **Save**] をクリックします。
   
    >[!NOTE]
    >Azure Active Directory のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    >  

>[!NOTE]
>FreshService から提供されている他の FreshService ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
>  

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを FreshService に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **FreshService** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


