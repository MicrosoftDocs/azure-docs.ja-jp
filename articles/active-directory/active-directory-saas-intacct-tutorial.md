---
title: "チュートリアル: Azure Active Directory と Intacct の統合 | Microsoft Docs"
description: "Azure Active Directory で Intacct を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 20db13c5ba67fcc402db5c692842719dd9a34eb1
ms.openlocfilehash: 0ea77d5f9c5b139ce6f87bc0cdd817166b03c22e
ms.lasthandoff: 03/01/2017

---

# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>チュートリアル: Azure Active Directory と Intacct の統合
このチュートリアルでは、Azure と Intacct の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Intacct テナント

このチュートリアルを完了すると、Intacct に割り当てた Azure Active Directory (Azure AD) ユーザーは、Intacct 企業サイトでのアプリケーションへのサインイン (サービス プロバイダーが開始したサインオン) や、[アクセス パネル](active-directory-saas-access-panel-introduction.md)の使用が可能になります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Intacct のアプリケーション統合の有効化
* シングル サインオンの構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-intacct-tutorial/IC790030.png "Scenario")

## <a name="enable-the-application-integration-for-intacct"></a>Intacct のアプリケーション統合の有効化
このセクションでは、Intacct のアプリケーション統合を有効にする方法について説明します。

**Intacct のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のウィンドウで、Active Directory のアイコンをクリックします。

   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューの上部のメニューで **[アプリケーション]** をクリックします。

   ![アプリケーション](./media/active-directory-saas-intacct-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。

   ![アプリケーションの追加](./media/active-directory-saas-intacct-tutorial/IC749321.png "Add application")
5. **[What do you want to do (実行する内容)]** ページで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-intacct-tutorial/IC749322.png "ギャラリーからのアプリケーションの追加")
6. 検索ボックスに「**Intacct**」と入力します。

   ![アプリケーション ギャラリー](./media/active-directory-saas-intacct-tutorial/IC790031.png "Application Gallery")
7. 結果ウィンドウで **[Intacct]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")

## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、ユーザーが Azure AD のアカウントで Intacct に対する認証を行えるようにする方法を説明します。 この認証には、SAML プロトコルに基づくフェデレーションを使用します。  

この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Intacct** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ページを開きます。

   ![シングル サインオンの構成](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configure single sign-on")
2. **[ユーザーの Intacct へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。

   ![シングル サインオンの構成](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページの **[サインオン URL]** ボックスに *https://Intacct.com/company* というパターンの URL を入力し、**[次へ]** をクリックします。

   ![アプリケーション URL の構成](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configure App URL")
4. **[Intacct でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。

   ![シングル サインオンの構成](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configure single sign-on")
5. 別の Web ブラウザー ウィンドウで、Intacct 企業サイトに管理者としてサインインします。
6. **[会社]** タブをクリックし、**[会社情報]** をクリックします。

   ![Company](./media/active-directory-saas-intacct-tutorial/IC790037.png "Company")
7. **[セキュリティ]** タブをクリックし、**[編集]** をクリックします。

   ![Security (セキュリティ)](./media/active-directory-saas-intacct-tutorial/IC790038.png "Security")
8. **[シングル サインオン (SSO)]** セクションで、次の手順を実行します。

   ![シングル サインオン](./media/active-directory-saas-intacct-tutorial/IC790039.png "シングル サインオン")

   1. **[シングル サインオンを有効にする]**を選択します。
   2. **[ID プロバイダーの種類]** として **[SAML 2.0]** を選択します。
   3. Azure クラシック ポータルの **[Intacct でのシングル サインオンの構成]** ページで **[発行者の URL]** の値をコピーし、それを **[Issuer URL (発行者 URL)]** ボックスに貼り付けます。
   4. Azure クラシック ポータルの **[Intacct でのシングル サインオンの構成]** ページで **[リモート ログイン URL]** の値をコピーし、それを **[Login URL (ログイン URL)]** ボックスに貼り付けます。
   5. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。 詳細については、「[How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」を参照してください。      
   6. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[Certificate (証明書)]** ボックスに貼り付けます。
   7. **[保存]**をクリックします。
9. Azure クラシック ポータルで、シングル サインオンの構成確認を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ページを閉じます。

   ![シングル サインオンの構成](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configure Single Sign-On")

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Intacct にサインインできるように設定するには、そのユーザーを Intacct にプロビジョニングする必要があります。 Intacct の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順を実行します。**

1. **Intacct** テナントにサインインします。
2. **[会社]** タブをクリックし、**[ユーザー]** をクリックします。

   ![ユーザー](./media/active-directory-saas-intacct-tutorial/IC790041.png "Users")
3. **[Add (追加)]** タブをクリックします。

   ![Add](./media/active-directory-saas-intacct-tutorial/IC790042.png "Add")
4. **[ユーザー情報]** セクションで、次の手順を実行します。

   ![User Information](./media/active-directory-saas-intacct-tutorial/IC790043.png "User Information")

   1. プロビジョニングする Azure AD アカウントの**ユーザー ID**、**姓**、**名**、**電子メール アドレス**、**役職**、**電話番号**を、**[User Information (ユーザー情報)]** セクションに入力します。
   2. プロビジョニングする Azure AD アカウントの**管理者特権**を選択します。
   3. **[保存]**をクリックします。 Azure AD アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

>[!NOTE]
>Azure AD ユーザー アカウントをプロビジョニングするには、Intacct から提供されているその他の Intacct ユーザー アカウント作成ツールまたは API を使用できます。
>

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、Azure AD ユーザーを Intacct に割り当てる必要があります。 ユーザーが割り当てられた後、アプリケーションにアクセスすることができます。

**ユーザーを Intacct に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Intacct** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。

   ![ユーザーの割り当て](./media/active-directory-saas-intacct-tutorial/IC790044.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。

   ![はい](./media/active-directory-saas-intacct-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。


### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Intacct へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるように設定します。

![ユーザーの割り当て][200]

**Intacct に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Intacct]** を選択します。

    ![Configure single sign-on](./media/active-directory-saas-intacct-tutorial/tutorial_intacct_50.png)

3. **[管理]** メニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックし、**[割り当ての追加]** で **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** で、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** で、**[選択]** ボタンをクリックします。

7. **[割り当ての追加]** で、**[割り当て]** ボタンをクリックします。



### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Intacct] タイルをクリックすると、自動的に Intacct アプリケーションにサインインします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intacct-tutorial/tutorial_general_203.png

