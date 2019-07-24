---
title: チュートリアル:Azure Active Directory と SecureW2 JoinNow Connector の統合 | Microsoft Docs
description: Azure Active Directory と SecureW2 JoinNow Connector の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2445b3af-f827-40de-9097-6f5c933d0f53
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b367befb90ec28ece963d67b479749e1c8ad363
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175321"
---
# <a name="tutorial-azure-active-directory-integration-with-securew2-joinnow-connector"></a>チュートリアル:Azure Active Directory と SecureW2 JoinNow Connector の統合

このチュートリアルでは、SecureW2 JoinNow Connector と Azure Active Directory (Azure AD) を統合する方法について説明します。

SecureW2 JoinNow Connector と Azure AD の統合には、次の利点があります。

- SecureW2 JoinNow Connector にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SecureW2 JoinNow Connector にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SecureW2 JoinNow Connector と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SecureW2 JoinNow Connector でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SecureW2 JoinNow Connector の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>ギャラリーからの SecureW2 JoinNow Connector の追加
Azure AD への SecureW2 JoinNow Connector の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SecureW2 JoinNow Connector を追加する必要があります。

**ギャラリーから SecureW2 JoinNow Connector を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**SecureW2 JoinNow Connector**」と入力して、結果パネルから **[SecureW2 JoinNow Connector]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の SecureW2 JoinNow Connector](./media/securejoinnow-tutorial/tutorial_securejoinnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SecureW2 JoinNow Connector で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SecureW2 JoinNow Connector ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SecureW2 JoinNow Connector の関連ユーザーの間で、リンク関係が確立されている必要があります。

SecureW2 JoinNow Connector で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SecureW2 JoinNow Connector のテスト ユーザーの作成](#create-a-securew2-joinnow-connector-test-user)** - SecureW2 JoinNow Connector で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SecureW2 JoinNow Connector アプリケーションでシングル サインオンを構成します。

**SecureW2 JoinNow Connector で Azure AD シングル サインオンを構成するには、次の手順を行います。**

1. Azure Portal の **SecureW2 JoinNow Connector** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/securejoinnow-tutorial/tutorial_securejoinnow_samlbase.png)

3. **[SecureW2 JoinNow Connector Domain and URLs]\(SecureW2 JoinNow Connector のドメインと URL\)** セクションで、次の手順を行います。

    ![[SecureW2 JoinNow Connector のドメインと URL] のシングル サインオン情報](./media/securejoinnow-tutorial/tutorial_securejoinnow_url.png)

    a. **[サインオン URL]** ボックスに、`https://<organization-identifier>-auth.securew2.com/auth/saml/SSO` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<organization-identifier>-auth.securew2.com/auth/saml` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[SecureW2 JoinNow Connector クライアント サポート チーム](mailto:support@securew2.com)に連絡してください。 

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/securejoinnow-tutorial/tutorial_securejoinnow_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/securejoinnow-tutorial/tutorial_general_400.png)

6. **SecureW2 JoinNow Connector** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [SecureW2 JoinNow Connector サポート チーム](mailto:support@securew2.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/securejoinnow-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/securejoinnow-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/securejoinnow-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/securejoinnow-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-a-securew2-joinnow-connector-test-user"></a>SecureW2 JoinNow Connector テスト ユーザーを作成する

このセクションでは、SecureW2 JoinNow Connector で Britta Simon というユーザーを作成します。  [SecureW2 JoinNow Connector クライアント サポート チーム](mailto:support@securew2.com)と協力して、SecureW2 JoinNow Connector プラットフォームでユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、SecureW2 JoinNow Connector へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SecureW2 JoinNow Connector に Britta Simon を割り当てるには、次の手順を行います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SecureW2 JoinNow Connector]** を選択します。

    ![アプリケーションの一覧の SecureW2 JoinNow Connector リンク](./media/securejoinnow-tutorial/tutorial_securejoinnow_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

**アプリケーションをテストするには、次の手順を行います。** 

a. SecureW2 JoinNow Connector クライアントを開き、一覧から適切なデバイスを選択して **[サインイン]** ボタンをクリックします。

b. 既定のブラウザーが開き、認証のために Azure Portal にリダイレクトされます。

c. 認証に成功すると、SecureW2 JoinNow Connector の初期ランディング ページに戻ります。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/securejoinnow-tutorial/tutorial_general_01.png
[2]: ./media/securejoinnow-tutorial/tutorial_general_02.png
[3]: ./media/securejoinnow-tutorial/tutorial_general_03.png
[4]: ./media/securejoinnow-tutorial/tutorial_general_04.png

[100]: ./media/securejoinnow-tutorial/tutorial_general_100.png

[200]: ./media/securejoinnow-tutorial/tutorial_general_200.png
[201]: ./media/securejoinnow-tutorial/tutorial_general_201.png
[202]: ./media/securejoinnow-tutorial/tutorial_general_202.png
[203]: ./media/securejoinnow-tutorial/tutorial_general_203.png

