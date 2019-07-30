---
title: チュートリアル:Azure Active Directory と Mitel Connect の統合 | Microsoft Docs
description: Azure Active Directory と Mitel Connect の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: e03870322df94a4c9587a3395c70925d2d2e838d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588217"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>チュートリアル:Azure Active Directory と Mitel MiCloud Connect の統合

このチュートリアルでは、Mitel MiCloud Connect と Azure Active Directory (Azure AD) を統合する方法について説明します。 MiCloud Connect と Azure AD の統合には、次の利点があります。

* Azure AD で、エンタープライズ資格情報を使用して、MiCloud Connect アプリにアクセスできるユーザーを制御できます。
* お使いのアカウントで、ユーザーが自身の Azure AD アカウントを使用して MiCloud Connect に自動的にサインイン (シングル サインオン) できるように設定できます。


SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

MiCloud Connect と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション

  Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Mitel MiCloud Connect アカウント

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、Azure AD のシングル サインオン (SSO) を構成してテストします。

* Mitel Connect では、**SP** によって開始される SSO がサポートされます

## <a name="adding-mitel-connect-from-the-gallery"></a>ギャラリーからの Mitel Connect の追加

Azure AD への Mitel Connect の統合を構成するには、Azure portal 内のマネージド SaaS アプリの一覧に、ギャラリーから Mitel Connect を追加する必要があります。

