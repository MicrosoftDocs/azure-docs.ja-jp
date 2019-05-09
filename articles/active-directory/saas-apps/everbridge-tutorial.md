---
title: チュートリアル:Azure Active Directory と EverBridge の統合 | Microsoft Docs
description: Azure Active Directory と EverBridge の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1bb62c9a11971f72a6c96c4652b136c19812cb3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57839323"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>チュートリアル:Azure Active Directory と EverBridge の統合

このチュートリアルでは、EverBridge と Azure Active Directory (Azure AD) を統合する方法について説明します。

EverBridge と Azure AD の統合には、次の利点があります。

- EverBridge にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に EverBridge にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と EverBridge の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- EverBridge でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの EverBridge の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-everbridge-from-the-gallery"></a>ギャラリーからの EverBridge の追加

Azure AD への EverBridge の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に EverBridge を追加する必要があります。

**ギャラリーから EverBridge を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**EverBridge**」と入力し、結果パネルで **[EverBridge]** を選び、**[追加]** ボタンをクリックして、アプリケーションを追加します。

    ![結果リストの EverBridge](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、EverBridge で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する EverBridge ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと EverBridge の関連ユーザーの間で、リンク関係が確立されている必要があります。

EverBridge で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[EverBridge テスト ユーザーの作成](#creating-an-everbridge-test-user)** - EverBridge で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、EverBridge アプリケーションでシングル サインオンを構成します。

**EverBridge で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **EverBridge** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](common/tutorial_general_301.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](common/editconfigure.png)

    >[!NOTE]
    >両端 (つまり、Azure Portal と Everbridge Portal) で Manager ポータルまたは Member ポータルとして、アプリケーションの構成を行う必要があります。

4. **EverBridge Manager Portal** として **EverBridge** アプリケーションを構成するには、**[基本的な SAML 構成]** セクションで次の手順を行います。

    ![EverBridge のドメインと URL のシングル サインオン情報](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. **[識別子]** ボックスに、`https://sso.everbridge.net/<API_Name>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[EverBridge サポート チーム](mailto:support@everbridge.com)に問い合わせてください。

5. **EverBridge Member Portal** として **EverBridge** アプリケーションを構成するには、**[基本的な SAML 構成]** セクションで次の手順を行います。

   * **IDP** 開始モードでアプリケーションを構成する場合は、次のようにします。

       ![EverBridge のドメインと URL のシングル サインオン情報](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

       * **[識別子]** ボックスに、`https://sso.everbridge.net/<API_Name>/<Organization_ID>` の形式で URL を入力します。

       * **[応答 URL]** ボックスに、`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias` のパターンを使用して URL を入力します。

   * アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

       ![EverBridge のドメインと URL のシングル サインオン情報](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

       * **[サインオン URL]** ボックスに、`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true` のパターンを使用して URL を入力します。

     > [!NOTE]
     > これらは実際の値ではありません。 これらの値は、実際の識別子、応答 URL、サインオン URL で更新してください。 これらの値を取得するには、[EverBridge サポート チーム](mailto:support@everbridge.com)に問い合わせてください。

6. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**フェデレーション メタデータの XML** をダウンロードし、メタデータ ファイルをコンピューターに保存します。

    ![証明書のダウンロードのリンク](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. **[Set up EverBridge]\(EverBridge の設定\)** セクションで、要件に従って適切な URL をコピーします。

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

    ![EverBridge の構成](common/configuresection.png)

8. **EverBridge Manager Portal** としての **EverBridge** アプリケーション用に SSO を構成するには、次の手順を行います。 
 
9. 別の Web ブラウザー ウィンドウで、EverBridge に管理者としてログインします。

9. 上部にあるメニューの **[Settings (設定)]** タブをクリックし、**[Security (セキュリティ)]** の **[Single Sign-On (シングル サインオン)]** を選択します。
   
     ![Configure single sign-on](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. **[名前]** ボックスに、ID プロバイダーの名前 (自分の会社名など) を入力します。
   
     b. **[API Name (API 名)]** ボックスに API の名前を入力します。
   
     c. **[Choose File]\(ファイルの選択\)** ボタンをクリックして、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。
   
     d. [SAML Identity Location]\(SAML ID の場所\) で、**[Identity is in the NameIdentifier element of the Subject statement]\(ID は Subject ステートメントの NameIdentifier 要素にあります\)** を選択します。
   
     e. **[Identity Provider Login URL]\(ID プロバイダーのログイン URL\)** テキスト ボックスに、Azure Portal からコピーした**ログイン URL** の値を貼り付けます。
   
     f. [Service Provider Initiated Request Binding]\(サービス プロバイダーが開始した要求のバインド\) で、**[HTTP Redirect]\(HTTP リダイレクト\)** を選択します。

     g. **[保存]**

10. **EverBridge Member Portal** としての **EverBridge** アプリケーションでシングル サインオンを構成するには、ダウンロードした**フェデレーション メタデータ XML** を [Everbridge のサポート チーム](mailto:support@everbridge.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](common/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
  
### <a name="creating-an-everbridge-test-user"></a>EverBridge テスト ユーザーの作成

このセクションでは、Everbridge で Britta Simon というユーザーを作成します。 [EverBridge サポート チーム](mailto:support@everbridge.com)と協力して、EverBridge プラットフォームにユーザーを追加します。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に EverBridge へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[EverBridge]** を選択します。

    ![Configure single sign-on](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [EverBridge] タイルをクリックすると、自動的に EverBridge アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
