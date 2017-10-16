---
title: "チュートリアル: Azure Active Directory と Absorb LMS の統合 | Microsoft Docs"
description: "Azure Active Directory と Absorb LMS の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 549e353bb41f1f31bc0441294d0a8066779402e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>チュートリアル: Azure Active Directory と Absorb LMS の統合

このチュートリアルでは、Absorb LMS と Azure Active Directory (Azure AD) を統合する方法について説明します。

Absorb LMS と Azure AD の統合には、次の利点があります。

- Absorb LMS にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが Azure AD アカウントで自動的に Absorb LMS にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Absorb LMS と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Absorb LMS でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルには実稼働環境を利用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

* ギャラリーからの Absorb LMS の追加
* Azure AD シングル サインオンの構成とテスト

## <a name="add-absorb-lms-from-the-gallery"></a>ギャラリーから Absorb LMS を追加する
Azure AD への Absorb LMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Absorb LMS を追加します。

次の方法でギャラリーから Absorb LMS を追加します。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ウィンドウ][2]
    
3. アプリケーションを追加するには、**[新しいアプリケーション]** ボタンを選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Absorb LMS**」と入力し、結果ウィンドウで **[Absorb LMS]** を選択し、**[追加]** をボタンを選択します。

    ![結果リストの Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon というテスト ユーザーに基づいて、Absorb LMS で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、特定の Absorb LMS ユーザーが Azure AD では何と呼ばれているか Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Absorb LMS でそれに対応するユーザーの間で、リンク関係を確立する必要があるということです。

このリンク関係を確立するには、Azure AD の *[ユーザー名]* 値に Absorb LMS の *[ユーザー名]* 値を割り当てます。

Absorb LMS で Azure AD のシングル サインオンを構成してテストするには、次からの 5 つのセクションで構成要素を完了します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Absorb LMS アプリケーションでシングル サインオンを構成します。

次のように Absorb LMS で Azure AD シングル サインオンを構成します。

1. Azure Portal の **Absorb LMS** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログ ボックスの **[モード]** ボックスで **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. **[Absorb LMS のドメインと URL]** セクションで、次の手順を実行します。

    ![[Absorb LMS のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.myabsorb.com/Account/SAML` の構文で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.myabsorb.com/Account/SAML` の構文で URL を入力します。
     
    > [!NOTE] 
    > ここの URL は実際の値ではありません。 実際の識別子と応答 URL に変更してください。 値を取得するには、[Absorb LMS クライアント サポート チーム](https://www.absorblms.com/support)にご連絡ください。 

4. **[SAML 署名証明書]** セクションの **[ダウンロード]** 列で、**[メタデータ XML]** を選択し、コンピューターにメタデータ ファイルを保存します。

    ![署名証明書のダウンロードのリンク](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. [ **保存**] を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
6. **[Absorb LMS Configuration]\(Absorb LMS 構成\)** セクションで **[Configure Absorb LMS]\(Absorb LMS を構成する\)** を選択し、**[サインオンの構成]** ウィンドウを開きます。それから **[クイック リファレンス]** セクションの **[サインアウト URL]** をコピーします。

    ![[Absorb LMS Configuration]\(Absorb LMS 構成\) ウィンドウ](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

7. 新しい Web ブラウザー ウィンドウで、Absorb LMS 企業サイトに管理者としてサインインします。

8. 右上にある **[アカウント]** ボタンを選択します。 

    ![[アカウント] ボタン](./media/active-directory-saas-absorblms-tutorial/1.png)

9. [アカウント] ウィンドウで、**[ポータルの設定]** を選択します。

    ![[ポータルの設定] リンク](./media/active-directory-saas-absorblms-tutorial/2.png)
    
10. **[ユーザー]** タブをクリックします。

    ![[ユーザー] タブ](./media/active-directory-saas-absorblms-tutorial/3.png)

11. [シングル サインオンの構成] ページで、次の手順を実行します。

    ![[シングル サインオンの構成] ページ](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. **[モード]** ボックスで、**[Identity Provider Initiated]\(ID プロバイダー開始\)** を選択します。

    b. メモ帳で、Azure Portal からダウンロードした証明書を開きます。 **-----BEGIN CERTIFICATE-----** タグと **-----END CERTIFICATE-----** タグを削除します。 次に **[キー]** ボックスに残りのコンテンツを貼り付けます。
    
    c. **[Id Property]\(ID プロパティ\)** ボックスで、Azure AD でユーザー ID として構成した属性を選択します。 たとえば、Azure AD で *userPrincipalName* が選択されている場合、**[ユーザー名]** を選択します。

    d. **[ログイン URL]** ボックスに、Azure Portal のアプリケーションの **[プロパティ]** ページにある**ユーザーのアクセス URL** を貼り付けます。

    e. **[ログアウト URL]** に、Azure Portal の **[サインオンの構成]** ウィンドウからコピーした **[サインアウト URL]** 値を貼り付けます。

12. **[Only Allow SSO Login]\(SSO ログインのみを許可する\)** を **[オン]** にします。

    ![[Only Allow SSO Login]\(SSO ログインのみを許可する\) の切り替え](./media/active-directory-saas-absorblms-tutorial/5.png)

13. **[保存]** を選択します。

> [!TIP]
> アプリの設定中に、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できます。 **[Active Directory]**  >  **[エンタープライズ アプリケーション]** セクションからアプリを追加した後、**[シングル サインオン]** タブを選択し、一番下の **[構成]** セクションから組み込みドキュメントにアクセスします。 詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure Portal で Britta Simon というテスト ユーザーを作成します。

![Azure AD のテスト ユーザーの作成][100]

Azure AD でテスト ユーザーを作成するには、次の手順に従います。

1. Azure Portal の左側のウィンドウで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** > **[すべてのユーザー]** の順に選択します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. ダイアログ ボックスの一番上にある **[追加]** を選択します。
 
    ![[追加] ボタン](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスの値を書き留めます。

    d. **[作成]**を選択します。

### <a name="create-an-absorb-lms-test-user"></a>Absorb LMS のテスト ユーザーの作成

Azure AD ユーザーが Absorb LMS にサインインするには、そのユーザーを Absorb LMS で設定する必要があります。  

Absorb LMS の場合、設定は手動で行います。

ユーザー アカウントを設定するには、次の操作を行います。

1. Absorb LMS 企業サイトに管理者としてサインインします。

2. 左側のウィンドウで、**[ユーザー]** を選択します。

    ![[Absorb LMS Users]\(Absorb LMS ユーザー\) リンク](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. **[ユーザー]** ウィンドウで、**[ユーザー]** を選択します。

    ![[ユーザー] リンク](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4. **[新規追加]** ドロップダウン リストで、**[ユーザー]** を選択します。

    ![[新規追加] ドロップダウン リスト](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. **[ユーザーの追加]** ページで、次の手順に従います。

    ![[ユーザーの追加] ページ](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. **[名]** ボックスに、ユーザーの名 (たとえば、**Britta**) を入力します。

    b. **[姓]** ボックスに、ユーザーの姓 (たとえば、**Simon**) を入力します。
    
    c. **[ユーザー名]** ボックスに、ユーザーの氏名 (たとえば、**Britta Simon**) を入力します。

    d. **[パスワード]** ボックスには、Britta Simon のパスワードを入力します。

    e. **[パスワードの確認]** ボックスに、パスワードを再度入力します。
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[アクティブ]** を **[アクティブ]** に切り替えます。  

6. **[保存]** を選択します。
 
### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon というユーザーに Absorb LMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

Absorb LMS に Britta Simon というユーザーを割り当てるには、次の手順に従います。

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に選択します。

    ![[すべてのアプリケーション] リンク][201] 

2. **アプリケーション**の一覧で **[Absorb LMS]** を選択します。

    ![アプリケーションの一覧の Absorb LMS のリンク](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク][202] 

4. **[追加]** を選択し、**[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** 一覧から、**[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンを選択します。

7. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで **Absorb LMS** タイルを選択すると、Absorb LMS アプリケーションに自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png

