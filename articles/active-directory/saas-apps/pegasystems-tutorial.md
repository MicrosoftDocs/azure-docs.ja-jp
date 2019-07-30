---
title: チュートリアル:Azure Active Directory と Pega Systems の統合 | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory と Pega Systems の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 013e477b66d2772698ce5c9cc61a59f8a5a04a5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094898"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>チュートリアル:Azure Active Directory と Pega Systems の統合

このチュートリアルでは、Pega Systems と Azure Active Directory (Azure AD) を統合する方法について説明します。

この統合には、次の利点があります。

* Azure AD を使用して、Pega Systems にアクセスするユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Pega Systems に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Pega Systems と Azure AD の統合を構成するには、以下が必要です。

* Azure AD サブスクリプション。 Azure AD 環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* シングル サインオンが有効な Pega Systems サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Pega Systems では、SP と IDP によって開始される SSO がサポートされます。

## <a name="add-pega-systems-from-the-gallery"></a>ギャラリーからの Pega Systems の追加

Azure AD への Pega Systems の統合を設定するには、ギャラリーからマネージド SaaS アプリの一覧に Pega Systems を追加する必要があります。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] を選択します。](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] を選択する](common/add-new-app.png)

4. 検索ボックスに「**Pega Systems**」と入力します。 検索結果で **[Pega Systems]** を選択し、 **[追加]** を選択します。

     ![[検索結果]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon という名前のテスト ユーザーを使用して、Pega Systems で Azure AD のシングル サインオンを構成およびテストします。
シングル サインオンを有効にするには、Azure AD ユーザーと Pega Systems の対応するユーザーの間で、関係を確立する必要があります。

Pega Systems で Azure AD のシングル サインオンを構成およびテストするには、以下の手順を完了する必要があります。

1. **[Azure AD のシングル サインオンを構成](#configure-azure-ad-single-sign-on)** して、この機能をユーザーに対して有効にします。
2. アプリケーション側で **[Pega Systems シングル サインオンを構成](#configure-pega-systems-single-sign-on)** します。
3. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
4. **[Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)** て、Azure AD のシングル サインオンをそのユーザーに対して有効にします。
5. ユーザーの Azure AD 表現にリンクされた **[Pega Systems テスト ユーザーを作成](#create-a-pega-systems-test-user)** します。
6. **[シングル サインオンをテスト](#test-single-sign-on)** して、この構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にします。

Pega Systems で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Pega Systems** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] の選択](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン方式の選択](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集**アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[編集] アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、アプリケーションを IDP 開始モードで構成する場合は、次の手順に従います。

    ![[基本的な SAML 構成] ダイアログ ボックス](common/idp-intiated.png)

    1. **[識別子]** ボックスに、次のパターンで URL を入力します。

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. **[応答 URL]** ボックスに、次のパターンで URL を入力します。

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. アプリケーションを SP 開始モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順に従います。

    ![[Pega Systems のドメインと URL] のシングル サインオン情報](common/both-advanced-urls.png)

    1. **[サインオン URL]** ボックスに、サインオン URL の値を入力します。

    1. **[リレー状態]** ボックスに、次のパターンで URL を入力します。`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > ここに示されている値はプレースホルダーです。 実際の識別子、応答 URL、サインオン URL、リレー状態 URL を使用する必要があります。 このチュートリアルで後述しているように、Pega アプリケーションから、識別子と応答 URL の値を取得することができます。 リレー状態値を取得するには、[Pega Systems のサポート チーム](https://www.pega.com/contact-us)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. Pega Systems アプリケーションには、特定の形式の SAML アサーションが必要です。 正しい形式のそれらを取得するには、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性を示しています。 **[編集]**   アイコンを選択して **[ユーザー属性]**   ダイアログ ボックスを開きます。

    ![[User Attributes (ユーザー属性)]](common/edit-attribute.png)

7. 前のスクリーンショットに示されている属性に加えて、Pega Systems アプリケーションでは、いくつかの追加の属性が SAML 応答で返される必要があります。 **[ユーザー属性]** ダイアログ ボックスの **[ユーザー要求]** セクションで、次の手順に従って、以下の SAML トークン属性を追加します。

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > これらの値は組織に固有です。 適切な値を指定します。

    1. **[新しい要求の追加]** を選択して **[ユーザー要求の管理]** ダイアログ ボックスを開きます。

    ![[新しい要求の追加] を選択する](common/new-save-attribute.png)

    ![[ユーザー要求の管理] ダイアログ ボックス](common/new-attribute-details.png)

    1. **[名前]** ボックスに、その行に表示される属性名を入力します。

    1. **[名前空間]** ボックスは空白のままにします。

    1. **[ソース]** で **[属性]** を選択します。

    1. **[ソース属性]** の一覧から、その行に表示される属性値を選択します。

    1. **[OK]** を選びます。

    1. **[保存]** を選択します。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、実際の要件に従って、 **[フェデレーション メタデータ XML]** の横にある **[ダウンロード]** リンクを選択し、証明書を自分のコンピューターに保存します。

    ![証明書のダウンロード リンク](common/metadataxml.png)

9. **[Pega Systems のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

    1. **[ログイン URL]** 。

    1. **[Azure AD 識別子]** 。

    1. **ログアウト URL**。

### <a name="configure-pega-systems-single-sign-on"></a>Pega Systems のシングル サインオンの構成

1. **Pega Systems** 側でシングル サインオンを構成するには、別のブラウザー ウィンドウで管理者アカウントを使って Pega ポータルにサインインします。

2. **[Create]\(作成\)**  >  **[SysAdmin]**  >  **[Authentication Service]\(認証サービス\)** の順に選択します。

    ![[Authentication Service]\(認証サービス\) を選択する](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. **[Create Authentication Service]\(認証サービスの作成\)** 画面で次の操作を実行します。

    ![[Create Authentication Service]\(認証サービスの作成\) 画面](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. **[Type]\(種類\)** ドロップダウン リストで、 **[SAML 2.0]** を選択します。

    1. **[Name]\(名前\)** ボックスに名前を入力します (例: **Azure AD SSO**)。

    1. **[Short Description]\(簡単な説明\)** ボックスに、任意の説明を入力します。  

    1. **[Create and open]\(作成して開く\)** を選択します。
    
4. **[Identity Provider (IdP) information]\(ID プロバイダー (IdP) 情報\)** セクションで **[Import IdP metadata]\(IdP メタデータのインポート\)** を選択し、Azure portal からダウンロードしたメタデータ ファイルを参照します。 **[Submit]\(送信\)** をクリックして、メタデータを読み込みます。

    ![ID プロバイダー (IdP) 情報セクション](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    インポートによって、次に示すように、IdP データが設定されます。

    ![インポートされた IdP データ](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. **[Service Provider (SP) settings]\(サービス プロバイダー (SP) 設定\)** セクションで、以下の手順を行います。

    ![サービス プロバイダー設定](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. **[Entity Identification]\(エンティティ ID\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]** ボックスに貼り付けます。

    1. **[Assertion Consumer Service (ACS) location]\(アサーション コンシューマー サービス (ACS) の場所\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[返信 URL]** ボックスに貼り付けます。

    1. **[Disable request signing]\(要求署名を無効にする\)** をオンにします。

7. **[保存]** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal で、左側のウィンドウの **[Azure Active Directory]** を選択し、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[すべてのユーザー] を選択する](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] の選択](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を完了します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** ボックスに、「**brittasimon@\<yourcompanydomain>.\<extension>** 」と入力します。 (例: BrittaSimon@contoso.com)。

    c. **[パスワードを表示]** を選択し、 **[パスワード]** ボックス内の値を書き留めます。

    d. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Pega Systems へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Pega Systems]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Pega Systems]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 画面の下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-a-pega-systems-test-user"></a>Pega Systems テスト ユーザーの作成

次に、Pega Systems で Britta Simon というユーザーを作成する必要があります。 [Pega Systems サポート チーム](https://www.pega.com/contact-us)と協力して、ユーザーを作成します。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

ここで、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストする必要があります。

アクセス パネルで [Pega Systems] タイルを選択すると、SSO を設定した Pega Systems インスタンスに自動的にサインインします。 詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)