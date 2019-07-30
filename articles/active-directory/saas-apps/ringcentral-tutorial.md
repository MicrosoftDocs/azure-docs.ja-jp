---
title: チュートリアル:Azure Active Directory と RingCentral の統合 | Microsoft Docs
description: Azure Active Directory と RingCentral の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: c374c9e8fd91d50b7e6589f22f9bed09fbe0de39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092874"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>チュートリアル:Azure Active Directory と RingCentral の統合

このチュートリアルでは、RingCentral と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と RingCentral を統合すると、次のことができます。

* RingCentral にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して RingCentral に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure portal) でアカウントを管理する。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションを持っていない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* RingCentral でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 RingCentral では、**IDP** によって開始される SSO がサポートされます

## <a name="adding-ringcentral-from-the-gallery"></a>ギャラリーからの RingCentral の追加

Azure AD への RingCentral の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから RingCentral を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するために、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**RingCentral**」と入力します。
1. 結果のパネルから **[RingCentral]** を選択し、アプリを追加します。 アプリがテナントに追加される間、数秒待ちます。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**Britta Simon** というテスト ユーザーを使用して、RingCentral で Azure AD の SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと RingCentral の関連ユーザーとの間にリンク関係を確立する必要があります。

RingCentral で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[RingCentral SSO の構成](#configure-ringcentral-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[RingCentral のテスト ユーザーの作成](#create-ringcentral-test-user)** - RingCentral で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうか確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で Azure AD SSO を有効にするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **RingCentral** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル**がある場合は、次の手順に従います。

    1. **[メタデータ ファイルをアップロードします]** をクリックします。
    1. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。
    1. メタデータ ファイルが正常にアップロードされると、**識別子**と**応答 URL** の値が、 **[基本的な SAML 構成]** セクションに自動的に設定されます。

    > [!Note]
    > RingCentral SSO 構成ページで、**サービス プロバイダー メタデータ ファイル**を取得します。これについては後で説明します。

1. **サービス プロバイダー メタデータ ファイル**がない場合は、次のフィールドに値を入力します。

    a. **[識別子]** ボックスに次の URL を入力します。

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. **[応答 URL]** ボックスに、URL を入力します。

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>RingCentral SSO の構成

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として RingCentral にサインインします。

1. 上部の **[ツール]** をクリックします。

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. **[シングル サインオン]** に移動します。

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. **[Single Sign-on]\(シングル サインオン\)** ページの **[SSO Configuration]\(SSO 構成\)** セクションで、 **[Step 1]\(ステップ 1\)** の **[Edit]\(編集\)** をクリックして、次の手順を実行します。

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. **[Set up Single Sign-on]\(シングル サインオンのセットアップ\)** ページで、次の手順を実行します。

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. **[Browse]\(参照\)** をクリックし、Azure portal からダウンロードしたメタデータ ファイルをアップロードします。

    b. メタデータをアップロードすると、 **[SSO General Information]\(SSO の一般情報\)** セクションの値が自動的に設定されます。

    c. **[Attribute Mapping]\(属性マッピング\)** セクションの **[Map Email Attribute to]\(メール属性のマップ先\)** で `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` を選択します

    d. **[Save]** をクリックします。

    e. **[Step 2]\(ステップ 2\)** の **[Download]\(ダウンロード\)** をクリックして**サービス プロバイダー メタデータ ファイル**をダウンロードし、Azure portal 内でそれを **[基本的な SAML 構成]** セクションにアップロードして、 **[識別子]** と **[応答 URL]** を自動的に設定します。

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. 同じページで **[Enable SSO]\(SSO の有効化\)** セクションに移動し、次の手順を実行します。

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * **[Enable SSO Service]\(SSO サービスを有効にする\)** を選択します。

    * **[Allow users to log in with SSO or RingCentral credential]\(ユーザーが SSO または RingCentral 資格情報でログインできるようにする\)** を選択します。

    * **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`Britta Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に RingCentral へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[RingCentral]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-ringcentral-test-user"></a>RingCentral のテスト ユーザーの作成

このセクションでは、RingCentral で Britta Simon というユーザーを作成します。  [RingCentral クライアント サポート チーム](https://success.ringcentral.com/RCContactSupp) と連携して、RingCentral プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-sso"></a>SSO のテスト

アクセス パネル上で [RingCentral] タイルを選択すると、SSO を設定した RingCentral に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
