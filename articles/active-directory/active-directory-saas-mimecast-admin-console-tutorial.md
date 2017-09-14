---
title: "チュートリアル: Azure Active Directory と Mimecast Admin Console の統合 | Microsoft Docs"
description: "Azure Active Directory と Mimecast Admin Console の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: f401f592d79ad954aa466de74d3e3fbb18aa9a5b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>チュートリアル: Azure Active Directory と Mimecast Admin Console の統合

このチュートリアルでは、Mimecast Admin Console と Azure Active Directory (Azure AD) を統合する方法について説明します。

Mimecast Admin Console と Azure AD の統合には、次のメリットがあります。

- Mimecast Admin Console にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Mimecast Admin Console にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Mimecast Admin Console の統合を構成するには、次のアイテムが必要です。

- Azure AD サブスクリプション
- Mimecast Admin Console でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Mimecast Admin Console の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>ギャラリーからの Mimecast Admin Console の追加
Azure AD への Mimecast Admin Console の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Mimecast Admin Console を追加する必要があります。

**ギャラリーから Mimecast Admin Console を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Mimecast Admin Console**」と入力し、結果ウィンドウで **[Mimecast Admin Console]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Mimecast Admin Console](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Mimecast Admin Console で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Mimecast Admin Console ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Mimecast Admin Console の関連ユーザーの間で、リンク関係が確立されている必要があります。

Mimecast Admin Console で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Mimecast Admin Console で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Mimecast Admin Console テスト ユーザーの作成](#create-a-mimecast-admin-console-test-user)** - Mimecast Admin Console で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Mimecast Admin Console アプリケーションでシングル サインオンを構成します。

**Mimecast Admin Console で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Mimecast Admin Console** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

3. **[Mimecast Admin Console のドメインと URL]** セクションで、次の手順を実行します。

    ![[Mimecast Admin Console のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    **[サインオン URL]** ボックスに、次の URL を入力します。
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > サインオン URL は、リージョン固有のものになります。

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_400.png)

6. **[Mimecast Admin Console 構成]** セクションで **[Mimecast Admin Console の構成]** をクリックし、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から、**SAML エンティティ ID と SAML Single シングル サインオン サービス URL** をコピーします。

    ![Mimecast Admin Console 構成](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

7. 別の Web ブラウザー ウィンドウで、Mimecast Admin Console の企業サイトに管理者としてログインします。

8. **[サービス]\>[アプリケーション]** に移動します。

    ![Services](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794998.png "Services")

9. **[認証プロファイル]**をクリックします。

    ![Authentication Profiles](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794999.png "Authentication Profiles")
    
10. **[新しい認証プロファイル]**をクリックします。

    ![New Authentication Profiles](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795000.png "New Authentication Profiles")

11. [ **認証プロバイダー** ] セクションで、次の手順に従います。

    ![Authentication Profile](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795015.png "Authentication Profile")
    
    a. **[説明]** テキストボックスに、構成の名前を入力します。
    
    b. **[Mimecast Admin Console に SAML 認証を適用]**を選択します。
    
    c. **[プロバイダー]** で **[Azure Active Directory]** を選択します。
    
    d. Azure Portal からコピーした **SAML エンティティ ID** を **[発行者の URL]** ボックスに貼り付けます。
    
    e. Azure Portal からコピーした **SAML シングル サインオン サービス URL** を **[ログイン URL]** ボックスに貼り付けます。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Azure Portal からコピーした **SAML シングル サインオン サービス URL** を **[ログアウト URL]** ボックスに貼り付けます。
    
    >[!NOTE]
    >Mimecast Admin Console では、[ログイン URL] と [ログアウト URL] の値は同じです。
    
    g. Azure Portal からダウンロードした base-64 の証明書をメモ帳で開き、最初の行 (“*--*“) と最後の行 (“*--*“) を削除し、残りの内容をクリップボードにコピーして、**[Identity Provider Certificate (Metadata)]\(ID プロバイダー証明書 (メタデータ)\)** ボックスに貼り付けます。
    
    h. **[シングル サインオンの許可]**を選択します。
    
    i. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**をクリックします。
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Mimecast Admin Console テスト ユーザーの作成

Azure AD ユーザーが Mimecast Admin Console にログインできるようにするには、そのユーザーを Mimecast Admin Console にプロビジョニングする必要があります。 Mimecast Admin Console の場合、プロビジョニングは手動で行います。

* ユーザーを作成する前に、ドメインを登録する必要があります。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. 管理者として **[Mimecast Admin Console]** にサインオンします。
2. **[ディレクトリ]\>[内部]**に移動します。
   
   ![Directories](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795003.png "Directories")
3. **[新規ドメインの登録]**をクリックします。
   
   ![Register New Domain](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795004.png "Register New Domain")
4. 新しいドメインを作成したら、 **[新規アドレス]**をクリックします。
   
   ![New Address](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795005.png "New Address")
5. 新しいアドレスのダイアログで、次の手順に従います。
   
   ![保存](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795006.png "保存")
   
   a. 該当するテキストボックスに、プロビジョニングする有効な Azure AD アカウントの **[メール アドレス]**、**[グローバル名]**、**[パスワード]**、**[パスワードの確認]** 属性を入力します。

   b. [ **Save**] をクリックします。

>[!NOTE]
>Mimecast Admin Console から提供されている他の Mimecast Admin Console ユーザー アカウント作成ツールや API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Mimecast Admin Console へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**を Mimecast Admin Console に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Mimecast Admin Console]** を選択します。

    ![アプリケーションの一覧の Mimecast Admin Console リンク](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Mimecast Admin Console のタイルをクリックすると、Mimecast Admin Console アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_203.png


