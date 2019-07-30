---
title: チュートリアル:Azure Active Directory と HubSpot の統合 | Microsoft Docs
description: Azure Active Directory と HubSpot の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b2c765778fc2bdd8425cc3f375831c0d317e753
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100885"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>チュートリアル:Azure Active Directory と HubSpot の統合

このチュートリアルでは、HubSpot と Azure Active Directory (Azure AD) を統合する方法について説明します。

HubSpot と Azure AD の統合により、次の利点が得られます。

* Azure AD を使用して、HubSpot にアクセスするユーザーを管理できます。
* ユーザーは、自分の Azure AD アカウントで HubSpot に自動的にサインイン (シングル サインオン) できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

HubSpot と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* シングル サインオンが有効な HubSpot のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、HubSpot を Azure AD と統合します。

HubSpot では、次の機能がサポートされています。

* **SP によって開始されるシングル サインオン**
* **IDP が起点となるシングル サインオン**

## <a name="add-hubspot-in-the-azure-portal"></a>Azure portal で HubSpot を追加する

HubSpot を Azure AD と統合するには、マネージド SaaS アプリの一覧に HubSpot を追加する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のメニューで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] オプション](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] オプション](common/add-new-app.png)

1. 検索ボックスに「**HubSpot**」と入力します。 検索結果で **HubSpot** を選択し、 **[追加]** を選択します。

    ![結果リストの HubSpot](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、HubSpot で Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと HubSpot 内の関連ユーザーとの間にリンク関係を確立する必要があります。

HubSpot で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

| タスク | 説明 |
| --- | --- |
| **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** | ユーザーがこの機能を使用できるようにします。 |
| **[HubSpot のシングル サインオンの構成](#configure-hubspot-single-sign-on)** | アプリケーションでシングル サインオン設定を構成します。 |
| **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** | Britta Simon という名前のユーザーの Azure AD シングル サインオンをテストします。 |
| **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** | Britta Simon が Azure AD シングル サインオンを使用できるようにします。 |
| **[HubSpot のテスト ユーザーの作成](#create-a-hubspot-test-user)** | HubSpot で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。 |
| **[シングル サインオンのテスト](#test-single-sign-on)** | 構成が機能することを確認します。 |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で HubSpot による Azure AD シングル サインオンを構成します。

1. [Azure portal](https://portal.azure.com/) の **HubSpot** アプリケーション統合ウィンドウで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン オプションを構成する](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML]** または **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウで、 **[編集]** (鉛筆アイコン) を選択して **[基本的な SAML 構成]** ウィンドウを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ウィンドウで、"*IDP-initiated モード*" を構成するには、次の手順のようにします。

    1. **[識別子]** ボックスに、次のパターンの URL を入力します: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<カスタマー ID\>。

    1. **[応答 URL]** ボックスに、次のパターンの URL を入力します: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<カスタマー ID\>。

    ![HubSpot のドメインと URL のシングル サインオン情報](common/idp-intiated.png)

    > [!NOTE]
    > URL の書式を設定するには、Azure portal の **[基本的な SAML 構成]** ウィンドウに示されているパターンを参照することもできます。

1. *SP-initiated* モードでアプリケーションを構成するには:

    1. **[追加の URL を設定します]** を選択します。

    1. **[サインオン URL]** ボックスに、「**https:\//app.hubspot.com/login**」と入力します。

    ![[追加の URL を設定します] オプション](common/metadata-upload-additional-signon.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** の横にある **[ダウンロード]** を選択します。 要件に基づいてダウンロード オプションを選択します。 お使いのコンピューターに証明書ファイルを保存します。

    ![証明書 (Base64) のダウンロード オプション](common/certificatebase64.png)

1. **[HubSpot のセットアップ]** セクションで、要件に基づいて次の URL をコピーします。

    * ログイン URL
    * Azure AD 識別子
    * ログアウト URL

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>HubSpot のシングル サインオンの構成

1. ブラウザーで新しいタブを開き、HubSpot の管理者アカウントにサインインします。

1. ページの右上隅にある **[Settings]\(設定\)** アイコンを選択します。

    ![HubSpot の [Settings]\(設定\) アイコン](./media/hubspot-tutorial/config1.png)

1. **[Account Defaults]\(アカウントの既定値\)** を選択します。

    ![HubSpot の [Account Defaults]\(アカウントの既定値\) オプション](./media/hubspot-tutorial/config2.png)

1. **[Security]\(セキュリティ\)** セクションまで下にスクロールし、 **[Set up]\(セットアップ\)** を選択します。

    ![HubSpot の [Set up]\(セットアップ\) オプション](./media/hubspot-tutorial/config3.png)

1. **[Set up single sign-on]\(シングル サインオンの設定\)** セクションで、次の手順のようにします。

    1. **[Audience URl (Service Provider Entity ID)]\(オーディエンス URI (サービス プロバイダー エンティティ ID)\)** ボックスで、 **[Copy]\(コピー\)** を選択して値をコピーします。 Azure portal で、 **[基本的な SAML 構成]** ウィンドウの **[識別子]** ボックスに値を貼り付けます。

    1. **[Sign on URl, ACS, Recipient, or Redirect]\(サインオン URl、ACS、受信者、またはリダイレクト\)** ボックスで、 **[Copy]\(コピー\)** を選択して値をコピーします。 Azure portal で、 **[基本的な SAML 構成]** ウィンドウの **[応答 URL]** ボックスに値を貼り付けます。

    1. HubSpot の **[Identity Provider Identifier or Issuer URL]\(ID プロバイダーの識別子または発行者 URL\)** ボックスに、Azure portal でコピーした **[Azure AD 識別子]** の値を貼り付けます。

    1. HubSpot の **[Identity Provider Single Sign-On URL]\(ID プロバイダーのシングル サインオン URL\)** ボックスに、Azure portal でコピーした **[ログイン URL]** の値を貼り付けます。

    1. メモ帳で、ダウンロードした証明書 (Base64) ファイルを開きます。 ファイルの内容を選択してコピーします。 次に、HubSpot でそれを  **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    1. **[認証]** を選択します。

        ![HubSpot の [Set up single sign-on]\(シングル サインオンの設定\) セクション](./media/hubspot-tutorial/config4.png)

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

このセクションでは、Britta Simon に HubSpot へのアクセス権を付与して、彼女が Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[HubSpot]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションの一覧で **[HubSpot]** を選択します。

    ![アプリケーションの一覧の HubSpot](common/all-applications.png)

1. メニューで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] オプション](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

1. **[ユーザーとグループ]** ウィンドウで、ユーザーの一覧から **Britta Simon** を選択します。 **[選択]** を選択します。

1. SAML アサーションでロール値が必要な場合は、 **[ロールの選択]** ウィンドウで、ユーザーに関連したロールを一覧から選択します。 **[選択]** を選択します。

1. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。

### <a name="create-a-hubspot-test-user"></a>HubSpot のテスト ユーザーの作成

Azure AD ユーザーが HubSpot にサインインできるようにするには、そのユーザーが HubSpot でプロビジョニングされている必要があります。 HubSpot では、プロビジョニングは手動で行います。

HubSpot でユーザー アカウントをプロビジョニングするには

1. HubSpot 企業サイトに管理者としてサインインします。

1. ページの右上隅にある **[Settings]\(設定\)** アイコンを選択します。

    ![HubSpot の [Settings]\(設定\) アイコン](./media/hubspot-tutorial/config1.png)

1. **[Users & Teams]\(ユーザーとチーム\)** を選択します。

    ![HubSpot の [Users & Teams]\(ユーザーとチーム\) オプション](./media/hubspot-tutorial/user1.png)

1. **[Create user]\(ユーザーの作成\)** を選択します。

    ![HubSpot の [Create user]\(ユーザーの作成\) オプション](./media/hubspot-tutorial/user2.png)

1. **[Add email addess(es)]\(メール アドレスの追加\)** ボックスにユーザーのメール アドレスを brittasimon\@contoso.com という形式で入力し、 **[Next]\(次へ\)** をクリックします。

    ![HubSpot の [Create users]\(ユーザーの作成\) セクションの [Add email addess(es)]\(メール アドレスの追加\) ボックス](./media/hubspot-tutorial/user3.png)

1. **[Create users]\(ユーザーの作成\)** セクションで、各タブを選択します。各タブで、ユーザーに関連するオプションとアクセス許可を設定します。 次に、 **[次へ]** を選択します。

    ![HubSpot の [Create users]\(ユーザーの作成\) セクションのタブ](./media/hubspot-tutorial/user4.png)

1. ユーザーに招待を送信するには、 **[Send]\(送信\)** を選択します。

    ![HubSpot の [Send]\(送信\) オプション](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > ユーザーが招待を受け入れると、ユーザーはアクティブ化されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

シングル サインオンをセットアップした後、マイ アプリ ポータルで **[HubSpot]** を選択すると、HubSpot に自動的にサインインされます。 マイ アプリ ポータルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
