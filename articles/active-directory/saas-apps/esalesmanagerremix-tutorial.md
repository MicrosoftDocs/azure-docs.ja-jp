---
title: チュートリアル:Azure Active Directory と E Sales Manager Remix の統合 | Microsoft Docs
description: Azure Active Directory と E Sales Manager Remix の間のシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: c06595b683092abf52300481068daab26394c4cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995643"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Azure Active Directory と E Sales Manager Remix の統合

このチュートリアルでは、Azure Active Directory (Azure AD) と E Sales Manager Remix を統合する方法について説明します。

Azure AD と E Sales Manager Remix の統合には、次の利点があります。

- E Sales Manager Remix にアクセスするユーザーを Azure AD 上でコントロールできます。
- ユーザーが自分の Azure AD アカウントで自動的に E Sales Manager Remix にサインオン (シングル サインオン (SSO)) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

E Sales Manager Remix と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- E Sales Manager Remix SSO が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を *使用しない* ことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

* ギャラリーからの E Sales Manager Remix の追加
* Azure AD シングル サインオンの構成とテスト

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>ギャラリーから E Sales Manager Remix を追加する
Azure AD と E Sales Manager Remix の統合を構成するには、次の手順に従って、ギャラリーから管理対象 SaaS アプリの一覧に E Sales Manager Remix を追加する必要があります。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ][2]
    
1. 新しいアプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

1. [検索] ボックスに「**E Sales Manager Remix**」と入力します。結果一覧から **[E Sales Manager Remix]** を選択し、 **[追加]** を選択します。

    ![結果一覧の E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、E Sales Manager Remix で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する E Sales Manager Remix ユーザーが Azure AD で識別される必要があります。 言い換えると、Azure AD ユーザーと E Sales Manager Remix の同じユーザーの間で、リンク関係が確立されている必要があります。

E Sales Manager Remix で Azure AD のシングル サインオンを構成してテストするには、次の 5 つのセクションで構成要素を完了します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

次の手順に従って、Azure Portal で Azure AD のシングル サインオンを有効にし、E Sales Manager Remix アプリケーションでシングル サインオンを構成します。

1. Azure Portal の **E Sales Manager Remix** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] リンク][4]

