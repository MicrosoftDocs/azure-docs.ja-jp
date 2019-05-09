---
title: チュートリアル:Azure Active Directory と ServiceNow の統合 | Microsoft Docs
description: Azure Active Directory と ServiceNow の間のシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0efc69aa7fb081303df6fc6327253fd5b8336999
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898603"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>チュートリアル:Azure Active Directory と ServiceNow の統合

このチュートリアルでは、ServiceNow と Azure Active Directory (Azure AD) を統合する方法について説明します。
ServiceNow と Azure AD の統合には、次の利点があります。

* ServiceNow にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して ServiceNow に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

ServiceNow と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* ServiceNow でのシングル サインオンが有効なサブスクリプション
* ServiceNow の場合は、Calgary バージョン以降の ServiceNow のインスタンスまたはテナント
* ServiceNow Express の場合は、Helsinki バージョン以降の ServiceNow Express のインスタンス
* ServiceNow のテナントで [Multiple Provider Single Sign On プラグイン](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)が有効になっている必要があります。 このプラグインを有効にするには、[サービス要求を送信してください](https://hi.service-now.com)。
* 自動構成のために、ServiceNow の Multi-Provider プラグインを有効にします。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ServiceNow では、**SP** によって開始される SSO がサポートされます

* ServiceNow では、[**自動化された**ユーザー プロビジョニング](servicenow-provisioning-tutorial.md)がサポートされます

## <a name="adding-servicenow-from-the-gallery"></a>ギャラリーからの ServiceNow の追加

Azure AD への ServiceNow の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ServiceNow を追加する必要があります。

**ギャラリーから ServiceNow を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**ServiceNow**」と入力し、結果ウィンドウで **[ServiceNow]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の ServiceNow](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、ServiceNow で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと ServiceNow 内の関連ユーザー間にリンク関係が確立されている必要があります。

ServiceNow で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[ServiceNow 向け Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on-for-servicenow)** - ユーザーがこの機能を使用できるようにします。
2. **[ServiceNow シングル サインオンの構成](#configure-servicenow-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[ServiceNow Express 向け Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on-for-servicenow-express)** - ユーザーがこの機能を使用できるようにします。
4. **[ServiceNow Express シングル サインオンの構成](#configure-servicenow-express-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
5. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
6. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
7. **[ServiceNow テスト ユーザーの作成](#create-servicenow-test-user)** - ServiceNow で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
8. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>ServiceNow 向け Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

ServiceNow で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **ServiceNow** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[ServiceNow のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<instance-name>.service-now.com/navpage.do`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<instance-name>.service-now.com`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、このチュートリアルの後半で説明する実際のサインオン URL と識別子に更新する必要があります。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

    a. コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーし、メモ帳に貼り付けます。このアプリのフェデレーション メタデータ URL はこのチュートリアルで後で使用されます。

    b. **[ダウンロード]** をクリックして **[証明書 (Base64)]** をダウンロードして、コンピューターに証明書ファイルを保存します。

6. **[ServiceNow のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-servicenow-single-sign-on"></a>ServiceNow のシングル サインオンの構成

1. ServiceNow アプリケーションに管理者としてサインオンします。

2. **[Integration - Multiple Provider Single Sign-On Installer (統合 - 複数プロバイダーのシングル サインオン インストーラー)]** プラグインをアクティブ化するには、次の手順に従います。

    a. 左側のナビゲーション ウィンドウの検索バーで **}[System Definition]\(システム定義\)** セクションを検索し、**[Plugins]\(プラグイン\)** をクリックします。

    ![プラグインをアクティブにする](./media/servicenow-tutorial/tutorial_servicenow_03.png "プラグインをアクティブにする")

    b. "**Integration - Multiple Provider Single Sign-On Installer**" を検索します。

     ![プラグインをアクティブにする](./media/servicenow-tutorial/tutorial_servicenow_04.png "プラグインをアクティブにする")

    c. プラグインを選択します。 右クリックして **[Activate/Upgrade]** を選択します。

     ![プラグインをアクティブにする](./media/servicenow-tutorial/tutorial_activate.png "プラグインをアクティブにする")

    d. **[Activate (アクティブ化)]** ボタンをクリックします。

     ![プラグインをアクティブにする](./media/servicenow-tutorial/tutorial_activate1.png "プラグインをアクティブにする")

3. 左側のナビゲーション ウィンドウの検索バーで **[Multi-Provider SSO]** セクションを検索し、**[Properties]** をクリックします。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/tutorial_servicenow_06.png "Configure app URL")

4. **[Multiple Provider SSO Properties]** ダイアログで、次の手順を実行します。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/ic7694981.png "Configure app URL")

    * **[Enable multiple provider SSO]** で **[Yes]** を選択します。
  
    * **[Enable Auto Importing of users from all identity providers into the user table]** で、**[Yes]** を選択します。

    * **[Enable debug logging for the multiple provider SSO integration]** で **[Yes]** を選択します。

    * **[The field on the user table that...]** ボックスに、「**user_name**」と入力します。
  
    * **[Save]** をクリックします。

5. **ServiceNow** は自動と手動の 2 つの方法で構成できます。

6. **ServiceNow** を自動的に構成するには、以下の手順に従います。

    * Azure Portal で **ServiceNow** の [シングル サインオン] ページに戻ります。

    * SAML ベースの認証に対応するため、Azure AD による ServiceNow の自動構成を行う ServiceNow 用のワン クリック構成サービスが提供されています。 このサービスを有効にするには、**[ServiceNow 構成]** セクションに移動し、**[ServiceNow を構成する]** をクリックして [サインオンの構成] ウィンドウを開きます。

        ![Configure single sign-on](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * ServiceNow のインスタンス名、管理者のユーザー名、および管理者パスワードを **[サインオンの構成]** フォームに入力し、**[今すぐ構成]** をクリックします。 ここで指定する管理者のユーザー名には、ServiceNow で **security_admin** ロールが割り当てられている必要があります。 これとは別に、ServiceNow を手動で構成して Azure AD を SAML ID プロバイダーとして使用させる方法があります。**[シングル サインオンを手動で構成する]** をクリックして、[クイック リファレンス] セクションから**サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** をコピーします。

        ![アプリケーション URL の構成](./media/servicenow-tutorial/configure.png "Configure app URL")

    * ServiceNow アプリケーションに管理者としてサインオンします。

    * 自動構成では、必要なすべての設定が **ServiceNow** 側で構成されますが、**[X.509 証明書]** は既定で無効です。 ServiceNow でご使用の ID プロバイダーに手動でマップする必要があります。 同様に次の手順に従ってください。

    * 左側のナビゲーション ウィンドウの検索バーで **[Multi-Provider SSO]** セクションを検索し、**[ID プロバイダー]** をクリックします。

        ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configure single sign-on")

    * 自動的に生成された ID プロバイダーをクリックします

        ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_08.png "Configure single sign-on")

    *  **[ID プロバイダー]** セクションで、次の手順に従います。

        ![シングル サインオンの構成](./media/servicenow-tutorial/automatic_config.png "Configure single sign-on")

        * **[Name]** ボックスに、構成の名前を入力します (例: **Microsoft Azure フェデレーション シングル サインオン**)。

        * 入力されている **ID プロバイダーの SingleLogoutRequest** の値をテキスト ボックスから削除してください。

        * **[ServiceNow Homepage]** の値をコピーして、Azure Portal の **[ServiceNow のドメインと URL]** セクションにある **[サインオン URL]** ボックスに貼り付けます。

            > [!NOTE]
            > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

        * **[Entity ID / Issuer]** の値をコピーして、Azure Portal の **[ServiceNow のドメインと URL]** セクションにある **[識別子]** ボックスに貼り付けます。

        * **[NameID Policy]** の値は必ず `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` に設定してください。 

    * **[X.509 証明書]** セクションまで下にスクロールし、**[編集]** を選択します。

        ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_09.png "Configure single sign-on")

    * 証明書を選択し、右矢印のアイコンをクリックして証明書を追加します。

        ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_11.png "Configure single sign-on")

    * **[Save]** をクリックします。

    * ページの右上隅にある **[接続をテスト]** をクリックします。

        ![プラグインをアクティブにする](./media/servicenow-tutorial/tutorial_activate2.png "プラグインをアクティブにする")

    * **[接続のテスト]** をクリックすると、資格情報を入力する必要があるポップアップ ウィンドウが表示され、下のページに結果が表示されます。 **SSO ログアウト テスト結果** エラーが予期されます。このエラーは無視して、**[アクティブ化]** ボタンをクリックしてください。

        ![シングル サインオンの構成](./media/servicenow-tutorial/servicenowactivate.png "Configure single sign-on")
  
7. **ServiceNow** を手動で構成するには、以下の手順に従います。

    * ServiceNow アプリケーションに管理者としてサインオンします。

    * 左側のナビゲーション ウィンドウで、 **[Identity Providers]** をクリックします。

        ![シングル サインオンの構成](./media/servicenow-tutorial/tutorial_servicenow_07.png "Configure single sign-on")

    * **[Identity Providers]\(ID プロバイダー\)** ダイアログ ボックスで、**[New]\(新規\)** をクリックします。

        ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694977.png "Configure single sign-on")

    * **[ID プロバイダー]** ダイアログ ボックスで、**[SAML]** をクリックします。

        ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694978.png "Configure single sign-on")

    * **ID プロバイダーのメタデータのインポート** ポップアップで、次の手順に従います。

        ![シングル サインオンの構成](./media/servicenow-tutorial/idp.png "Configure single sign-on")

        * Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** を貼り付けます。

        * **[インポート]** をクリックします。

    * IdP メタデータ URL が読み取られ、すべてのフィールド情報が設定されます。

        ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694982.png "Configure single sign-on")

        * **[Name]** ボックスに、構成の名前を入力します (例: **Microsoft Azure フェデレーション シングル サインオン**)。

        * 入力されている **ID プロバイダーの SingleLogoutRequest** の値をテキスト ボックスから削除してください。

        * **[ServiceNow Homepage]** の値をコピーして、Azure Portal の **[ServiceNow のドメインと URL]** セクションにある **[サインオン URL]** ボックスに貼り付けます。

            > [!NOTE]
            > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

        * **[Entity ID / Issuer]** の値をコピーして、Azure Portal の **[ServiceNow のドメインと URL]** セクションにある **[識別子]** ボックスに貼り付けます。

        * **[NameID Policy]** の値は必ず `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` に設定してください。

        * **[詳細設定]** をクリックします。 ServiceNow のデプロイでユーザーを一意に識別するためのフィールドに応じて、**[User Field]** ボックスに「**email**」または「**user_id**」と入力します。

            > [!NOTE]
            > SAML トークンの一意の識別子として Azure AD ユーザー ID (ユーザーのプリンシパル名) か電子メール アドレスを出力するように Azure AD を構成できます。そのためには、Azure Portal で **[ServiceNow]、[属性]、[シングル サインオン]** セクションの順に移動し、目的のフィールドを **nameidentifier** 属性にマッピングします。 Azure AD に格納される選択した属性 (ユーザー プリンシパル名など) の値と、ServiceNow に格納される入力したフィールド (user_name など) の値が一致している必要があります。

        * ページの右上隅にある **[接続をテスト]** をクリックします。

        * **[接続のテスト]** をクリックすると、資格情報を入力する必要があるポップアップ ウィンドウが表示され、下のページに結果が表示されます。 **SSO ログアウト テスト結果** エラーが予期されます。このエラーは無視して、**[アクティブ化]** ボタンをクリックしてください。

          ![シングル サインオンの構成](./media/servicenow-tutorial/servicenowactivate.png "Configure single sign-on")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>ServiceNow Express 向け Azure AD シングル サインオンの構成

