---
title: "チュートリアル: Azure Active Directory と Benefitsolver の統合 | Microsoft Docs"
description: "Azure Active Directory で Benefitsolver を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: cf4529b1-3fb6-4475-82b7-2ceedcb70b3c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 8a13dd5ebd872f86247158379b28bc291a9c9d83
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>チュートリアル: Azure Active Directory と Benefitsolver の統合
このチュートリアルでは、Azure と Benefitsolver の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Benefitsolver でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを終了すると、Benefitsolver に割り当てた Azure AD ユーザーは、「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Benefitsolver のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Scenario")

## <a name="enabling-the-application-integration-for-benefitsolver"></a>Benefitsolver のアプリケーション統合の有効化
このセクションでは、Benefitsolver のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>Benefitsolver のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Benefitsolver**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Application Gallery")
7. 結果ウィンドウで **[Benefitsolver]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Benefitssolver](./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Benefitsolver に対する認証を行えるようにする方法を説明します。  

Benefitsolver アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを **saml トークン属性**構成に追加する必要があります。 

次のスクリーンショットはその例です。

![属性](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributes")

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Benefitsolver** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configure Single Sign-On")
2. **[ユーザーの Benefitsolver へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configure Single Sign-On")
3. **[アプリケーション設定の構成]** ページで、次の手順を実行します。
   
   ![アプリケーションの設定の構成](./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configure App Settings")
   
   1. **[サインオン URL]** ボックスに、「**http://azure.benefitsolver.com**」と入力します。
   2. **[応答 URL]** ボックスに、「**https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**」と入力します。  
   3. **[次へ]**をクリックします。
4. **[Benefitsolver でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、メタデータ ファイルをコンピューターのローカルに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configure Single Sign-On")
5. ダウンロードしたメタデータ ファイルを Benefitsolver サポート チームに送信します。
   
   >[!NOTE]
   >Benefitsolver サポート チームが、実際に SSO を構成する必要があります。 ご使用のサブスクリプションで SSO が有効になると通知が届きます。
   >

6. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configure Single Sign-On")
7. 上部のメニューで、 **属性** to open the **SAML Token 属性** ダイアログを開きます。
   
   ![属性](./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Attributes")
8. 必要な属性のマッピングを追加するには、次の手順を実行します。
   
   ![属性](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributes")
   
   | 属性名 | 属性値 |
   | --- | --- |
   | ClientID |この値は、Benefitsolver サポート チームから入手する必要があります。 |
   | ClientKey |この値は、Benefitsolver サポート チームから入手する必要があります。 |
   | LogoutURL |この値は、Benefitsolver サポート チームから入手する必要があります。 |
   | EmployeeID |この値は、Benefitsolver サポート チームから入手する必要があります。 |
   
   1. 上記の表の各データ行で、 **[ユーザー属性の追加]**をクリックします。
   2. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
   3. **[属性値]** ボックスで、その行に対して表示される属性値を選択します。
   4. **[完了]** をクリックします。
9. **[変更の適用]**をクリックします。

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
Azure AD ユーザーが Benefitsolver にログインできるようにするには、ユーザーを Benefitsolver にプロビジョニングする必要があります。  

Benefitsolver の場合、従業員のデータは、人事情報システムの国勢調査ファイルを介して (通常夜間に) アプリケーションに入力されます。  

>[!NOTE]
>Benefitsolver から提供されている他の Benefitsolver ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>ユーザーを Benefitsolver に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Benefitsolver** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Assign Users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