**ギャラリーから Mitel Connect を追加するには、次の手順を実行します。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** をクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** の順にクリックします。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索フィールドに「**Mitel Connect**」と入力し、結果のパネルから **[Mitel Connect]** をクリックして、 **[追加]** をクリックします。

     ![結果リストの Mitel Connect](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、MiCloud Connect に対する Azure AD シングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと MiCloud Connect 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

MiCloud Connect に対する Azure AD シングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD を使った SSO のための MiCloud Connect の構成](#configure-micloud-connect-for-sso-with-azure-ad)** - ユーザーがこの機能を使用できるようにして、アプリケーション側で SSO 設定を構成します。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
4. **[Mitel MiCloud Connect のテスト ユーザーの作成](#create-a-mitel-micloud-connect-test-user)** - MiCloud Connect アカウントで Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Azure AD を使った SSO のための MiCloud Connect の構成

このセクションでは、Azure portal 内で MiCloud Connect について Azure AD によるシングル サインオンを有効にすると共に、MiCloud Connect アカウントに Azure AD を使用した SSO を許可するよう構成します。

MiCloud Connect に Azure AD による SSO を構成する際は、Azure portal と Mitel アカウント ポータルを並べて開いておくのが最も簡単です。 Azure portal と Mitel アカウント ポータルの間で、いくつかの情報を互いにコピーする必要があります。


1. [Azure portal](https://portal.azure.com/) で構成ページを開くには、次の手順を実行します。

    a. **Mitel Connect** アプリケーション統合ページで、 **[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク](common/select-sso.png)

    b. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML]** をクリックします。

    ![シングル サインオン選択モード](common/select-saml-option.png)
    
    SAML ベースのサインオン ページが表示されます。

2. Mitel アカウント ポータルで構成ダイアログを開くには、次の手順を実行します。

    a. **[Phone System]\(電話システム\)** メニューで、 **[Add-On Features]\(アドオン機能\)** をクリックします。

    b. **[Single Sign-On]\(シングル サインオン\)** の右側で、 **[Activate]\(アクティブ化\)** または **[Settings]\(設定\)** をクリックします。
    
    [Connect Single Sign-On Settings]\(Connect シングル サインオン設定\) ダイアログ ボックスが開きます。
    
3. **[Enable Single Sign-On]\(シングル サインオンを有効にする\)** チェック ボックスをオンにします。
    ![画像](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Azure portal で、 **[基本的な SAML 構成]** セクションの **[編集]** アイコンをクリックします。
    ![画像](common/edit-urls.png)

    [基本的な SAML 構成] ダイアログ ボックスが開きます。

5.  Mitel アカウント ポータル内の **[Mitel Identifier (Entity ID)]\(Mitel 識別子 (エンティティ ID)\)** フィールドから URL をコピーし、Azure portal の **[識別子 (エンティティ ID)]** フィールドに貼り付けます。

6. Mitel アカウント ポータル内の **[Reply URL (Assertion Consumer Service URL)]\(応答 URL (Assertion Consumer Service URL)\)** フィールドから URL をコピーし、Azure portal の **[応答 URL (Assertion Consumer Service URL)]** フィールドに貼り付けます。  
   ![画像](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. **[サインオン URL]** ボックスに、次のいずれかの URL を入力します。

    * **https://portal.shoretelsky.com** - 既定の Mitel アプリケーションとして Mitel アカウント ポータルを使用する場合
    * **https://teamwork.shoretel.com** - 既定の Mitel アプリケーションとして Teamwork を使用する場合

    **注**: 既定の Mitel アプリケーションとは、ユーザーがアクセス パネルの [Mitel Connect] タイルをクリックしたときにアクセスされるアプリケーションです。 また、Azure AD からテスト セットアップを実行した場合にアクセスされるアプリケーションでもあります。

8. Azure portal 内の **[基本的な SAML 構成]** ダイアログ ボックスで、 **[保存]** をクリックします。

9. Azure portal 内の **[SAML ベースのサインオン]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** の横にある **[ダウンロード]** をクリックして**署名証明書**をダウンロードし、コンピューターに保存します。
    ![画像](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. テキスト エディターで署名証明書ファイルを開き、ファイル内のデータをすべてコピーして、Mitel アカウント ポータルの **[Signing Certificate]\(署名証明書\)** フィールドに貼り付けます。 
    ![画像](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. Azure portal の **[SAML ベースのサインオン]** ページの **[Setup Mitel Connect]\(Mitel Connect のセットアップ\)** セクションで、次の手順を実行します。

    a. **[ログイン URL]** フィールドから URL をコピーして、Mitel アカウント ポータルの **[Sign-in URL]\(サインイン URL\)** フィールドに貼り付けます。

    b. **[Azure AD 識別子]** フィールドから URL をコピーして、Mitel アカウント ポータルの **[Entity ID]\(エンティティ ID\)** フィールドに貼り付けます。
    ![画像](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Mitel アカウント ポータル内の **[Connect Single Sign-On Settings]\(Connect シングル サインオンの設定\)** ダイアログ ボックスで、 **[Save]\(保存\)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal 内の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順にクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** をクリックします。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. ユーザーのプロパティのダイアログで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@\<yourcompanydomain\>.\<extension\>」と入力します。  
たとえば、「 BrittaSimon@contoso.com 」のように入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Mitel Connect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** の順にクリックします。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Mitel Connect]** をクリックします。

    ![アプリケーションの一覧の Mitel Connect のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** をクリックします。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログで、 **[ユーザー]** の一覧から **[Britta Simon]** を選択し、画面の下部にある **[選択]** をクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、ユーザーに適したロールを **[ロールの選択]** ダイアログの一覧から選択し、画面の下部にある **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-a-mitel-micloud-connect-test-user"></a>Mitel MiCloud Connect のテスト ユーザーの作成

このセクションでは、お使いの MiCloud Connect アカウントで Britta Simon というユーザーを作成します。 シングル サインオンを使用する前に、ユーザーを作成し、アクティブにする必要があります。

Mitel アカウント ポータルでのユーザーの追加の詳細については、Mitel Knowledge Base で「[Adding a User (ユーザーの追加)](https://docs.shoretel.com/connectcloud/Account/Users/AddUser)」を参照してください。

次の詳細情報を使用して、MiCloud Connect アカウントにユーザーを作成します。

  * **[名前]:** Britta Simon

* **勤務先のメール アドレス:** `brittasimon@<yourcompanydomain>.<extension>`   
(例: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **ユーザー名:** `brittasimon@<yourcompanydomain>.<extension>`  
(例: [brittasimon@contoso.com](mailto:brittasimon@contoso.com)。ユーザー名は通常、ユーザーの勤務先のメール アドレスと同じ)

**注:** MiCloud Connect のユーザー名は、Azure 内のユーザーのメール アドレスと同じである必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [Mitel Connect] タイルをクリックすると自動的にリダイレクトされ、 **[サインオン URL]** フィールドで既定値として構成した MiCloud Connect アプリケーションにサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
