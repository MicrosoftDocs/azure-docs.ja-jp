---
title: "チュートリアル: Azure Active Directory と Veracode の統合 | Microsoft Docs"
description: "Azure Active Directory と Veracode の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: d49349c5ae08e67d91e30967f3644623211823ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>チュートリアル: Azure Active Directory と Veracode の統合

このチュートリアルでは、Veracode と Azure Active Directory (Azure AD) を統合する方法について説明します。

Veracode と Azure AD の統合には、次の利点があります。

- Veracode にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Veracode に自動サインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Veracode と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Veracode でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Veracode の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-veracode-from-the-gallery"></a>ギャラリーからの Veracode の追加
Azure AD への Veracode の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Veracode を追加する必要があります。

**ギャラリーから Veracode を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Veracode**」と入力し、結果ウィンドウで **[Veracode]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Veracode](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Veracode で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Veracode ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Veracode の関連ユーザーの間で、リンク関係が確立されている必要があります。

Veracode で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Veracode で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Veracode テスト ユーザーの作成](#create-a-veracode-test-user)** - Veracode で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Veracode アプリケーションでシングル サインオンを構成します。

**Veracode で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Veracode** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_samlbase.png)

3. アプリは Azure と事前に統合済みであるため、**[Veracode のドメインと URL]** セクションで実行が必要な手順はありません。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_url.png)

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_certificate.png) 

5. このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Veracode に対する認証を行うことができるようにする方法を説明します。

    Veracode アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを **SAML トークン属性** の構成に追加する必要があります。 次のスクリーンショットはその例です。
    
    ![属性](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_attr.png "Attributes")

6. 必要な属性のマッピングを追加するには、次の手順を実行します。

    | 属性名 | 属性値 |
    |--- |--- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | 電子メール |User.mail |
    
    a.この問題では、ターゲット (またはクラス) ラベルは "tip_amount" です。 上記の表の各データ行で、 **[ユーザー属性の追加]**をクリックします。
    
    ![属性](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr.png "Attributes")
    
    ![属性](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_addattr1.png "Attributes")
    
    b. **[属性名]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[属性値]** ボックスで、その行に対して表示される属性値を選択します。
    
    d. **[OK]**をクリックします。

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-veracode-tutorial/tutorial_general_400.png)

8. **[Veracode 構成]** セクションで、**[Veracode の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から **SAML エンティティ ID** をコピーします。

    ![Veracode 構成](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_configure.png) 

9. 別の Web ブラウザーのウィンドウで、Veracode 企業サイトに管理者としてログインします。

10. 上部のメニューで、**[設定]**、**[管理者]** の順にクリックします。
   
    ![Administration](./media/active-directory-saas-veracode-tutorial/ic802911.png "Administration")

11. **[SAML]** タブをクリックします。

12. **[組織の SAML 設定]** セクションで、次の手順に従います。
   
    ![Administration](./media/active-directory-saas-veracode-tutorial/ic802912.png "Administration")
   
    a.  **[発行者]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
    
    b. Azure Portal からダウンロードした証明書をアップロードするには、**[ファイルの選択]** をクリックします。
   
    c. **[自己登録を有効にする]**を選択します。

13. **[自己登録の設定]** セクションで次の手順を実行し、**[保存]** をクリックします。
   
    ![Administration](./media/active-directory-saas-veracode-tutorial/ic802913.png "Administration")
   
    a. **[新しいユーザーのアクティブ化]** として **[アクティブ化不要]** を選択します。
   
    b. **[ユーザー データの更新]** として **[優先 Veracode ユーザー データ]** を選択します。
   
    c. **[SAML 属性の詳細]**では、以下を選択します。
      * **ユーザー ロール**
      * **ポリシー管理者**
      * **レビュー担当者**
      * **セキュリティ リーダー**
      * **役員**
      * **申請者**
      * **作成者**
      * **すべてのスキャンの種類**
      * **チームのメンバーシップ**
      * **既定のチーム**

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-veracode-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-veracode-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-veracode-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-veracode-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-veracode-test-user"></a>Veracode テスト ユーザーの作成
Azure AD ユーザーが Veracode にログインできるようにするには、そのユーザーを Veracode にプロビジョニングする必要があります。 Veracode の場合、プロビジョニングは自動化されています。 ユーザー側で必要な操作はありません。 最初のシングル サインオンの試行中に、必要に応じてユーザーが自動的に作成されます。

> [!NOTE]
> Veracode から提供されている他の Veracode ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングすることもできます。
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Veracode へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Veracode に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Veracode]** を選択します。

    ![アプリケーションの一覧の Veracode リンク](./media/active-directory-saas-veracode-tutorial/tutorial_veracode_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Veracode] タイルをクリックすると、自動的に Veracode アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veracode-tutorial/tutorial_general_203.png