1. [Azure portal](https://portal.azure.com/) の **ServiceNow** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[ServiceNow のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<instance-name>.service-now.com/navpage.do`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<instance-name>.service-now.com`

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、このチュートリアルの後半で説明する実際のサインオン URL と識別子に更新する必要があります。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. SAML ベースの認証に対応するため、Azure AD による ServiceNow の自動構成を行う ServiceNow 用のワン クリック構成サービスが提供されています。 このサービスを有効にするには、**ServiceNow の設定** セクションで、**[ステップ バイ ステップの手順を表示]** をクリックして [サインオンの構成] ウィンドウを開きます。

    ![Configure single sign-on](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. ServiceNow のインスタンス名、管理者のユーザー名、および管理者パスワードを **[サインオンの構成]** フォームに入力し、**[今すぐ構成]** をクリックします。 ここで指定する管理者のユーザー名には、ServiceNow で **security_admin** ロールが割り当てられている必要があります。 これとは別に、ServiceNow を手動で構成して Azure AD を SAML ID プロバイダーとして使用させる方法があります。**[シングル サインオンを手動で構成する]** をクリックして、[クイック リファレンス] セクションから**サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** をコピーします。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/configure.png "Configure app URL")

### <a name="configure-servicenow-express-single-sign-on"></a>ServiceNow Express のシングル サインオンの構成

1. ServiceNow Express アプリケーションに管理者としてサインオンします。

2. 左側にあるナビゲーション ウィンドウで、**[Single Sign-On (シングル サインオン)]** をクリックします。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/ic7694980ex.png "Configure app URL")