1. **[シングル サインオン]** ウィンドウの **[シングル サインオン モード]** ボックスで、 **[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ウィンドウ](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. **[E Sales Manager Remix のドメインと URL]** で、次の手順を実行します。

    ![[E Sales Manager Remix のドメインと URL] のシングル サインオン情報](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. **[サインオン URL]** ボックスに、*https://\<Server-Based-URL>/\<sub-domain>/esales-pc* の形式で URL を入力します。

    b. **[識別子]** ボックスに、*https://\<Server-Based-URL>/\<sub-domain>/* 形式で URL を入力します。

    c. **[識別子]** の値は、このチュートリアルの中で後で使用します。
    
    > [!NOTE] 
    > 上記の値は、実際の値ではありません。 実際のサインオン URL と識別子で値を更新してください。 これらの値を取得するには、[E Sales Manager Remix クライアント サポート チーム](mailto:esupport@softbrain.co.jp)に問い合わせてください。

1. **[SAML 署名証明書]** で、 **[証明書 (Base64)]** を選択し、コンピューターに証明書ファイルを保存します。

    ![証明書 (Base64) ダウンロード リンク](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. **[その他のすべてのユーザー属性を表示および編集する]** チェックボックスをオンにし、**emailaddress** 属性を選択します。
    
    ![[ユーザー属性] ウィンドウ](./media/esalesmanagerremix-tutorial/configure1.png)

    **[属性の編集]** ウィンドウが開きます。

1. **[名前空間]** と **[名前]** の値をコピーします。 *\<Namespace>/\<Name>* のパターンの値を生成し、このチュートリアルの中で後で使用するために保存しておきます。

    ![[属性の編集] ウィンドウ](./media/esalesmanagerremix-tutorial/configure2.png)

1. **[E Sales Manager Remix の構成]** で、 **[E Sales Manager Remix の構成]** を選択します。

    ![[E Sales Manager Remix 構成] セクションを示すスクリーンショット。[E sales Manager Remix の構成] が選択されています。](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    **[サインオンの構成]** ウィンドウが開きます。

1. **[クイック リファレンス]** セクションのサインアウト URL と SAML シングル サインオン サービス URL をコピーします。

1. **[保存]** を選択します。

    ![[保存] ボタン](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. E Sales Manager Remix アプリケーションに管理者としてサインインします。

1. 右上のメニューから **[To Administrator Menu]\(管理者メニューに移動\)** を選択します。

    ![[To Administrator Menu]\(管理者メニューに移動\) コマンド](./media/esalesmanagerremix-tutorial/configure4.png)

1. 左側のウィンドウで、 **[System settings]\(システム設定\)**  >  **[Cooperation with external system]\(外部システムと連携\)** を選択します

    ![[System settings]\(システム設定\) と [Cooperation with external system]\(外部システムと連携\) のリンク](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. **[Cooperation with external system]\(外部システムと連携\)** ウィンドウで、 **[SAML]** を選択します。

    ![[Cooperation with external system]\(外部システムと連携\) ウィンドウ](./media/esalesmanagerremix-tutorial/configure6.png)

1. **[SAML authentication setting]\(SAML 認証設定\)** で、次の手順を実行します。

    ![[SAML authentication setting]\(SAML 認証設定\)](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. **[PC version]\(PC バージョン\)** チェック ボックスをオンにします。
    
    b. **[Collaboration item]\(コラボレーション項目\)** セクションで、ドロップダウン リストから **[email]\(電子メール\)** を選択します。

    c. **[Collaboration item]\(コラボレーション項目\)** ボックスに、先ほど Azure Portal からコピーした要求の値 ( **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** ) を貼り付けます。

    d. **[Issuer (entity ID)]\(発行者 (エンティティ ID)\)** ボックスに、Azure Portal の **[E Sales Manager Remix のドメインと URL]** セクションからコピーしておいた識別子の値を貼り付けます。

    e. Azure Portal からダウンロードした証明書をアップロードするには、 **[File selection]\(ファイルの選択\)** を選択します。

    f. **[Identity provider Login URL]\(ID プロバイダーの ログイン URL\)** ボックスに、Azure Portal からコピーしておいた SAML シングル サインオン サービス URL を貼り付けます。

    g. **[Identity provider Logout URL]\(ID プロバイダーの ログアウト URL\)** ボックスに、Azure Portal からコピーしておいたサインアウト URL を貼り付けます。

    h. **[Setting complete]\(設定完了\)** をクリックします。

> [!TIP]
> アプリのセットアップ中、上記手順の簡易版を [Azure Portal](https://portal.azure.com) でご覧いただけます。 **[Active Directory]**  >  **[エンタープライズ アプリケーション]** セクションにこのアプリを追加した後、 **[シングル サインオン]** タブを選択し、下部にある **[構成]** セクションの組み込みドキュメントにアクセスします。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure Portal で次の手順に従って Britta Simon というテスト ユーザーを作成します。

![Azure AD のテスト ユーザーの作成][100]

1. Azure Portal の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] リンク](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. 現在のユーザーの一覧を表示するには、 **[ユーザーとグループ]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. **[すべてのユーザー]** の上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **[ユーザー]** ウィンドウが開きます。

1. **[ユーザー]** ウィンドウで、次の手順を実行します。

    ![[ユーザー] ウィンドウ](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。

    d. **［作成］** を選択します
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>E Sales Manager Remix のテスト ユーザーの作成

1. E Sales Manager Remix アプリケーションに管理者としてサインオンします。

1. 右上のメニューから **[To Administrator Menu]\(管理者メニューに移動)** を選択します。

    ![E Sales Manager Remix 構成](./media/esalesmanagerremix-tutorial/configure4.png)

1. **[Your company settings]\(会社の設定\)**  >  **[Maintenance of departments and employees]\(部署と従業員のメンテナンス\)** の順に選択し、 **[Employees registered]\(登録済み従業員\)** を選択します。

    ![[Employees registered]\(登録済み従業員\) タブ](./media/esalesmanagerremix-tutorial/user1.png)

1. **[New employee registration]\(従業員の新規登録)** セクションで、次の手順を実行します。
    
    ![[New employee registration]\(従業員の新規登録) セクション](./media/esalesmanagerremix-tutorial/user2.png)

    a. **[Employee Name]\(従業員の名前\)** ボックスに、ユーザーの名前 (たとえば **Britta**) を入力します。

    b. その他の必須フィールドに入力します。
    
    c. SAML を有効にしている場合、管理者はサインイン ページからサインインできません。 **[Admin Login]\(管理ログイン\)** チェック ボックスを選択して、ユーザーに管理者サインイン特権を付与します。

    d. **[Registration]\(登録\)** を選択します。

1. 今後、管理者としてサインインするには、管理者権限を持っているユーザーとしてサインインします。その後、右上のメニューから **[To Administrator Menu]\(管理者メニューに移動\)** を選択します。

    ![[To Administrator Menu]\(管理者メニューに移動\) コマンド](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ユーザー Britta Simon に E Sales Manager Remix へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。 そのためには、次の手順を実行します。 

![ユーザー ロールを割り当てる][200] 

1. Azure Portal で **[アプリケーション]** ビューを開いて **ディレクトリ** ビューに移動してから、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] と [すべてのアプリケーション] のリンク][201] 

1. **アプリケーション** の一覧で **[E Sales Manager Remix]** を選択します。

    ![[E Sales Manager Remix] リンク](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** を選択し、 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ウィンドウの **[ユーザー]** 一覧で、**Britta Simon** を選択します。

1. **[選択]** ボタンを選択します。

1. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [E Sales Manager Remix] タイルを選択すると、自動的に E Sales Manager Remix アプリケーションにサインインします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png