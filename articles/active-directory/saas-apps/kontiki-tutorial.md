---
title: 'チュートリアル: Azure Active Directory と Kontiki の統合 | Microsoft Docs'
description: Azure Active Directory と Kontiki の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 9e0b438e0cfa729300099463086248780c69484c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95019842"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>チュートリアル: Azure Active Directory と Kontiki の統合

このチュートリアルでは、Kontiki と Azure Active Directory (Azure AD) を統合する方法について説明します。

Kontiki と Azure AD の統合により、次の利点が得られます。

* Azure AD を使用して、Kontiki にアクセスするユーザーを管理できます。
* ユーザーは、自分の Azure AD アカウントで Kontiki に自動的にサインイン (シングル サインオン) できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Kontiki と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* シングル サインオンが有効な Kontiki のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、Kontiki を Azure AD と統合します。

Kontiki では、次の機能をサポートしています。

* **SP が起点となるシングル サインオン**
* **Just-in-time のユーザー プロビジョニング**

## <a name="add-kontiki-in-the-azure-portal"></a>Azure portal で Kontiki を追加する

Kontiki を Azure AD と統合するには、マネージド SaaS アプリの一覧に Kontiki を追加する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインする

1. 左側のメニューで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] オプション](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] オプション](common/add-new-app.png)

1. 検索ボックスに「**Kontiki**」と入力します。 検索結果で **[Kontiki]** を選択し、 **[追加]** を選択します。

    ![結果リストの Kontiki](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Kontiki で Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと Kontiki 内の関連ユーザーとの間にリンク関係を確立する必要があります。

Kontiki で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

| タスク | 説明 |
| --- | --- |
| **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** | ユーザーがこの機能を使用できるようにします。 |
| **[Kontiki シングル サインオンの構成](#configure-kontiki-single-sign-on)** | アプリケーションでシングル サインオン設定を構成します。 |
| **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** | Britta Simon という名前のユーザーの Azure AD シングル サインオンをテストします。 |
| **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** | Britta Simon が Azure AD シングル サインオンを使用できるようにします。 |
| **[Kontiki のテスト ユーザーの作成](#create-a-kontiki-test-user)** | Kontiki で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。 |
| **[シングル サインオンのテスト](#test-single-sign-on)** | 構成が機能することを確認します。 |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Kontiki による Azure AD シングル サインオンを構成します。

1. [Azure portal](https://portal.azure.com/) の **Kontiki** アプリケーション統合ウィンドウで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン オプションを構成する](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML]** または **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウで、 **[編集]** (鉛筆アイコン) を選択して **[基本的な SAML 構成]** ウィンドウを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ウィンドウで、 **[サインオン URL]** テキスト ボックスに、次のパターンを持つ URL を入力します。`https://<companyname>.mc.eval.kontiki.com`

    ![[Kontiki ドメインと URL] のシングル サインオン情報](common/sp-signonurl.png)

    > [!NOTE]
    > 使用する正しい値を取得するには、[Kontiki クライアント サポート チーム](https://kollective.com/support/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** の横の **[ダウンロード]** を選択します。 要件に基づいてダウンロード オプションを選択します。 お使いのコンピューターに証明書ファイルを保存します。

    ![フェデレーション メタデータ XML 証明書のダウンロード オプション](common/metadataxml.png)

1. **[Kontiki のセットアップ]** セクションで、要件に基づいて次の URL をコピーします。

    * ログイン URL
    * Azure AD 識別子
    * ログアウト URL

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Kontiki シングル サインオンの構成

Kontiki 側でシングル サインオンを構成するには、ダウンロードしたフェデレーション メタデータ XML と Azure portal からコピーした適切な URL を [Kontiki サポート チーム](https://kollective.com/support/)に送信する必要があります。 Kontiki サポート チームは、送られてきた情報を使用して、SAML シングル サインオン接続が両方の側で正しく設定されているかどうかを確認します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure ポータルで Britta Simon というテスト ユーザーを作成します。

1. Azure portal 内で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザー] と [すべてのユーザー] オプション](common/users.png)

1. **[ 新規ユーザー]** を選択します。

    ![[新しいユーザー] オプション](common/new-user.png)

1. **[ユーザー]** ウィンドウで、次の手順を実行します。

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに「**brittasimon\@\<your-company-domain>.\<extension>** 」と入力します。 たとえば、「**brittasimon\@contoso.com**」です。

    1. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示された値を書き留めます。

    1. **［作成］** を選択します

    ![[ユーザー] ウィンドウ](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Kontiki へのアクセス権を付与して、彼女が Azure シングル サインオンを使用できるようにします。

1. Azure portal 内で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[Kontiki ]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションの一覧で **[Kontiki]** を選択します。

    ![アプリケーションの一覧の Kontiki](common/all-applications.png)

1. メニューで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] オプション](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

1. **[ユーザーとグループ]** ウィンドウで、ユーザーの一覧から **Britta Simon** を選択します。 **[選択]** を選択します。

1. SAML アサーションでロール値が必要な場合は、 **[ロールの選択]** ウィンドウで、ユーザーに関連したロールを一覧から選択します。 **[選択]** を選択します。

1. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。

### <a name="create-a-kontiki-test-user"></a>Kontiki のテスト ユーザーの作成

Kontiki でのユーザー プロビジョニングの構成にあたって必要な操作はありません。 割り当てられたユーザーがマイ アプリ ポータルを使用して Kontiki にサインインしようとすると、そのユーザーが存在するかどうかが Kontiki によって確認されます。 ユーザー アカウントが見つからない場合、Kontiki によりユーザー アカウントが自動的に作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

シングル サインオンをセットアップした後、マイ アプリ ポータルで **[Kontiki]** を選択すると、Kontiki に自動的にサインインされます。 マイ アプリ ポータルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](./tutorial-list.md)
- [Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)
- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)