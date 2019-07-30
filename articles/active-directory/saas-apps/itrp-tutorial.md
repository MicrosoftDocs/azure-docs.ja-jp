---
title: チュートリアル:Azure Active Directory と ITRP の統合 | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory と ITRP の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: d44391624e29d2bdd182bb07452e0e8def2d1407
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656689"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>チュートリアル:Azure Active Directory と ITRP の統合

このチュートリアルでは、ITRP と Azure Active Directory (Azure AD) を統合する方法について説明します。
この統合には、次の利点があります。

* Azure AD を使用して、ITRP にアクセスできるユーザーを管理できます。
* ユーザーが自分の Azure AD アカウントを使用して ITRP に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

ITRP と Azure AD の統合を構成するには、以下が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオンが有効な ITRP サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ITRP では、SP Initiated SSO がサポートされます。

## <a name="add-itrp-from-the-gallery"></a>ギャラリーからの ITRP の追加

Azure AD への ITRP の統合を設定するには、ギャラリーからマネージド SaaS アプリの一覧に ITRP を追加する必要があります。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] を選択します。](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] を選択する](common/add-new-app.png)

4. 検索ボックスに「**ITRP**」と入力します。 検索結果で **[ITRP]** を選択し、 **[追加]** を選択します。

     ![[検索結果]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon という名前のテスト ユーザーを使用して、ITRP に対する Azure AD シングル サインオンを構成およびテストします。
シングル サインオンを有効にするには、Azure AD ユーザーと ITRP の対応するユーザーの間で、関係を確立する必要があります。

ITRP に対する Azure AD シングル サインオンを構成およびテストするには、以下の手順を完了する必要があります。

1. **[Azure AD のシングル サインオンを構成](#configure-azure-ad-single-sign-on)** して、この機能をユーザーに対して有効にします。
2. アプリケーション側で **[ITRP シングル サインオンを構成](#configure-itrp-single-sign-on)** します。
3. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
4. **[Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)** て、Azure AD のシングル サインオンをそのユーザーに対して有効にします。
5. **[ITRP テスト ユーザーを作成](#create-an-itrp-test-user)** して、Azure AD の対応するユーザーにリンクさせます。
6. **[シングル サインオンをテスト](#test-single-sign-on)** して、この構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にします。

ITRP に対する Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の ITRP アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] の選択](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン方式の選択](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集**アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[編集] アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、次の手順を実行します。

    ![[基本的な SAML 構成] ダイアログ ボックス](common/sp-identifier.png)

    1. **[サイン オン URL]** ボックスに、次のパターンの URL を入力します。
    
       `https://<tenant-name>.itrp.com`

    1. **[識別子 (エンティティ ID)]** ボックスに、次のパターンの URL を入力します。

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > これらの値はプレースホルダーです。 実際のサインオン URL と識別子を使用する必要があります。 この値を取得するには、[ITRP サポート チーム](https://www.itrp.com/support)に問い合わせてください。 また、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスに示されているパターンを参照することもできます。

5. **[SAML 署名証明書]** セクションで **[編集]** アイコンを選択して、 **[SAML 署名証明書]** ダイアログ ボックスを開きます。

    ![[編集] アイコン](common/edit-certificate.png)

6. **[SAML 署名証明書]** ダイアログ ボックスで、証明書の **[拇印]** の値をコピーして保存します。

    ![拇印の値をコピーする](common/copy-thumbprint.png)

7. **[Set up ITRP]\(ITRP の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

    1. **[ログイン URL]** 。

    1. **[Azure AD 識別子]** 。

    1. **[ログアウト URL]** 。

### <a name="configure-itrp-single-sign-on"></a>ITRP シングル サインオンの構成

1. 新しい Web ブラウザー ウィンドウで、ITRP 企業サイトに管理者としてサインインします。

1. ウィンドウの上部にある **[Settings]\(設定\)** アイコンを選択します。

    ![[Settings]\(設定\) アイコン](./media/itrp-tutorial/ic775570.png "[Settings]\(設定\) アイコン")

1. 左側のウィンドウで、 **[Single Sign-On]\(シングル サインオン\)** を選択します。

    ![[Single Sign-On]\(シングル サインオン\)](./media/itrp-tutorial/ic775571.png "[Single Sign-On]\(シングル サインオン\)")

1. **[Single Sign-On]\(シングル サインオン\)** 構成セクションで、次の手順を実行します。

    ![[Single sign-on]\(シングル サインオン\) セクション](./media/itrp-tutorial/ic775572.png "[Single sign-on]\(シングル サインオン\) セクション")

    ![[Single sign-on]\(シングル サインオン\) セクション](./media/itrp-tutorial/ic775573.png "[Single sign-on]\(シングル サインオン\) セクション")

    1. **[Enabled]** を選択します。

    1. **[Remote logout URL]\(リモート ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** の値を貼り付けます。

    1. **[SAML SSO URL]** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    1. **[Certificate Fingerprint]\(証明書のフィンガープリント\)** ボックスに、Azure portal からコピーした証明書の **[拇印]** の値を貼り付けます。

    1. **[保存]** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal で、左側のウィンドウの **[Azure Active Directory]** を選択し、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[すべてのユーザー] を選択する](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] を選択する](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに、「**BrittaSimon@\<yourcompanydomain>.\<extension>** 」と入力します。 (例: BrittaSimon@contoso.com)。

    1. **[パスワードを表示]** を選択し、 **[パスワード]** ボックス内の値を書き留めます。

    1. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ITRP へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[ITRP]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[ITRP]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧で **[Britta Simon]** を選択し、ウィンドウの下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 ウィンドウの下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-an-itrp-test-user"></a>ITRP のテスト ユーザーの作成

Azure AD ユーザーが ITRP にサインインできるようにするには、そのユーザーを ITRP に追加する必要があります。 手動で追加する必要があります。

ユーザー アカウントを作成するには、以下の手順に従います。

1. ITRP テナントにサインインします。

1. ウィンドウの上部にある **[Records]\(記録\)** アイコンを選択します。

    ![[Records]\(記録\) アイコン](./media/itrp-tutorial/ic775575.png "[Records]\(記録\) アイコン")

1. メニューで **[People]\(ユーザー\)** を選択します。

    ![[People]\(ユーザー\) の選択](./media/itrp-tutorial/ic775587.png "[People]\(ユーザー\) の選択")

1. プラス記号 ( **+** ) を選択して、新しいユーザーを追加します。

    ![プラス記号の選択](./media/itrp-tutorial/ic775576.png "プラス記号の選択")

1. **[Add New Person]\(新しいユーザーの追加\)** ダイアログ ボックスで、次の手順を実行します。

    ![[Add New Person]\(新しいユーザーの追加\) ダイアログ ボックス](./media/itrp-tutorial/ic775577.png "[Add New Person]\(新しいユーザーの追加\) ダイアログ ボックス")

    1. 追加する有効な Azure AD アカウントの名前とメール アドレスを入力します。

    1. **[保存]** を選択します。

> [!NOTE]
> ITRP から提供されているユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

ここで、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストする必要があります。

アクセス パネル上で [ITRP] タイルを選択すると、SSO を設定した ITRP インスタンスに自動的にサインインします。 アクセス パネルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
