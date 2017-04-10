---
title: "チュートリアル: Azure Active Directory と Box の統合 | Microsoft Docs"
description: "Azure Active Directory で Box を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 5f3517f8-30f2-4be7-9e47-43d702701797
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 6e6dad2dba03bd3339d841c885ab4e60b2dafbd3
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-box"></a>チュートリアル: Azure Active Directory と Box の統合
このチュートリアルでは、Azure と Box の統合について説明します。  

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Box のテスト テナント

このチュートリアルを完了すると、Box に割り当てた Azure AD ユーザーは、Box 企業サイト (サービス プロバイダーが開始したサインオン) または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Box のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザーとグループのプロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-box-tutorial/IC769537.png "Scenario")

## <a name="enable-the-application-integration-for-box"></a>Box のアプリケーション統合の有効化
このセクションでは、Box のアプリケーション統合を有効にする方法について説明します。

**Box のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-box-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-box-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-box-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Box**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-box-tutorial/IC701023.png "Application Gallery")
7. 結果ウィンドウで **[Box]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")

## <a name="configure-single-sign-on"></a>Configure single sign-on
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Box に対する認証を行えるようにする方法を説明します。 

この手順の途中で、メタデータを Box.com にアップロードする必要があります。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Box** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-box-tutorial/IC769538.png "Configure single sign-on")
2. **[ユーザーの Box へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-box-tutorial/IC769539.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページで、**[Box テナント URL]** ボックスに、Box テナント URL (例: https://<mydomainname>.box.com) を入力し、**[次へ]** をクリックします。
   
  ![アプリケーション URL の構成](./media/active-directory-saas-box-tutorial/IC669826.png "Configure app URL")
4. **[Box でのシングル サインオンの構成]** ページで、メタデータをダウンロードするために、**[メタデータのダウンロード]** をクリックし、データ ファイルをコンピューターのローカルに保存します。
   
  ![シングル サインオンの構成](./media/active-directory-saas-box-tutorial/IC669824.png "Configure single sign-on")
5. メタデータ ファイルを Box サポート チームに転送します。 サポート チームは、シングル サインオンを構成する必要があります。
6. [シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
  ![シングル サインオンの構成](./media/active-directory-saas-box-tutorial/IC769540.png "Configure single sign-on")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

このセクションでは、Box への Active Directory ユーザー アカウントのプロビジョニングを有効にする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure クラシック ポータルの **Box** アプリケーション統合ページで、**[ユーザー プロビジョニングの構成]** をクリックして **[ユーザー プロビジョニングの構成]** ダイアログを開きます。 
   
    ![Enable automatic user provisioning](./media/active-directory-saas-box-tutorial/IC769541.png "Enable automatic user provisioning")
2. **[Box へのユーザー プロビジョニングを有効にする]** ダイアログ ページで、**[ユーザー プロビジョニングを有効にする]** をクリックします。 
   
    ![Enable automatic user provisioning](./media/active-directory-saas-box-tutorial/IC769544.png "Enable automatic user provisioning")
3. **[Box へのアクセスを許可するにはログインしてください]** ページで、必要な資格情報を入力し、**[認証する]** をクリックします。 
   
    ![Enable automatic user provisioning](./media/active-directory-saas-box-tutorial/IC769546.png "Enable automatic user provisioning")
4. **[Box のアクセス許可]** をクリックしてこの操作を承認し、Azure クラシック ポータルに戻ります。 
   
    ![Enable automatic user provisioning](./media/active-directory-saas-box-tutorial/IC769549.png "Enable automatic user provisioning")
5. **[プロビジョニングのオプション]** ページの **[プロビジョニングするオブジェクトの種類]** チェック ボックスを使用すると、ユーザー オブジェクトのほかに、グループ オブジェクトを Box にプロビジョニングするかどうかを選択できます。  詳細については、下の「ユーザーとグループの割り当て」セクションを参照してください。
6. 構成を終了するには、[完了] をクリックします。 
   
    ![Enable automatic user provisioning](./media/active-directory-saas-box-tutorial/IC769551.png "Enable automatic user provisioning")

## <a name="assign-a-test-user"></a>テスト ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Box に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Box** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。 
   
    ![ユーザーの割り当て](./media/active-directory-saas-box-tutorial/IC769552.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。 
   
   ![はい](./media/active-directory-saas-box-tutorial/IC767830.png "Yes")

10 分間待機し、アカウントが Box に同期されたことを確認します。

最初の検証手順として、Azure クラシック ポータルの Box アプリケーション統合ページの D でダッシュボードをクリックして、プロビジョニングの状態を確認できます。

![Dashboard](./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

正常に完了したユーザー プロビジョニング サイクルは、関連する状態で示されます。

![Integration status](./media/active-directory-saas-box-tutorial/IC769555.png "Integration status")

Box テナントでは、同期済みのユーザーは、**[管理コンソール]** の **[管理対象のユーザー]** に表示されます。

![Integration status](./media/active-directory-saas-box-tutorial/IC769556.png "Integration status")

## <a name="assign-users-and-groups"></a>ユーザーとグループの割り当て
Azure クラシック ポータルの **[Box] の [ユーザーとグループ]** タブでは、Box へのアクセスを許可するユーザーとグループを指定できます。 ユーザーまたはグループを割り当てると、次の処理が実行されます。

* Azure AD により、(直接割り当てまたはグループのメンバーシップによって) 割り当てられたユーザーに Box への認証が許可されます。 割り当てられていないユーザーには Box へのサインインが許可されず、Azure AD のサインイン ページでエラーが返されます。
* Box のアプリ タイルがユーザーの [アプリケーション起動プログラム](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)に追加されます。
* 自動プロビジョニングが有効になっている場合、割り当てられたユーザーまたはグループはプロビジョニング キューに追加され、自動的にプロビジョニングされます。
  
  * ユーザー オブジェクトのみをプロビジョニングするよう構成した場合は、直接割り当てられたすべてのユーザーがプロビジョニング キューに配置され、さらに、割り当てられたグループのメンバーであるユーザーもすべてプロビジョニング キューに配置されます。 
  * グループ オブジェクトをプロビジョニングするよう構成した場合は、割り当てられたすべてのグループ オブジェクトだけでなく、それらのグループのメンバーであるユーザーもすべて Box にプロビジョニングされます。 Box への書き込み時に、グループとユーザーのメンバーシップは保持されます。

**[属性] の [シングル サインオン]** タブを使用すると、SAML ベースの認証時に Box に提示するユーザーの属性 (または要求) を構成できます。また、**[属性] の [プロビジョニング]** タブを使用すると、プロビジョニング操作時の Azure AD から Box へのユーザーとグループの属性のフロー方法を構成できます。 詳細については、次のリソースを参照してください。

## <a name="additional-resources"></a>その他のリソース
* [Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ](active-directory-saml-claims-customization.md)
* [属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


