---
title: チュートリアル:Azure Active Directory と HubSpot の統合 | Microsoft Docs
description: Azure Active Directory と HubSpot の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bbb307654d4aaf753a4a3284875dee4f5707f2a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901732"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>チュートリアル:Azure Active Directory と HubSpot の統合

このチュートリアルでは、HubSpot と Azure Active Directory (Azure AD) を統合する方法について説明します。

HubSpot と Azure AD の統合には、次の利点があります。

- HubSpot にアクセスできる Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に HubSpot にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

HubSpot と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- HubSpot のシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの HubSpot の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-hubspot-from-the-gallery"></a>ギャラリーからの HubSpot の追加

Azure AD への HubSpot の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に HubSpot を追加する必要があります。

**ギャラリーから HubSpot を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

4. 検索ボックスに、「**HubSpot**」と入力します。 結果ウィンドウで **[HubSpot]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/hubspot-tutorial/tutorial_hubspot_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、HubSpot で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する HubSpot ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと HubSpot の関連ユーザーの間で、リンク関係が確立されている必要があります。

HubSpot で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **HubSpot テスト ユーザーの作成** - HubSpot で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、HubSpot アプリケーションでシングル サインオンを構成します。

**HubSpot で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **HubSpot** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML]** モードの **[選択]** をクリックして、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/hubspot-tutorial/tutorial_general_301.png)

3. **SAML** モードを別のモードから変更する必要がある場合は、画面上部の **[シングル サインオン モードの変更]** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/tutorial_general_300.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![Configure single sign-on](./media/hubspot-tutorial/tutorial_general_302.png)

5. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[HubSpot のドメインと URL] のシングル サインオン情報](./media/hubspot-tutorial/tutorial_hubspot_url.png)

    a. **[識別子]** ボックスに、`https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>` のパターンを使用して URL を入力します。

    b. **[応答 URL]** ボックスに、`https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL に値を置き換えます。実際の値については後で説明します。 これらの値を取得するには、[HubSpot クライアント サポート チーム](https://help.hubspot.com/)に問い合わせてください。

    c. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[HubSpot のドメインと URL] のシングル サインオン情報](./media/hubspot-tutorial/tutorial_hubspot_url1.png)

    **[サインオン URL]** ボックスに、URL として「`https://app.hubspot.com/login`」を入力します。

6. **[SAML 署名証明書]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**証明書 (Base64)** をダウンロードし、証明書ファイルをコンピューターに保存します。

    ![Configure single sign-on](./media/hubspot-tutorial/tutorial_hubspot_certificate.png)

7. **[Set up HubSpot] (HubSpot のセットアップ)** セクションで、コピー ボタンをクリックして、**[ログイン URL]** と **[Azure AD 識別子]** の値をコピーします。

    ![Configure single sign-on](./media/hubspot-tutorial/tutorial_hubspot_configure.png)

8. ブラウザーで新しいタブを開き、HubSpot の管理者アカウントにログインします。

9. ページの右上隅の**設定アイコン**をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/config1.png)

10. **[Account Defaults] (アカウントの既定値)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/config2.png)

11. **[セキュリティ]** セクションまで下へスクロールし、**[Set up] (セットアップ)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/config3.png)

12. **[Set up single sign-on]\(シングル サインオンの設定\)** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/hubspot-tutorial/config4.png)

    a. **[コピー]** ボタンをクリックして、**[Audience URl(Service Provider Entity ID)] (対象ユーザー URI(サービス プロバイダー エンティティ ID))** 値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[Identifier] (識別子)** テキスト ボックスに貼り付けます。

    b. **[コピー]** ボタンをクリックして、**[Sign on URl,ACS,Recipient, or Redirect] (サインオン URI、ACS、受信者、またはリダイレクト)** 値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[Reply URL] (応答 URL)** テキスト ボックスに貼り付けます。

    c. **[Identity Provider Identifier or Issuer URL] (ID プロバイダーの識別子または発行者 URL)** テキストボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    d. **[ID プロバイダー のシングル サインオン URL]** テキストボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    e. ダウンロードした **証明書 (Base64)**  ファイルをメモ帳で開きます。 その内容をクリップボードにコピーし、 **[X.509 証明書]** テキストボックスに貼り付けます。

    f. **[確認]** をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![Azure AD ユーザーの作成][100]

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![Azure AD のテスト ユーザーの作成](./media/hubspot-tutorial/create_aaduser_01.png) 

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/hubspot-tutorial/create_aaduser_02.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[プロパティ]** を選択し、**[パスワードを表示]** チェック ボックスをオンにして、[パスワード] ボックスに表示された値を書き留めます。

    d. **作成**を選択します。

### <a name="creating-a-hubspot-test-user"></a>HubSpot のテスト ユーザーの作成

Azure AD ユーザーが HubSpot にログインできるようにするには、そのユーザーを HubSpot にプロビジョニングする必要があります。
HubSpot の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **HubSpot** 企業サイトに管理者としてログインします。

2. ページの右上隅の**設定アイコン**をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/config1.png)

3. **[Users & Teams]\(ユーザーとチーム)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/user1.png)

4. **[Create user]\(ユーザーの作成\)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/user2.png)

5. **[Add email addess(es)]\(メール アドレスの追加\)** ボックスにユーザーのメール アドレス (例: **brittasimon\@contoso.com**) を入力し、**[Next]\(次へ\)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/user3.png)

6. **[ユーザーの作成]** セクションで、個々のタブをすべて移動し、ユーザーの適切なオプションやアクセス許可を選択して **[Next]\(次へ)** をクリックします。

    ![Configure single sign-on](./media/hubspot-tutorial/user4.png)

7. **[送信]** をクリックしてユーザーに招待を送信します。

    ![Configure single sign-on](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > ユーザーは、招待状を受け取った後にアクティブ化されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に HubSpot へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[HubSpot]** を選択します。

    ![Configure single sign-on](./media/hubspot-tutorial/tutorial_hubspot_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンを選択します。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで HubSpot のタイルをクリックすると、HubSpot アプリケーションのページに自動的にログインします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hubspot-tutorial/tutorial_general_01.png
[2]: ./media/hubspot-tutorial/tutorial_general_02.png
[3]: ./media/hubspot-tutorial/tutorial_general_03.png
[4]: ./media/hubspot-tutorial/tutorial_general_04.png
[100]: ./media/hubspot-tutorial/tutorial_general_100.png
[200]: ./media/hubspot-tutorial/tutorial_general_200.png
[201]: ./media/hubspot-tutorial/tutorial_general_201.png
[202]: ./media/hubspot-tutorial/tutorial_general_202.png
[203]: ./media/hubspot-tutorial/tutorial_general_203.png
