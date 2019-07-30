---
title: チュートリアル:Azure Active Directory と Abstract の統合 | Microsoft Docs
description: Azure Active Directory と Abstract の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81886fa9165269d89bde8306c5829be41952c190
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302583"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>チュートリアル:Abstract を Azure Active Directory と統合する

このチュートリアルでは、Abstract と Azure Active Directory (Azure AD) を統合する方法について説明します。 Abstract を Azure AD と統合すると、次のことが可能になります。

* Abstract にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Abstract に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Abstract でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Abstract では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-abstract-from-the-gallery"></a>ギャラリーからの Abstract の追加

Azure AD への Abstract の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Abstract を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Abstract**」と入力します。
1. 結果のパネルから **[Abstract]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Abstract に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Abstract の関連ユーザーとの間にリンク関係を確立する必要があります。

Abstract に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Abstract SSO の構成](#configure-abstract-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Abstract テスト ユーザーの作成](#create-abstract-test-user)** - Abstract で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Abstract** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは **IDP** 開始モードで事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.abstract.com/signin`」と入力します。

4. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Abstract SSO の構成

Abstract で SSO を構成するために必要になるので、Azure portal から `App Federation Metadata Url` と `Azure AD Identifier` を取得しておきます。

これらの情報は、 **[SAML でシングル サインオンをセットアップします]** ページに表示されます。

* `App Federation Metadata Url` は、 **[SAML 署名証明書]** セクションにあります。
* `Azure AD Identifier` は、 **[Abstract の設定]** セクションにあります。


これで、Abstract 上で SSO を構成する準備ができました。

>[!Note]
>Abstract の SSO 設定にアクセスするには、組織の管理者アカウントを使用して認証を行う必要があります。

1. [Abstract Web アプリ](https://app.abstract.com/)を開きます。
2. 左側のバーにある **[Permissions]\(アクセス許可\)** ページに移動します。
3. **[Configure SSO]\(SSO の構成\)** セクションで、 **[Metadata URL]\(メタデータ URL\)** と **[Entity ID]\(エンティティ ID\)** を入力します。
4. 手動による例外がある場合は、それを入力します。 手動による例外セクションに一覧表示されるメール アドレスでは、SSO がバイパスされ、そのメール アドレスとパスワードでログインできるようになります。 
5. **[変更を保存]** をクリックします。

>[!Note] 
>手動による例外の一覧には、プライマリ メール アドレスを使用する必要があります。 一覧表示されているメール アドレスがユーザーのセカンダリ メールである場合、SSO のアクティブ化は失敗します。 その場合は、失敗したアカウントのプライマリ メール アドレスを含むエラー メッセージが表示されます。 お客様がそのユーザーを知っていると確認した後に、そのプライマリ メール アドレスを手動による例外一覧に追加します。

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

このセクションでは、B.Simon に Abstract へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Abstract]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-abstract-test-user"></a>Abstract テスト ユーザーの作成

Abstract で SSO をテストするには:

1. [Abstract Web アプリ](https://app.abstract.com/)を開きます。
2. 左側のバーにある **[Permissions]\(アクセス許可\)** ページに移動します。
3. **[Test with my Account]\(自分のアカウントでテストする\)** をクリックします。 テストが失敗する場合は、[Microsoft のサポート チーム](https://www.abstract.com/help/contact/)にお問い合わせください。

>[!Note]
>Abstract の SSO 設定にアクセスするには、組織の管理者アカウントを使用して認証を行う必要があります。
この組織の管理者アカウントは、Azure portal 上で Abstract に割り当てる必要があります。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Abstract] タイルをクリックすると、SSO を設定した Abstract に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

