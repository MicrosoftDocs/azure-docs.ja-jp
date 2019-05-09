---
title: チュートリアル:Azure Active Directory と Pega Systems の統合 | Microsoft Docs
description: Azure Active Directory と Pega Systems の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 34fe5d85829d909989513214406ba96ea5be0aa8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271103"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>チュートリアル:Azure Active Directory と Pega Systems の統合

このチュートリアルでは、Pega Systems と Azure Active Directory (Azure AD) を統合する方法について説明します。
Pega Systems と Azure AD の統合には、次の利点があります。

* Pega Systems にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Pega Systems に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Pega Systems と Azure AD の統合を構成するには、以下が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Pega Systems でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Pega Systems では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-pega-systems-from-the-gallery"></a>ギャラリーからの Pega Systems の追加

Azure AD への Pega Systems の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Pega Systems を追加する必要があります。

**ギャラリーから Pega Systems を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Pega Systems**」と入力し、結果パネルで **Pega Systems** を選び、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の Pega Systems](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Pega Systems で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Pega Systems 内の関連ユーザー間にリンク関係が確立されている必要があります。

Pega Systems で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Pega Systems シングル サインオンの構成](#configure-pega-systems-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Pega Systems テスト ユーザーの作成](#create-pega-systems-test-user)** - Pega Systems で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Pega Systems で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Pega Systems** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Pega Systems のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、**[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Pega Systems のドメインと URL] のシングル サインオン情報](common/both-advanced-urls.png)

    a. **[サインオン URL]** ボックスに、サインオン URL の値を入力します。

    b. **[リレー状態]** ボックスに、`https://<CUSTOMERNAME>.pegacloud.io/prweb/sso` のパターンで URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL、リレー状態 URL でこれらの値を更新します。 このチュートリアルの後半で説明する Pega アプリケーションで、識別子と応答 URL の値を見つけることができます。 リレー状態については、[Pega Systems のクライアント サポート チーム](https://www.pega.com/contact-us)に連絡して値を取得してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. Pega Systems アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]** アイコンをクリックして、 **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

7. その他に、Pega Systems アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | Name | ソース属性|
    | ------------------- | -------------------- |
    | uid | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organization | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | 電話 | *********** |

    > [!NOTE]
    > これらは、お客様に固有な値です。 適切な値を指定してください。

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

9. **[Pega Systems のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-pega-systems-single-sign-on"></a>Pega Systems のシングル サインオンを構成する

1. **Pega Systems** 側でシングル サインオンを構成するには、別のブラウザー ウィンドウで管理者アカウントを使って **Pega ポータル**を開きます。

2. **[Create]\(作成\)** -> **[SysAdmin]** -> **[Authentication Service]\(認証サービス\)** の順に選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. **[Create Authentication Service]\(認証サービスの作成\)** 画面で次の操作を実行します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. [Type]\(種類\) で **[SAML 2.0]** を選択します

    b. **[Name]\(名前\)** ボックスに名前を入力します (例: Azure AD SSO)

    c. **[Short Description]\(簡単な説明\)** ボックスに、任意の説明を入力します  

    d. **[Create and open]\(作成して開く\)** をクリックします 
    
4. **[Identity Provider (IdP) information]\(ID プロバイダー (IdP) 情報\)** セクションで **[Import IdP metadata]\(IdP メタデータのインポート\)** をクリックし、Azure Portal からダウンロードしたメタデータ ファイルを参照します。 **[Submit]\(送信\)** をクリックして、メタデータを読み込みます。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
5. 次のように、IdP データが設定されます。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. **[Service Provider (SP) settings]\(サービス プロバイダー (SP) 設定\)** セクションで、以下の操作を行います。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. **[Entity Identification]\(エンティティ ID\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** にある **[識別子]** ボックスに貼り付けます。

    b. **[Assertion Consumer Service (ACS) location]\(アサーション コンシューマー サービス (ACS) の場所\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** にある **[返信 URL]** ボックスに貼り付けます。

    c. **[Disable request signing]\(要求署名を無効にする\)** をオンにします。

7. **[保存]**

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Pega Systems へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[Pega Systems]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Pega Systems]** を選択します。

    ![アプリケーションの一覧にある Pega Systems のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-pega-systems-test-user"></a>Pega Systems テスト ユーザーの作成

このセクションの目的は、Pega Systems で Britta Simon というユーザーを作成することです。 Pega Sysyems でユーザーを作成するには、[Pega Systems クライアント サポート チーム](https://www.pega.com/contact-us)と協力してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Pega Systems] タイルをクリックすると、SSO を設定した Pega Systems に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)