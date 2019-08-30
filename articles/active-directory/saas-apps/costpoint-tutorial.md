---
title: チュートリアル:Azure Active Directory と Costpoint の統合 | Microsoft Docs
description: Azure Active Directory と Costpoint の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1a8b916feb2ad67623434f2b63468be72bf1aa
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879584"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>チュートリアル:Costpoint と Azure Active Directory の統合

このチュートリアルでは、Costpoint と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Costpoint を統合すると、次のことができます。

* Costpoint にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Costpoint に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Costpoint でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Costpoint では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="adding-costpoint-from-the-gallery"></a>ギャラリーからの Costpoint の追加

Azure AD への Costpoint の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Costpoint を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Costpoint**」と入力します。
1. 結果ウィンドウで **[Costpoint]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Costpoint に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Costpoint の関連ユーザーの間で、リンク関係を確立する必要があります。

Costpoint で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Costpoint の構成](#configure-costpoint)** - アプリケーション側で SSO 設定を構成します。
3. **[Azure AD テスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Costpoint テスト ユーザーの作成](#create-costpoint-test-user)** - Costpoint で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Costpoint** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル**がある場合は、次の手順に従います。

    > [!NOTE]
    > このチュートリアルの「**Costpoint メタデータの生成**」セクションで後述するように、サービス プロバイダー メタデータ ファイルを取得します。
 
    1. **[メタデータ ファイルをアップロードします]** をクリックします。
    
    1. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。
    
    1. メタデータ ファイルが正常にアップロードされると、**識別子**と**応答 URL** の値が、Costpoint セクションのテキスト ボックスに自動的に設定されます

        > [!Note]
        > **識別子**と**返信 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。 **[識別子 (エンティティ ID)]** および **[応答 URL (Assertion Consumer Service URL)]** が正しく設定され、 **[ACS URL]** が **/LoginServlet.cps** で終わる有効な Costpoint URL であることを確認してください。

    1. **[追加の URL を設定します]** をクリックします。

    1. **[リレー状態]** ボックスに、値を次の形式で URL を入力します。`system=[your system], (for example, **system=DELTEKCP**)`

1. **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。
    
    **[サインオン URL]** テキスト ボックスに URL として「`https://costpointteea.deltek.com/cpweb/cploginform.htm`」と入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、およびリレー状態でこれらの値を更新します。 これらの値を取得するには、[Costpoint クライアント サポート チーム](https://www.deltek.com/about/contact-us)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー アイコンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に保存します。

   ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="generate-costpoint-metadata"></a>Costpoint メタデータの生成

Costpoint SAML SSO 構成については、**DeltekCostpoint711Security.pdf** ガイドで説明されています。 これの、「**SAML Single Sign-on Setup -> Configure SAML Single Sign-on between Costpoint and Azure AD (SAML シングル サインオンの設定 -> Costpoint と Azure AD 間の SAML シングル サインオンの構成)** 」セクションを参照してください。 指示に従って、**Costpoint SP フェデレーション メタデータ XML** ファイルを生成します。 Azure portal の **[基本的な SAML 構成]** で、これを使用します。

![Costpoint Configuration Utility (Costpoint 構成ユーティリティ)](./media/costpoint-tutorial/config02.png)

> [!NOTE]
> [Costpoint クライアント サポート チーム](https://www.deltek.com/about/contact-us)から **DeltekCostpoint711Security.pdf** ガイドを入手できます。 このファイルがない場合は、サポート チームに連絡して入手してください。

### <a name="configure-costpoint"></a>Costpoint の構成

**Costpoint Configuration Utility (Costpoint 構成ユーティリティ)** に戻り、**アプリのフェデレーション メタデータ URL** を **[IdP Federation Metadata XML]\(IdP フェデレーション メタデータ XML\)** ボックス貼り付けます。その後、**DeltekCostpoint711Security.pdf** ガイドの手順を続行し、Costpoint SAML の設定を完了します。 

![Costpoint Configuration Utility (Costpoint 構成ユーティリティ)](./media/costpoint-tutorial/config01.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Costpoint へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Costpoint]** を選択します。
1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーションにロール値が必要な場合は、 **[ロールの選択]** ダイアログ ボックスでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンをクリックします。

### <a name="create-costpoint-test-user"></a>Costpoint テスト ユーザーの作成

このセクションでは、Costpoint 内にユーザーを作成します。 **ユーザー ID** が **B.SIMON** で、名前が **B.Simon** であるとします。 [Costpoint クライアント サポート チーム](https://www.deltek.com/about/contact-us)と協力して、Costpoint プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。
 
作成後、ユーザーの **[Authentication Method]\(認証方法\)** の選択は **Active Directory** であり、 **[SAML Single Sign-on]\(SAML シングル サインオン\)** チェック ボックスはオンであり、 **[Active Directory or Certificate ID]\(Active Directory または証明書 ID\)** は Azure Active Directory からのユーザー名である必要があります (次の図を参照)。

![Costpoint ユーザー](./media/costpoint-tutorial/user01.png)

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Costpoint] タイルを選択すると、SSO を設定した Costpoint に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)