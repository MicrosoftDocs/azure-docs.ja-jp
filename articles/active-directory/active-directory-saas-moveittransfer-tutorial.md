---
title: "チュートリアル: Azure Active Directory と MOVEit Transfer - Azure AD integration の統合 | Microsoft Docs"
description: "Azure Active Directory と MOVEit Transfer - Azure AD integration の間でシングル サインオンを構成する方法について確認します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: d35aceb9be2d0ff49f86a00cc84f5deb198d88f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>チュートリアル: Azure Active Directory と MOVEit Transfer - Azure AD integration の統合

このチュートリアルでは、MOVEit Transfer - Azure AD integration と Azure Active Directory (Azure AD) を統合する方法について説明します。

MOVEit Transfer - Azure AD integration と Azure AD の統合には、次の利点があります。

- MOVEit Transfer - Azure AD integration にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで MOVEit Transfer - Azure AD integration に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と MOVEit Transfer - Azure AD integration の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- MOVEit Transfer - Azure AD integration でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの MOVEit Transfer - Azure AD integration の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>ギャラリーからの MOVEit Transfer - Azure AD integration の追加
Azure AD への MOVEit Transfer - Azure AD integration の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に MOVEit Transfer - Azure AD integration を追加する必要があります。

**ギャラリーから MOVEit Transfer - Azure AD integration を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**MOVEit Transfer - Azure AD integration**」と入力し、結果パネルで **MOVEit Transfer - Azure AD integration** を選び、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の MOVEit Transfer - Azure AD integration](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、MOVEit Transfer - Azure AD integration で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する MOVEit Transfer - Azure AD integration ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと MOVEit Transfer - Azure AD integration の関連ユーザーの間で、リンク関係が確立されている必要があります。

MOVEit Transfer - Azure AD integration で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

MOVEit Transfer - Azure AD integration で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[MOVEit Transfer - Azure AD integration テスト ユーザーの作成](#create-a-moveit-transfer---azure-ad-integration-test-user)** - MOVEit Transfer - Azure AD integration で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、MOVEit Transfer - Azure AD integration アプリケーションでシングル サインオンを構成します。

**MOVEit Transfer - Azure AD integration で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **MOVEit Transfer - Azure AD integration** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

3. **[MOVEit Transfer - Azure AD integration のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. **[サインオン URL]** ボックスに、`https://contoso.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://contoso.com/<tenatid>` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post` のパターンを使用して URL を入力します。    
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値は、後で **[Service Provider Metadata URL]\(サービス プロバイダー メタデータ URL\)** セクションで参照するか、または [MOVEit Transfer - Azure AD integration クライアント サポート チーム](https://community.ipswitch.com/s/support)に問い合わせて入手できます。

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_400.png)
    
6. MOVEit Transfer テナントに管理者としてサインオンします。

7. 左側のナビゲーション ウィンドウで、 **[Settings (設定)]**をクリックします。

    ![アプリ側の [Settings]\(設定\) セクション](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)

8. **[Security Policies]\(セキュリティ ポリシー\) -> [User Auth]\(ユーザー認証\)** にある **[Single Signon]\(シングル サインオン\)** リンクをクリックします。

    ![アプリ側のセキュリティ ポリシー](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)

9. メタデータ URL リンクをクリックし、メタデータ ドキュメントをダウンロードします。

    ![サービス プロバイダー メタデータ URL](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * **entityID** が、**[MOVEit Transfer - Azure AD integration のドメインと URL]** セクションの **[識別子]** と一致することを確認します。
    * **AssertionConsumerService** の場所 URL が、**[MOVEit Transfer - Azure AD integration のドメインと URL]** セクションの **[応答 URL]** と一致することを確認します。
    
    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)

10. **[Add Identity Provider (ID プロバイダーの追加)]** ボタンをクリックして新しいフェデレーション ID プロバイダーを追加します。

    ![ID プロバイダーの追加](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)

11. **[Browse]\(参照\)** をクリックして Azure Portal からダウンロードしたメタデータ ファイルを選び、**[Add Identity Provider]\(ID プロバイダーの追加\)** をクリックしてダウンロードしたファイルをアップロードします。

    ![SAML ID プロバイダー](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)

12. **[Edit Federated Identity Provider Settings (フェデレーション ID プロバイダーの設定の編集)]** ページの **[Enabled (有効)]** で **[Yes (はい)]** を選択し、**[Save (保存)]** をクリックします。

    ![フェデレーション ID プロバイダーの設定](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)

13. **[Edit Federated Identity Provider User Settings]\(フェデレーション ID プロバイダー ユーザーの設定の編集\)** ページで、次の操作を実行します。
    
    ![フェデレーション ID プロバイダーの設定の編集](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. **[Login name (ログイン名)]** として **[SAML NameID]** を選択します。
    
    b. **[Full name]\(フル ネーム\)** として **[Other]\(その他\)** を選び、**[Attribute name]\(属性名\)** ボックスに値「`http://schemas.microsoft.com/identity/claims/displayname`」を入力します。
    
    c. **[Email]\(電子メール\)** として **[Other]\(その他\)** を選び、**[Attribute name]\(属性名\)** ボックスに値「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」を入力します。
    
    d. **[Auto-create account on signon (サインオン時にアカウントを自動作成する)]** で **[Yes (はい)]** を選択します。
    
    e. **[保存]** ボタンをクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>MOVEit Transfer - Azure AD integration テスト ユーザーの作成

このセクションの目的は、MOVEit Transfer - Azure AD integration で Britta Simon というユーザーを作成することです。 MOVEit Transfer - Azure AD integration では、Just-In-Time プロビジョニングがサポートされています。この設定は有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーが存在しない場合は、MOVEit Transfer - Azure AD integration へのアクセスを試みたときに、新しいユーザーが自動的に作成されます。

>[!NOTE]
>ユーザーを手動で作成する必要がある場合は、[MOVEit Transfer - Azure AD integration クライアント サポート チーム](https://community.ipswitch.com/s/support)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、MOVEit Transfer - Azure AD integration へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**MOVEit Transfer - Azure AD integration に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[MOVEit Transfer - Azure AD integration]** を選択します。

    ![アプリケーション一覧の MOVEit Transfer - Azure AD integration リンク](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで MOVEit Transfer - Azure AD integration のタイルをクリックすると、自動的に MOVEit Transfer - Azure AD integration アプリケーションにサインオンします。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png