3. **[Single Sign-On (シングル サインオン)]** ダイアログで右上の構成アイコンをクリックし、次のプロパティを設定します。

    ![アプリケーション URL の構成](./media/servicenow-tutorial/ic7694981ex.png "Configure app URL")

    a. **[Enable multiple provider SSO (複数プロバイダー SSO を有効にする)]** を右に切り替えます。

    b. **[Enable debug logging for the multiple provider SSO integration (複数プロバイダー SSO 統合でのデバッグ ログの有効化)]** を右に切り替えます。

    c. **[The field on the user table that...]** ボックスに、「**user_name**」と入力します。

4. **[Single Sign-On (シングル サインオン)]** ダイアログで、**[Add New Certificate (新しい証明書の追加)]** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694973ex.png "Configure single sign-on")

5. **[X.509 Certificates]** ダイアログで、次の手順を実行します。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694975.png "Configure single sign-on")

    a. **[Name]\(名前\)** ボックスに、構成の名前を入力します (例:**TestSAML2.0**)。

    b. **[アクティブ]** を選択します。

    c. **[Format]** で **[PEM]** を選択します。

    d. **[Type]** で **[Trust Store Cert]** を選択します。

    e. Azure Portal からダウンロードした Base64 エンコードの証明書をメモ帳で開き、その内容をクリップボードにコピーして、**[PEM Certificate]** ボックスに貼り付けます。

    f. **[Update]\(更新\)** をクリックします。

