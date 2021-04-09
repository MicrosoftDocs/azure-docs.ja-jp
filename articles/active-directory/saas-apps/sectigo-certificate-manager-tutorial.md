---
title: チュートリアル:Azure Active Directory と Sectigo Certificate Manager の統合 | Microsoft Docs
description: Azure Active Directory と Sectigo Certificate Manager の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: c589db84b6221aa23868b3b49aea84f33623619f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92673856"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>チュートリアル:Azure Active Directory と Sectigo Certificate Manager の統合

このチュートリアルでは、Sectigo Certificate Manager (SCM と呼ばれることもあります) と Azure Active Directory (Azure AD) を統合する方法について説明します。

Sectigo Certificate Manager と Azure AD の統合により、次の利点が得られます。

* Azure AD を使用して Sectigo Certificate Manager にアクセスできるユーザーを制御できます。
* ユーザーは自分の Azure AD アカウントを使用して Sectigo Certificate Manager に自動的にサインイン (シングル サインオン) できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Sectigo Certificate Manager と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* Sectigo Certificate Manager アカウント。

> [!NOTE]
> Sectigo では、複数の Sectigo Certificate Manager インスタンスを運用しています。 Sectigo Certificate Manager のメイン インスタンスは **https:\//cert-manager.com** です。このチュートリアルでは、この URL を使用します。  アカウントが別のインスタンスにある場合は、それに応じて URL を調整する必要があります。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、Sectigo Certificate Manager を Azure AD と統合します。

Sectigo Certificate Manager では、次の機能がサポートされます。

* **SP が起点となるシングル サインオン**
* **IDP が起点となるシングル サインオン**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Azure portal で Sectigo Certificate Manager を追加する

Sectigo Certificate Manager と Azure AD を統合するには、マネージド SaaS アプリの一覧に Sectigo Certificate Manager を追加する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。

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

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    1. Sectigo Certificate Manager のメイン インスタンスの場合には、 **[識別子 (エンティティ ID)]** ボックスに「**https:\//cert-manager.com/shibboleth**」と入力します。

    1. Sectigo Certificate Manager のメイン インスタンスの場合には、 **[応答 URL]** ボックスに「**https:\//cert-manager.com/Shibboleth.sso/SAML2/POST**」と入力します。
        
    > [!NOTE]
    > 一般に "*SP Initiated モード*" では **サインオン URL** が必須となっていますが、Sectigo Certificate Manager からのログインでは不要です。        

1. 必要に応じて、"*IDP Initiated モード*" を構成し、**テスト** を機能させるために、 **[基本的な SAML 構成]** セクションで次の手順を実行します。

    1. **[追加の URL を設定します]** を選択します。

    1. **[リレー状態]** ボックスに、Sectigo Certificate Manager の顧客別 URL を入力します。 Sectigo Certificate Manager のメイン インスタンスの場合には、「**https:\//cert-manager.com/customer/\<customerURI\>/idp**」を入力します。

    ![Sectigo Certificate Manager ドメインと URL のシングル サインオン情報](common/idp-relay.png)

1. **[ユーザー属性とクレーム]** セクションで、次の手順に従います。

    1. **追加の要求** をすべて削除します。
    
    1. **[新しいクレームの追加]** を選択し、次の 4 つのクレームを追加します。
    
        | 名前 | 名前空間 | source | ソース属性 | [説明] |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | empty | 属性 | user.userprincipalname | 管理者用 Sectigo Certificate Manager の **[IdP Person ID]\(IdP 人物 ID\)** フィールドの内容と合致している必要があります。 |
        | mail | empty | 属性 | User.mail | 必須 |
        | givenName | empty | 属性 | User.givenname | オプション |
        | sn | empty | 属性 | User.surname | オプション |

       ![Sectigo Certificate Manager - 4 つのクレームの追加](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** の隣にある **[ダウンロード]** を選択します。 XML ファイルを、お使いのコンピューターに保存します。

    ![フェデレーション メタデータ XML のダウンロード オプション](common/metadataxml.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Sectigo Certificate Manager シングル サインオンの構成

Sectigo Certificate Manager 側でシングル サインオンを構成するには、ダウンロードしたフェデレーション メタデータ XML ファイルを [Sectigo Certificate Manager サポート チーム](https://sectigo.com/support)に送信します。 Sectigo Certificate Manager チームは、送られてきた情報を使用して、SAML シングル サインオン接続が両方の側で正しく設定されているかどうかを確認します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure ポータルで Britta Simon というテスト ユーザーを作成します。

1. Azure portal 内で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザー] と [すべてのユーザー] オプション](common/users.png)

1. **[ 新規ユーザー]** を選択します。

    ![[新しいユーザー] オプション](common/new-user.png)

1. **[ユーザー]** ウィンドウで、次の手順を実行します。

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに「**brittasimon\@\<your-company-domain>.\<extension\>** 」と入力します。 たとえば、「**brittasimon\@contoso.com**」です。

    1. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示された値を記録しておきます。

    1. **［作成］** を選択します

    ![[ユーザー] ウィンドウ](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Sectigo Certificate Manager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

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

このセクションでは、Azure AD のシングル サインオン構成をテストします。

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Sectigo Certificate Manager からのテスト (SP Initiated シングル サインオン)

自分の顧客別 URL (Sectigo Certificate Manager のメイン インスタンスの場合には、https:\//cert-manager.com/customer/\<customerURI\>/) を参照し、 **[Or Sign In With]\(または、他のサインイン手段を使用する\)** の下にあるボタンを選択します。  正しく構成されている場合には、Sectigo Certificate Manager に自動的にサインインします。

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Azure のシングル サインオン構成からのテスト (IDP Initiated シングル サインオン)

**Sectigo Certificate Manager** アプリケーション統合ペインで、 **[シングル サインオン]** 、 **[テスト]** の順に選択します。  正しく構成されている場合には、Sectigo Certificate Manager に自動的にサインインします。

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>マイ アプリ ポータルを使ったテスト (IDP Initiated シングル サインオン)

マイ アプリ ポータルで **[Sectigo Certificate Manager]** を選択します。  正しく構成されている場合には、Sectigo Certificate Manager に自動的にサインインします。 マイ アプリ ポータルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](./tutorial-list.md)
- [Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)
- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)