---
title: チュートリアル:Azure Active Directory と Predictix Assortment Planning の統合 | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory と Predictix Assortment Planning の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: adf00d24c05deab149edb95b8087b8522dbda99a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515391"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>チュートリアル:Azure Active Directory と Predictix Assortment Planning の統合

このチュートリアルでは、Predictix Assortment Planning と Azure Active Directory (Azure AD) を統合する方法について説明します。
この統合には、次の利点があります。

* Azure AD を使用して、Predictix Assortment Planning にアクセスできるユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Predictix Assortment Planning に自動的にサインイン (シングル サインオン) するようにできます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Predictix Assortment Planning と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を取得できます。
* シングル サインオンが有効な Predictix Assortment Planning サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Predictix Assortment Planning では、SP によって開始される SSO がサポートされます。

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>ギャラリーからの Predictix Assortment Planning の追加

Azure AD への Predictix Assortment Planning の統合を設定するには、ギャラリーから管理対象 SaaS アプリの一覧に Predictix Assortment Planning を追加する必要があります。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] を選択します。](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] を選択する](common/add-new-app.png)

4. 検索ボックスに、「**Predictix Assortment Planning**」と入力します。 検索結果で **[Predictix Assortment Planning]** を選択し、 **[追加]** を選択します。

     ![[検索結果]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon という名前のテスト ユーザーを使用して、Predictix Assortment Planning で Azure AD のシングル サインオンを構成してテストします。
シングル サインオンを有効にするには、Azure AD ユーザーと Predictix Assortment Planning の対応するユーザーの関係を確立する必要があります。

Predictix Assortment Planning で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD のシングル サインオンを構成](#configure-azure-ad-single-sign-on)** して、この機能をユーザーに対して有効にします。
2. アプリケーション側で **[Predictix Assortment Planning のシングル サインオンを構成](#configure-predictix-assortment-planning-single-sign-on)** します。
3. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
4. **[Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)** て、Azure AD のシングル サインオンをそのユーザーに対して有効にします。
5. Azure AD でのユーザー表現にリンクされた **[Predictix Assortment Planning テスト ユーザーを作成](#create-a-predictix-assortment-planning-test-user)** します。
6. **[シングル サインオンをテスト](#test-single-sign-on)** して、この構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にします。

Predictix Assortment Planning で Azure AD のシングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Predictix Assortment Planning** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] の選択](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン方式の選択](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集** アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[編集] アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、次の手順を完了します。

    ![[基本的な SAML 構成] ダイアログ ボックス](common/sp-identifier.png)

    1. **[サイン オン URL]** ボックスに、次のパターンの URL を入力します。

        ```https
        https://<sub-domain>.ap.predictix.com/sso/request
        https://<sub-domain>.dev.ap.predictix.com/
        ```

    1. **[識別子 (エンティティ ID)]** ボックスに、次のパターンの URL を入力します。

        ```https
        https://<sub-domain>.ap.predictix.com
        https://<sub-domain>.dev.ap.predictix.com
        ```

    > [!NOTE]
    > これらの値はプレースホルダーです。 実際のサインオン URL と識別子を使用する必要があります。 これらの値を取得するには、[Predictix Assortment Planning サポート チーム](https://www.infor.com/support)に問い合わせてください。 また、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、要件に従って **[証明書 (Base64)]** の横にある **[ダウンロード]** リンクを選択し、証明書をコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificatebase64.png)

6. **[Predictix Assortment Planning のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

    1. **[ログイン URL]** 。

    1. **[Azure AD 識別子]** 。

    1. **[ログアウト URL]** 。

### <a name="configure-predictix-assortment-planning-single-sign-on"></a>Predictix Assortment Planning のシングル サインオンを構成する

Predictix Assortment Planning 側でシングル サインオンを構成するには、ダウンロードした証明書と Azure portal からコピーした URL を [Predictix Assortment Planning サポート チーム](https://www.infor.com/support)に送信する必要があります。 このチームは、SAML SSO 接続が両方の側で正しく設定されていることを確認します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal で、左側のウィンドウの **[Azure Active Directory]** を選択し、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[すべてのユーザー] を選択する](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] を選択する](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに「**BrittaSimon@\<yourcompanydomain>.\<extension>** 」と入力します (例: BrittaSimon@contoso.com)。

    1. **[パスワードを表示]** を選択し、 **[パスワード]** ボックス内の値を書き留めます。

    1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Predictix Assortment Planning へのアクセスを許可することで、このユーザーが Azure AD のシングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** 、 **[Predictix Assortment Planning]** の順に選択します。

    ![エンタープライズ アプリケーション](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Predictix Assortment Planning]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 画面の下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-a-predictix-assortment-planning-test-user"></a>Predictix Assortment Planning テスト ユーザーの作成

次に、Predictix Assortment Planning で Britta Simon という名前のユーザーを作成する必要があります。 [Predictix Assortment Planning サポート チーム](https://www.infor.com/support)と協力してユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成してアクティブ化する必要があります。

> [!NOTE]
> Azure AD アカウントの所有者が電子メールを受信し、アカウントを確認するためのリンクを選択した後に、そのアカウントがアクティブになります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

ここで、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストする必要があります。

アクセス パネルで [Predictix Assortment Planning] タイルを選択すると、SSO を設定した Predictix Assortment Planning インスタンスに自動的にサインインされます。 詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)