6. **[Single Sign-On (シングル サインオン)]** ダイアログで、**[Add New IdP (新しい IdP の追加)]** をクリックします。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694976ex.png "Configure single sign-on")

7. **[Add New Identity Provider (新しい ID プロバイダーの追加)]** ダイアログの **[Configure Identity Provider (ID プロバイダーの構成)]** で、次の手順に従います。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694982ex.png "Configure single sign-on")

    a. **[Name]\(名前\)** ボックスに、構成の名前を入力します (例:**SAML 2.0**)。

    b. **[Identity Provider URL]\(ID プロバイダー URL\)** フィールドに、Azure Portal からコピーした **ID プロバイダー ID** の値を貼り付けます。

    c. **[Identity Provider's AuthnRequest]\(ID プロバイダーの AuthnRequest\)** フィールドに、Azure Portal からコピーした**認証要求 URL** の値を貼り付けます。

    d. **[Identity Provider's SingleLogoutRequest]\(ID プロバイダーの SingleLogoutRequest\)** フィールドに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます

    e. **[Identity Provider Certificate (ID プロバイダー証明書)]** で、前の手順で作成した証明書を選択します。

8. **[Advanced Settings (詳細設定)]** をクリックし、**[Additional Identity Provider Properties (追加の ID プロバイダーのプロパティ)]** で、次の手順に従います。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694983ex.png "Configure single sign-on")

    a. **[Protocol Binding for the IDP's SingleLogoutRequest]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**」と入力します。

    b. **[NameID Policy (NameID ポリシー)]** ボックスに「**urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**」と入力します。

    c. **[AuthnContextClassRef Method (AuthnContextClassRef メソッド)]** に「`http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`」と入力します。

    d. **[Create an AuthnContextClass]** をオフにします。

9. **[Additional Service Provider Properties (追加のサービス プロバイダーのプロパティ)]** で、次の手順に従います。

    ![シングル サインオンの構成](./media/servicenow-tutorial/ic7694984ex.png "Configure single sign-on")

    a. **[ServiceNow Homepage]** ボックスに ServiceNow インスタンス ホームページの URL を入力します。

    > [!NOTE]
    > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

    b. **[Entity ID / Issuer]** ボックスに、ServiceNow テナントの URL を入力します。

    c. **[Audience URI (対象ユーザー URI)]** ボックスに、ServiceNow テナントの URL を入力します。

    d. **[Clock Skew]** ボックスに「**60**」と入力します。

    e. ServiceNow のデプロイでユーザーを一意に識別するためのフィールドに応じて、**[User Field]** ボックスに「**email**」または「**user_id**」と入力します。

    > [!NOTE]
    > SAML トークンの一意の識別子として Azure AD ユーザー ID (ユーザーのプリンシパル名) か電子メール アドレスを出力するように Azure AD を構成できます。そのためには、Azure Portal で **[ServiceNow]、[属性]、[シングル サインオン]** セクションの順に移動し、目的のフィールドを **nameidentifier** 属性にマッピングします。 Azure AD に格納される選択した属性 (ユーザー プリンシパル名など) の値と、ServiceNow に格納される入力したフィールド (user_name など) の値が一致している必要があります。

    f. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ServiceNow へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択してから、**[ServiceNow]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[ServiceNow]** を選択します。

    ![アプリケーションの一覧の ServiceNow リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-servicenow-test-user"></a>ServiceNow テスト ユーザーの作成

このセクションの目的は、ServiceNow で Britta Simon というユーザーを作成することです。 ServiceNow では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](servicenow-provisioning-tutorial.md)を参照してください。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合、[ServiceNow クライアント サポート チーム](https://www.servicenow.com/support/contact-support.html)に問い合わせる必要があります

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ServiceNow] タイルをクリックすると、SSO を設定した ServiceNow に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [[ユーザー プロビジョニングの構成]](servicenow-provisioning-tutorial.md)
