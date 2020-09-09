---
title: チュートリアル:Azure Active Directory と Costpoint の統合 | Microsoft Docs
description: Azure Active Directory と Costpoint の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 1db1589561968cf1e2974edcee2bfe1cccb4a327
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549249"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>チュートリアル:Costpoint と Azure Active Directory の統合

このチュートリアルでは、Costpoint と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Costpoint を統合すると、次のことができます。

* Costpoint にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Costpoint に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Costpoint でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Costpoint では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="generate-costpoint-metadata"></a>Costpoint メタデータの生成

Costpoint SAML SSO 構成については、**DeltekCostpoint711Security.pdf** ガイドで説明されています。 Deltek Costpoint サポート サイトからこのガイドをダウンロードし、「**SAML シングル サインオンの設定**」 > 「**Costpoint と Microsoft Azure 間の SAML シングル サインオンの構成**」セクションを参照してください。 指示に従って、**Costpoint SP フェデレーション メタデータ XML** ファイルを生成します。 

![Costpoint Configuration Utility (Costpoint 構成ユーティリティ)](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>ギャラリーからの Costpoint の追加

Costpoint を Azure AD に統合するには、まず、Azure portal でギャラリーからマネージド SaaS アプリの一覧に Costpoint を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

1. 左側のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。

   ![Azure Active Directory のボタン](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

   ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。

   ![[新しいアプリケーション] ボタン](common/add-new-app.png)

1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Costpoint**」と入力します。

   ![結果一覧の Costpoint](common/search-new-app.png)

1. 結果リストで **[Costpoint]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Costpoint に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Costpoint の関連ユーザーの間で、リンク関係を確立する必要があります。

Costpoint で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
1. **[Costpoint の構成](#configure-costpoint)** - アプリケーション側で SAML SSO 設定を構成します。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Costpoint テスト ユーザーの作成](#create-a-costpoint-test-user)** - Costpoint で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. **Costpoint** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

   ![シングル サインオン構成のリンク](common/select-sso.png)

1. **[基本的な SAML 構成]** セクションで、"*サービス プロバイダー メタデータ ファイル*" がある場合は、次の手順を実行します。

   > [!NOTE]
   > サービス プロバイダー メタデータ ファイルは、「[Costpoint メタデータの生成](#generate-costpoint-metadata)」で取得します。 このファイルの使用方法については、このチュートリアルの後半で説明します。
 
   1. **[メタデータ ファイルをアップロードする]** を選択します。Costpoint によって以前に生成された **Costpoint SP フェデレーション メタデータ XML** ファイルを選択し、次に **[追加]** を選択してファイルをアップロードします。

      ![メタデータ ファイルをアップロードする](./media/costpoint-tutorial/upload-metadata.png)
    
   1. メタデータ ファイルが正常にアップロードされると、**識別子**と**応答 URL** の値が、Costpoint セクションに自動的に設定されます。

      > [!NOTE]
      > **識別子**と **URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。 **[識別子 (エンティティ ID)]** および **[応答 URL (Assertion Consumer Service URL)]** が正しく設定され、 **[ACS URL]** が **/LoginServlet.cps** で終わる有効な Costpoint URL であることを確認してください。

   1. **[追加の URL を設定します]** を選択します。 **[リレー状態]** に、次のパターンを使用して値を入力します: `system=[your system]`(例: **system = DELTEKCP**)。

1. **[Set up Single Sign-On with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、**コピー** アイコンを選択して **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に保存します。

   ![[SAML Signing Certificate (SAML 署名証明書)]](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Costpoint の構成

1. Costpoint Configuration Utility (Costpoint 構成ユーティリティ) に戻ります。 **[IdP Federation Metadata XML]\(IdP フェデレーション メタデータ XML\)** ボックスに、"*アプリのフェデレーション メタデータ URL*" ファイルの内容を貼り付けます。 

   ![Costpoint Configuration Utility (Costpoint 構成ユーティリティ)](./media/costpoint-tutorial/config-utility-idp.png)

1. **DeltekCostpoint711Security.pdf** ガイドの手順を続行して、Costpoint SAML の設定を完了します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure portal で B.Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

   ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

1. **[ 新規ユーザー]** を選択します。

   ![[新しいユーザー] ボタン](common/new-user.png)

1. **[ユーザー]** プロパティで、次の手順を実行します。

   ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

   1. **[名前]** フィールドに「**B.Simon**」と入力します。
   
   1. **[ユーザー名]** フィールドに「`b.simon\@yourcompanydomain.extension`」と入力します (たとえば、B.Simon@contoso.com)。
   
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** フィールドに表示された値を書き留めます。
   
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Costpoint へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

1. アプリケーションの一覧で **[Costpoint]** を選択します。

1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

   ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、 **[ユーザー]** 一覧から **[B.Simon]** を選択します。 次に **[選択]** を選択します。

1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択し、 **[選択]** を選択します。

1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-a-costpoint-test-user"></a>Costpoint テスト ユーザーの作成

このセクションでは、Costpoint 内にユーザーを作成します。 ユーザー ID が **B.SIMON** で、ユーザーの名前が **B.Simon** であるとします。 [Costpoint クライアント サポート チーム](https://www.deltek.com/about/contact-us)と協力して、Costpoint プラットフォームにユーザーを追加してください。 ユーザーがシングル サインオンを使用できるようにするには、事前にユーザーを作成し、有効化する必要があります。

ユーザーの作成後、ユーザーの **[Authentication Method]\(認証方法\)** の選択は **[Active Directory]** であり、 **[SAML Single Sign-on]\(SAML シングル サインオン\)** チェック ボックスはオンであり、 **[Active Directory or Certificate ID]\(Active Directory または証明書 ID\)** は Azure Active Directory からのユーザー名である必要があります (次のスクリーンショットを参照)。

![Costpoint ユーザー](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>SSO のテスト

SSO が設定されているため、アクセス パネルで [Costpoint] タイルを選択すると Costpoint アプリケーションに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
