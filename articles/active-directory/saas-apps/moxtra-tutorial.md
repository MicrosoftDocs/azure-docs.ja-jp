---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Moxtra の統合 | Microsoft Docs
description: Azure Active Directory と Moxtra の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3e53ba11744b0e78287ffc46c4aac7b99b16b23
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889636"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Moxtra の統合

このチュートリアルでは、Moxtra と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Moxtra を統合すると、次のことができます。

* Moxtra にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Moxtra に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Moxtra でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Moxtra では、**SP** によって開始される SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-moxtra-from-the-gallery"></a>ギャラリーから Moxtra を追加する

Azure AD への Moxtra の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Moxtra を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Moxtra**」と入力します。
1. 結果のパネルから **[Moxtra]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Moxtra の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Moxtra に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Moxtra の関連ユーザーとの間にリンク関係を確立する必要があります。

Moxtra に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Moxtra の SSO の構成](#configure-moxtra-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Moxtra テスト ユーザーの作成](#create-moxtra-test-user)** - Moxtra で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Moxtra** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** テキスト ボックスに URL として「`https://www.moxtra.com/service/#login`」と入力します。

1. Moxtra アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![image](common/edit-attribute.png)

1. その他に、Moxtra アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 [ユーザー属性] ダイアログの [ユーザー要求] セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | 名前 | ソース属性|
    | ------------------- | -------------------- |    
    | firstname | User.givenname |
    | lastname | User.surname |
    | idpid    | < Azure AD 識別子 >

    > [!Note]
    > 属性 **idpid** の値は、実際のものではありません。 手順 8 の **[Set up Moxtra]\(Moxtra の設定\)** セクションから実際の値を取得できます。 

    1. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    1. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    1. **[名前空間]** は空白のままにします。

    1. [ソース] として **[属性]** を選択します。

    1. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    1. **[OK]** をクリックします。

    1. **[Save]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Moxtra のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

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

このセクションでは、B.Simon に Moxtra へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Moxtra]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-moxtra-sso"></a>Moxtra の SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者として Moxtra 企業サイトにサインオンします。

2. 左のツール バーで、 **[管理コンソール] > [SAML Single Sign-on]\(SAML シングル サインオン\)** の順にクリックし、 **[新規]** をクリックします。
   
    ![Configure single sign-on](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. **[SAML]** ページで、次の手順を実行します。
   
    ![Configure single sign-on](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. **[名前]** テキスト ボックスに、構成の名前を入力します (例:*SAML*)。 
  
    b. **[IdP Entity ID]\(IdP エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。 
 
    c. **[Login URL]\(ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。 
 
    d. **[AuthnContextClassRef]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:ac:classes:Password**」と入力します。 
 
    e. **[NameID 形式]** ボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**」と入力します。 
 
    f. Azure Portal からダウンロードした証明書をメモ帳で開き、その内容をコピーして、 **[証明書]** ボックスに貼り付けます。    
 
    g. SAML 電子メール ドメイン テキストボックスに、SAML 電子メール ドメインを入力します。    
  
    >[!NOTE]
    >ドメインを検証するための手順を確認するには、下の**i**をクリックします。

    h. **[Update]** をクリックします。

### <a name="create-moxtra-test-user"></a>Moxtra テスト ユーザーの作成

このセクションの目的は、Moxtra で B.Simon というユーザーを作成することです。

**Moxtra で B.Simon というユーザーを作成するには、次の手順を実行します。**

1. Moxtra 企業サイトに管理者としてサインオンします。

1. 左のツール バーで、 **[Admin Console (管理コンソール)]、[User Management (ユーザー管理)]** の順にクリックし、 **[Add User (ユーザーの追加)]** をクリックします。
   
    ![Configure single sign-on](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. **[Add User]** ダイアログで、次の手順を実行します。
  
    a. **[名]** ボックスに「**B**」と入力します。
  
    b. **[姓]** ボックスに「**Simon**」と入力します。
  
    c. **[電子メール]** ボックスに、Azure portal と同じ B.simon のメール アドレスを入力します。
  
    d. **[Division (事業部)]** ボックスに「**Dev**」と入力します。
  
    e. **[Department (部門)]** ボックスに「**IT**」と入力します。
  
    f. **[管理者]** を選択します。
  
    g. **[追加]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Moxtra] タイルをクリックすると、SSO を設定した Moxtra に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Moxtra を試す](https://aad.portal.azure.com/)

