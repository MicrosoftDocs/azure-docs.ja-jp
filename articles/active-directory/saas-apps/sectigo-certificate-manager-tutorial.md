---
title: チュートリアル:Azure Active Directory と Sectigo Certificate Manager の統合 | Microsoft Docs
description: Azure Active Directory と Sectigo Certificate Manager の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588230"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>チュートリアル:Azure Active Directory と Sectigo Certificate Manager の統合

このチュートリアルでは、Sectigo Certificate Manager と Azure Active Directory (Azure AD) を統合する方法について説明します。

Sectigo Certificate Manager と Azure AD の統合により、次の利点が得られます。

* Azure AD を使用して Sectigo Certificate Manager にアクセスできるユーザーを制御できます。
* ユーザーは自分の Azure AD アカウントを使用して Sectigo Certificate Manager に自動的にサインイン (シングル サインオン) できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

Sectigo Certificate Manager と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* シングル サインオンが有効な Sectigo Certificate Manager のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、Sectigo Certificate Manager を Azure AD と統合します。

Sectigo Certificate Manager では、次の機能がサポートされます。

* **SP によって開始されるシングル サインオン**
* **IDP が起点となるシングル サインオン**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Azure portal で Sectigo Certificate Manager を追加する

Sectigo Certificate Manager と Azure AD を統合するには、マネージド SaaS アプリの一覧に Sectigo Certificate Manager を追加する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のメニューで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] オプション](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] オプション](common/add-new-app.png)

1. 検索ボックスに「**Sectigo Certificate Manager**」と入力します。 検索結果で **Sectigo Certificate Manager** を選択し、 **[追加]** を選択します。

    ![結果リストの Sectigo Certificate Manager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Sectigo Certificate Manager で Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと Sectigo Certificate Manager 内の関連ユーザー間にリンクされた関係を確立する必要があります。

Sectigo Certificate Manager による Azure AD のシングル サインオンを構成してテストするには、次のビルド ブロックを完了する必要があります。

| タスク | 説明 |
| --- | --- |
| **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** | ユーザーがこの機能を使用できるようにします。 |
| **[Sectigo Certificate Manager シングル サインオンの構成](#configure-sectigo-certificate-manager-single-sign-on)** | アプリケーションでシングル サインオン設定を構成します。 |
| **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** | Britta Simon という名前のユーザーの Azure AD シングル サインオンをテストします。 |
| **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** | Britta Simon が Azure AD シングル サインオンを使用できるようにします。 |
| **[Sectigo Certificate Manager のテスト ユーザーの作成](#create-a-sectigo-certificate-manager-test-user)** | Sectigo Certificate Manager で、Azure AD の Britta Simon に対応し、そのユーザーにリンクされているユーザーを作成します。 |
| **[シングル サインオンのテスト](#test-single-sign-on)** | 構成が機能することを確認します。 |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Sectigo Certificate Manager による Azure AD シングル サインオンを構成します。

1. [Azure portal](https://portal.azure.com/) の **Sectigo Certificate Manager** アプリケーション統合ウィンドウで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン オプションを構成する](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML]** または **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウで、 **[編集]** (鉛筆アイコン) を選択して **[基本的な SAML 構成]** ウィンドウを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ウィンドウで、*IDP 開始モード*を構成するために、次の手順を実行します。

    1. **[識別子]** ボックスに、これらの URL のいずれかを入力します。
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. **[応答 URL]** ボックスに、これらの URL のいずれかを入力します。
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. **[追加の URL を設定します]** を選択します。

    1. **[リレー状態]** ボックスに、これらの URL のいずれかを入力します。
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Sectigo Certificate Manager ドメインと URL のシングル サインオン情報](common/idp-relay.png)

1.  *SP 開始モード*でアプリケーションを構成するには、次の手順を実行します。

    * **[サインオン URL]** ボックスに、これらの URL のいずれかを入力します。
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Sectigo Certificate Manager ドメインと URL のシングル サインオン情報](common/both-signonurl.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** の横にある **[ダウンロード]** を選択します。 要件に基づいてダウンロード オプションを選択します。 お使いのコンピューターに証明書ファイルを保存します。

    ![証明書 (Base64) のダウンロード オプション](common/certificatebase64.png)

1. **[Sectigo Certificate Manager のセットアップ]** セクションで、要件に従って以下の URL をコピーします。

    * ログイン URL
    * Azure AD 識別子
    * ログアウト URL

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Sectigo Certificate Manager シングル サインオンの構成

Sectigo Certificate Manager 側でシングル サインオンを構成するには、ダウンロードした証明書 (Base64) ファイルと、Azure portal からコピーした関連した URL を [Sectigo Certificate Manager サポート チーム](https://sectigo.com/support)に送信します。 Sectigo Certificate Manager チームは、送られてきた情報を使用して、SAML シングル サインオン接続が両方の側で正しく設定されているかどうかを確認します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure ポータルで Britta Simon というテスト ユーザーを作成します。

1. Azure portal 内で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザー] と [すべてのユーザー] オプション](common/users.png)

1. **[ 新規ユーザー]** を選択します。

    ![[新しいユーザー] オプション](common/new-user.png)

1. **[ユーザー]** ウィンドウで、次の手順を実行します。

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに、「**brittasimon\@\<会社のドメイン>.\<拡張子\>** 」と入力します。 たとえば、「**brittasimon\@contoso.com**」です。

    1. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示された値を書き留めます。

    1. **作成** を選択します。

    ![[ユーザー] ウィンドウ](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Sectigo Certificate Manager へのアクセスを許可することで、彼女が Azure シングル サインオンを使用できるようにします。

1. Azure portal 内で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Sectigo Certificate Manager]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションの一覧で **[Sectigo Certificate Manager]** を選択します。

    ![アプリケーションの一覧の Sectigo Certificate Manager](common/all-applications.png)

1. メニューで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] オプション](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

1. **[ユーザーとグループ]** ウィンドウで、ユーザーの一覧から **Britta Simon** を選択します。 **[選択]** を選択します。

1. SAML アサーションでロール値が必要な場合は、 **[ロールの選択]** ウィンドウで、ユーザーに関連したロールを一覧から選択します。 **[選択]** を選択します。

1. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Sectigo Certificate Manager のテスト ユーザーの作成

このセクションでは、Sectigo Certificate Manager で Britta Simon というユーザーを作成します。 [Sectigo Certificate Manager サポート チーム](https://sectigo.com/support)と連携して、Sectigo Certificate Manager プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

シングル サインオンをセットアップした後、マイ アプリ ポータルで **[Sectigo Certificate Manager]** を選択すると、Sectigo Certificate Manager に自動的にサインインされます。 マイ アプリ ポータルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


