---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と ServiceNow の統合 | Microsoft Docs
description: Azure Active Directory と ServiceNow の間のシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: c90234249f3cf7eb6ed4793110d61e1f8190ed60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99092635"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と ServiceNow の統合

このチュートリアルでは、ServiceNow と Azure Active Directory (Azure AD) を統合する方法について説明します。 ServiceNow を Azure AD を統合すると、次のことができます。

* ServiceNow にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して ServiceNow に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Jao6]

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* ServiceNow でのシングル サインオン (SSO) が有効なサブスクリプション。
* ServiceNow では、Calgary\(カルガリー\)、Kingston\(キングストン\)、London\(ロンドン\)、Madrid\(マドリッド\)、New York\(ニューヨーク\)、Orlando\(オーランド\)、Paris\(パリ\) バージョン以降が ServiceNow のインスタンスまたはテナントでサポートされています。
* ServiceNow Express の場合は、Helsinki バージョン以降の ServiceNow Express のインスタンス。
* ServiceNow のテナントで [Multiple Provider Single Sign On プラグイン](https://old.wiki/index.php/Multiple_Provider_Single_Sign-On#gsc.tab=0)が有効になっている必要があります。
* 自動構成のために、ServiceNow の Multi-Provider プラグインを有効にします。
* ServiceNow Classic (モバイル) アプリケーションをインストールするには、適切なストアに移動して ServiceNow Classic アプリケーションを検索します。 その後、ダウンロードします。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 

* ServiceNow では、**SP** Initiated SSO がサポートされます。

* ServiceNow では、[自動化されたユーザー プロビジョニング](servicenow-provisioning-tutorial.md)がサポートされます。

* ServiceNow Classic (モバイル) アプリケーションを Azure AD と共に構成して SSO を有効にできます。 Android と iOS の両方のユーザーがサポートされます。 このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

## <a name="add-servicenow-from-the-gallery"></a>ギャラリーからの ServiceNow の追加

Azure AD への ServiceNow の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ServiceNow を追加する必要があります。

1. 職場または学校アカウントを使用するか、個人用 Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のウィンドウで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**ServiceNow**」と入力します。
1. 結果ウィンドウで **[ServiceNow]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-servicenow"></a>ServiceNow の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、ServiceNow に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと ServiceNow の関連ユーザーの間で、リンク関係が確立されている必要があります。

ServiceNow に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
    1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
    1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
    1. [ServiceNow Express 向けに Azure AD SSO を構成](#configure-azure-ad-sso-for-servicenow-express)して、ユーザーがこの機能を使用できるようにします。
2. [ServiceNow を構成](#configure-servicenow)して、アプリケーション側で SSO 設定を構成します。
    1. [ServiceNow テスト ユーザーを作成](#create-servicenow-test-user)して、ServiceNow 内で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
    1. [ServiceNow Express の SSO を構成](#configure-servicenow-express-sso)して、アプリケーション側でシングル サインオン設定を構成します。  
3. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。
4. [ServiceNow Classic (モバイル) の SSO をテスト](#test-sso-for-servicenow-classic-mobile)して、構成が機能するかどうかを検証します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **ServiceNow** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** のペン アイコンを選択して設定を編集します。

   ![アイコンが強調表示された [SAML でシングル サインオンをセットアップします] ページのスクリーンショット](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** に、次のパターンを使用して URL を入力します。`https://<instancename>.service-now.com/navpage.do`

    b. **[識別子 (エンティティ ID)]** に、次のパターンを使用して URL を入力します。`https://<instance-name>.service-now.com`

    c. **[応答 URL]** に、次の URL パターンのいずれかを入力します。

    | [応答 URL]|
    |----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` | 

    d. **[ログアウト URL]** に、`https://<instancename>.service-now.com/navpage.do`のパターンを使用する URL を入力します。

    > [!NOTE]
    > 識別子の値に "/" が追加されている場合は、手動で削除してください。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、このチュートリアルの後半で説明する実際のサインオン URL、応答 URL、ログアウト URL と識別子に更新する必要があります。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけます。 

   ![[ダウンロード] が強調表示された [SAML 署名証明書] セクションのスクリーンショット](common/certificatebase64.png)

   a. コピー ボタンを選択して **アプリのフェデレーション メタデータ URL** をコピーし、メモ帳に貼り付けます。 この URL は、このチュートリアルの後半で使用します。

    b. **[ダウンロード]** を選択して **証明書 (Base64)** をダウンロードして、ご利用のコンピューターに証明書ファイルを保存します。

1. **[ServiceNow のセットアップ]** セクションで、実際の要件に基づいて適切な URL をコピーします。

   ![URL が強調表示された [ServiceNow のセットアップ] セクションのスクリーンショット](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **名前** には、`B.Simon`を入力します。  
   1. **[ユーザー名]** に「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** を選択し、 **[パスワード]** ボックス内に表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に ServiceNow へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[ServiceNow]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択し、 **[選択]** を選択します。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>ServiceNow Express 向け Azure AD SSO の構成

1. [Azure portal](https://portal.azure.com/) の **ServiceNow** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] が強調表示された ServiceNow アプリケーション統合ページのスクリーンショット](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![SAML が強調表示された [シングル サインオン方式の選択] のスクリーンショット](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、ペン アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![ペン アイコンが強調表示された [SAML でシングル サインオンをセットアップします] ページのスクリーンショット](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** に、次のパターンを使用して URL を入力します。`https://<instancename>.service-now.com/navpage.do`

    b. **[識別子 (エンティティ ID)]** に、次のパターンを使用して URL を入力します。`https://<instance-name>.service-now.com`

    c. **[応答 URL]** に、次のいずれかの URL を入力します。

    | [応答 URL] |
    |-----------|
    | `https://<instancename>.service-now.com/navpage.do` |
    | `https://<instancename>.service-now.com/customer.do` |

    d. **[ログアウト URL]** に、`https://<instancename>.service-now.com/navpage.do`のパターンを使用する URL を入力します。
    
    > [!NOTE]
    > 識別子の値に "/" が追加されている場合は、手動で削除してください。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、このチュートリアルの後半で説明する実際のサインオン URL、応答 URL、ログアウト URL と識別子に更新する必要があります。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで **[ダウンロード]** を選択して、実際の要件に適した特定のオプションの **証明書 (Base64)** をダウンロードします。 それを自分のコンピューターに保存します。

    ![[ダウンロード] が強調表示された [SAML 署名証明書] セクションのスクリーンショット](common/certificatebase64.png)

6. Azure AD では、SAML ベースの認証に対応するように ServiceNow を自動的に構成できます。 このサービスを有効にするには、 **[ServiceNow のセットアップ]** セクションで、 **[ステップ バイ ステップの手順を表示]** を選択して **[サインオンの構成]** ウィンドウを開きます。

    ![[ステップ バイ ステップの手順を表示] が強調表示された [ServiceNow のセットアップ] セクションのスクリーンショット](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

7. **[サインオンの構成]** フォームに、実際の ServiceNow のインスタンス名、管理者のユーザー名、および管理者パスワードを入力します。 **[今すぐ構成]** を選択します。 この機能を使用するためには、指定する管理者のユーザー名に、ServiceNow で **security_admin** ロールが割り当てられている必要があります。 これとは別に、Azure AD を SAML ID プロバイダーとして使用するよう ServiceNow を手動で構成するには、 **[シングル サインオンを手動で構成する]** を選択します。 [クイック リファレンス] セクションから **ログアウト URL、Azure AD 識別子、およびログイン URL** をコピーします。

    ![[今すぐ構成] が強調表示された [サインオンの構成] フォームのスクリーンショット](./media/servicenow-tutorial/configure.png "アプリケーション URL の構成")

## <a name="configure-servicenow"></a>ServiceNow の構成

1. ServiceNow アプリケーションに管理者としてサインオンします。

1. これらの手順に従って、**Integration - Multiple Provider single sign-on Installer (統合 - 複数プロバイダーのシングル サインオン インストーラー)** プラグインをアクティブにします。

    a. 左側のウィンドウの検索ボックスで **[System Definition]\(システム定義\)** セクションを検索し、 **[Plugins]\(プラグイン\)** を選択します。

    ![[System Definition]\(システム定義\) と [Plugins]\(プラグイン\) が強調表示された [System Definition]\(システム定義\) セクションのスクリーンショット](./media/servicenow-tutorial/tutorial-servicenow-03.png "プラグインのアクティブ化")

    b. "**Integration - Multiple Provider Single Sign-On Installer**" を検索します。

     ![[Integration - Multiple Provider single sign-on Installer]\(統合 - 複数プロバイダーのシングル サインオン インストーラー\) が強調表示された [System Plugins]\(システム プラグイン\) ページのスクリーンショット](./media/servicenow-tutorial/tutorial-servicenow-04.png "プラグインのアクティブ化")

    c. プラグインを選択します。 右クリックして **[Activate/Upgrade]\(アクティブ化/アップグレード\)** を選択します。

     ![[Activate/Upgrade]\(アクティブ化/アップグレード\) が強調表示されたプラグインの右クリック メニューのスクリーンショット](./media/servicenow-tutorial/tutorial-activate.png "プラグインのアクティブ化")

    d. **[アクティブ化]** を選びます。

     ![[Activate]\(アクティブ化\) が強調表示された [Activate Plugin]\(プラグインのアクティブ化\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/tutorial-activate-1.png "プラグインのアクティブ化")

1. 左側のウィンドウの検索バーで **[Multi-Provider SSO]\(マルチプロバイダー SSO\)** セクションを検索し、 **[Properties]\(プロパティ\)** を選択します。

    ![[Multi-Provider SSO]\(マルチプロバイダー SSO\) と [Properties]\(プロパティ\) が強調表示された [Multi-Provider SSO]\(マルチプロバイダー SSO\) セクションのスクリーンショット](./media/servicenow-tutorial/tutorial-servicenow-06.png "アプリケーション URL の構成")

1. **[Multiple Provider SSO Properties]\(複数プロバイダー SSO のプロパティ\)** ダイアログ ボックスで、次の手順を実行します。

    ![[Multiple Provider SSO Properties]\(複数プロバイダー SSO のプロパティ\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/ic7694981.png "アプリケーション URL の構成")

    * **[Enable multiple provider SSO]\(複数プロバイダー SSO を有効にする\)** で **[Yes]\(はい\)** を選択します。
  
    * **[Enable Auto Importing of users from all identity providers into the user table]\(すべての ID プロバイダーからユーザー テーブルへのユーザーの自動インポートを有効にする\)** で、 **[Yes]\(はい\)** を選択します。

    * **[Enable debug logging for the multiple provider SSO integration]\(複数プロバイダー SSO 統合のデバッグ ログを有効にする\)** で **[Yes]\(はい\)** を選択します。

    * **[The field on the user table that...]\(ユーザー テーブルのフィールド...\)** に、「**email**」と入力します。
  
    * **[保存]** を選択します。

1. ServiceNow の構成は、自動または手動で行うことができます。 ServiceNow を自動的に構成するには、これらの手順に従います。

    1. Azure portal で **ServiceNow** の [シングル サインオン] ページに戻ります。

    1. ServiceNow のワンクリック構成サービスが提供されています。 このサービスを有効にするには、 **[ServiceNow 構成]** セクションに移動し、 **[ServiceNow を構成する]** を選択して **[サインオンの構成]** ウィンドウを開きます。

        ![[ステップ バイ ステップの手順を表示] が強調表示された [ServiceNow のセットアップ] のスクリーンショット](./media/servicenow-tutorial/tutorial-servicenow-configure.png)

    1. **[サインオンの構成]** フォームに、実際の ServiceNow のインスタンス名、管理者のユーザー名、および管理者パスワードを入力します。 **[今すぐ構成]** を選択します。 この機能を使用するには、指定する管理者のユーザー名に、ServiceNow で **security_admin** ロールが割り当てられている必要があります。 これとは別に、Azure AD を SAML ID プロバイダーとして使用するよう ServiceNow を手動で構成するには、 **[シングル サインオンを手動で構成する]** を選択します。 [クイック リファレンス] セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

        ![[今すぐ構成] が強調表示された [サインオンの構成] フォームのスクリーンショット](./media/servicenow-tutorial/configure.png "アプリケーション URL の構成")

    1. ServiceNow アプリケーションに管理者としてサインオンします。

       * 自動構成では、必要なすべての設定は **ServiceNow** 側で構成されますが、**X.509 証明書** は既定で有効になっておらず、 **[Single Sign-On Script]\(シングル サインオン スクリプト\)** 値は **MultiSSOv2_SAML2_custom** に指定されています。 ServiceNow でご使用の ID プロバイダーに手動でマップする必要があります。 次の手順に従います。

         1. 左側のウィンドウの検索ボックスで **[Multi-Provider SSO]\(マルチプロバイダー SSO\)** セクションを検索し、 **[Identity Providers]\(ID プロバイダー\)** を選択します。

            ![[Identity Providers]\(ID プロバイダー\) が強調表示された [Multi-Provider SSO]\(マルチプロバイダー SSO\) セクションのスクリーンショット](./media/servicenow-tutorial/tutorial-servicenow-07.png "Configure single sign-on")

         1. 自動的に生成された ID プロバイダーを選択します。

            ![自動的に生成された ID プロバイダーが強調表示された ID プロバイダーのスクリーンショット](./media/servicenow-tutorial/tutorial-servicenow-08.png "Configure single sign-on")

         1.  **[ID プロバイダー]** セクションで、次の手順に従います。

             ![[Identity Provider]\(ID プロバイダー\) セクションのスクリーンショット](./media/servicenow-tutorial/automatic-config.png "Configure single sign-on")

               a. **[Name]\(名前\)** に、自分の構成の名前を入力します (例: **Microsoft Azure フェデレーション シングル サインオン**)。

               b. **[ServiceNow Homepage]\(ServiceNow のホーム ページ\)** の値をコピーして、Azure portal の **ServiceNow の [基本的な SAML 構成]** セクションにある **[サインオン URL]** に貼り付けます。

                > [!NOTE]
                > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

              c. **[Entity ID / Issuer]\(エンティティ ID/発行者\)** の値をコピーして、Azure portal の **ServiceNow の [基本的な SAML 構成]** セクションにある **[識別子]** に貼り付けます。

              d. **[NameID Policy]\(NameID ポリシー\)** が `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 値に設定されていることを確認します。 

              e. **[Advanced]\(詳細\)** をクリックし、 **[Single Sign-On Script]\(シングル サインオン スクリプト\)** 値を **MultiSSOv2_SAML2_custom** に指定します。

         1. **[X.509 Certificate]\(X.509 証明書\)** セクションまで下へスクロールし、 **[Edit]\(編集\)** を選択します。

             ![[Edit]\(編集\) が強調表示された [X.509 Certificate]\(X.509 証明書\) セクションのスクリーンショット](./media/servicenow-tutorial/tutorial-servicenow-09.png "Configure single sign-on")

         1. 証明書を選択し、右矢印のアイコンを選択して証明書を追加します。

            ![証明書と右矢印のアイコンが強調表示された [Collection]\(コレクション\) のスクリーンショット](./media/servicenow-tutorial/tutorial-servicenow-11.png "Configure single sign-on")

          1. **[保存]** を選択します。

          1. ページの右上隅にある **[Test Connection]\(テスト接続\)** を選択します。

             ![[Test Connection]\(テスト接続\) が強調表示されたページのスクリーンショット](./media/servicenow-tutorial/tutorial-activate-2.png "プラグインのアクティブ化")

             > [!NOTE]
             > テスト接続が失敗し、この接続をアクティブにできない場合は、ServiceNow によってオーバーライド スイッチが提供されます。 **Search Navigation (検索ナビゲーション)** に「**Sys_properties.LIST**」と入力する必要があります。そうすると、[System Properties]\(システム プロパティ\) の新しいページが開きます。 ここでは、**glide.authenticate.multisso.test.connection.mandatory** という名前の新しいプロパティを作成する必要があります。これには、**データ型** を **True/False** とし、**値** を **False** として設定します。

             > ![[Test connection]\(テスト接続\) ページのスクリーンショット](./media/servicenow-tutorial/test-connection-fail.png "Configure single sign-on")
        
          1. 自分の資格情報の入力を求められたら、入力します。 次のページが表示されます。 **SSO Logout Test Results (SSO ログアウト テスト結果)** エラーが予期されます。 このエラーは無視して、 **[Activate]\(アクティブ化\)** を選択します。

             ![資格情報ページのスクリーンショット。](./media/servicenow-tutorial/servicenow-activate.png "Configure single sign-on")
  
1. **ServiceNow** を手動で構成するには、これらの手順に従います。

    1. ServiceNow アプリケーションに管理者としてサインオンします。

    1. 左側のウィンドウで、 **[Identity Providers]\(ID プロバイダー\)** を選択します。

        ![[Identity Providers]\(ID プロバイダー\) が強調表示された [Multi-Provider SSO]\(マルチプロバイダー SSO\) のスクリーンショット](./media/servicenow-tutorial/tutorial-servicenow-07.png "Configure single sign-on")

    1. **[Identity Providers]\(ID プロバイダー\)** ダイアログ ボックスで、 **[New]\(新規\)** を選択します。

        ![[New]\(新規\) が強調表示された [Identity Providers]\(ID プロバイダー\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/ic7694977.png "Configure single sign-on")

    1. **[Identity Providers]\(ID プロバイダー\)** ダイアログ ボックスで、 **[SAML]** を選択します。

        ![[SAML] が強調表示された [Identity Providers]\(ID プロバイダー\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/ic7694978.png "Configure single sign-on")

    1. **[Import Identity Provider Metadata]\(ID プロバイダーのメタデータのインポート\)** で、次の手順に従います。

        ![[URL] と [Import]\(インポート\) が強調表示された [Import Identity Provider Metadata]\(ID プロバイダーのメタデータのインポート\) のスクリーンショット](./media/servicenow-tutorial/idp.png "Configure single sign-on")

        1. Azure portal からコピーした **アプリのフェデレーション メタデータ URL** を貼り付けます。

        1. **[インポート]** を選択します。

    1. IdP メタデータ URL が読み取られ、すべてのフィールド情報が設定されます。

        ![[Identity Provider]\(ID プロバイダー\) のスクリーンショット](./media/servicenow-tutorial/ic7694982.png "Configure single sign-on")

        a. **[Name]\(名前\)** に、自分の構成の名前を入力します (例: **Microsoft Azure フェデレーション シングル サインオン**)。

        b. **[ServiceNow Homepage]\(ServiceNow のホーム ページ\)** の値をコピーします。 Azure portal の **ServiceNow の [基本的な SAML 構成]** セクションにある **[サインオン URL]** に貼り付けます。

        > [!NOTE]
        > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

        c. **[Entity ID / Issuer]\(エンティティ ID/発行者\)** の値をコピーします。 Azure portal の **ServiceNow の [基本的な SAML 構成]** セクションにある **[識別子]** に貼り付けます。

        d. **[NameID Policy]\(NameID ポリシー\)** が `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 値に設定されていることを確認します。

        e. **[Advanced] \(詳細設定)** を選択します。 **[User Field]\(ユーザー フィールド\)** に「**email**」と入力します。

        > [!NOTE]
        > SAML トークンの一意の識別子として Azure AD ユーザー ID (ユーザーのプリンシパル名) かメール アドレスを出力するように Azure AD を構成できます。 そのためには、Azure portal で **[ServiceNow]**  >  **[属性]**  >  **[シングル サインオン]** セクションの順に移動し、目的のフィールドを **nameidentifier** 属性にマッピングします。 Azure AD に格納される選択した属性 (ユーザー プリンシパル名など) の値と、ServiceNow に格納される入力したフィールド (user_name など) の値が一致している必要があります。

        g. ページの右上隅にある **[Test Connection]\(テスト接続\)** を選択します。

        > [!NOTE]
        > テスト接続が失敗し、この接続をアクティブにできない場合は、ServiceNow によってオーバーライド スイッチが提供されます。 **Search Navigation (検索ナビゲーション)** に「**Sys_properties.LIST**」と入力する必要があります。そうすると、[System Properties]\(システム プロパティ\) の新しいページが開きます。 ここでは、**glide.authenticate.multisso.test.connection.mandatory** という名前の新しいプロパティを作成する必要があります。これには、**データ型** を **True/False** とし、**値** を **False** として設定します。

          > ![テスト接続のスクリーンショット](./media/servicenow-tutorial/test-connection-fail.png "Configure single sign-on")

        h. 自分の資格情報の入力を求められたら、入力します。 次のページが表示されます。 **SSO Logout Test Results (SSO ログアウト テスト結果)** エラーが予期されます。 このエラーは無視して、 **[Activate]\(アクティブ化\)** を選択します。

          ![credentials](./media/servicenow-tutorial/servicenow-activate.png "Configure single sign-on")

### <a name="create-servicenow-test-user"></a>ServiceNow テスト ユーザーの作成

このセクションの目的は、ServiceNow 内で B.Simon というユーザーを作成することです。 ServiceNow では、自動ユーザー プロビジョニングがサポートされており、既定で有効になっています。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[ServiceNow クライアント サポート チーム](https://www.servicenow.com/support/contact-support.html)に問い合わせてください。

### <a name="configure-servicenow-express-sso"></a>ServiceNow Express SSO の構成

1. ServiceNow Express アプリケーションに管理者としてサインオンします。

2. 左側のウィンドウで、 **[Single Sign-On]\(シングル サインオン\)** を選択します。

    ![[Single Sign-On]\(シングル サインオン\) が強調表示された ServiceNow Express アプリケーションのスクリーンショット](./media/servicenow-tutorial/ic7694980ex.png "アプリケーション URL の構成")

3. **[Single Sign-On]\(シングル サインオン\)** ダイアログ ボックスで右上の構成アイコンを選択し、次のプロパティを設定します。

    ![[Single Sign-On]\(シングル サインオン\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/ic7694981ex.png "アプリケーション URL の構成")

    a. **[Enable multiple provider SSO (複数プロバイダー SSO を有効にする)]** を右に切り替えます。

    b. **[Enable debug logging for the multiple provider SSO integration (複数プロバイダー SSO 統合でのデバッグ ログの有効化)]** を右に切り替えます。

    c. **[The field on the user table that...]\(ユーザー テーブルのフィールド...\)** に、「**user_name**」と入力します。

4. **[Single Sign-On]\(シングル サインオン\)** ダイアログ ボックスで、 **[Add New Certificate]\(新しい証明書の追加\)** を選択します。

    ![[Add New Certificate]\(新しい証明書の追加\) が強調表示された [Single Sign-On]\(シングル サインオン\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/ic7694973ex.png "Configure single sign-on")

5. **[X.509 Certificates]\(X.509 証明書\)** ダイアログ ボックスで、次の手順を実行します。

    ![[X.509 Certificates]\(X.509 証明書\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/ic7694975.png "Configure single sign-on")

    a. **[Name]\(名前\)** に、自分の構成の名前を入力します (例:**TestSAML2.0**)。

    b. **[アクティブ]** を選択します。

    c. **[Format]\(形式\)** で **[PEM]** を選択します。

    d. **[Type]\(種類\)** で **[Trust Store Cert]\(信頼ストア証明書\)** を選択します。

    e. Azure portal からダウンロードした実際の Base64 エンコード証明書をメモ帳で開きます。 その内容をクリップボードにコピーし、 **[PEM Certificate]\(PEM 証明書\)** ボックスに貼り付けます。

    f. **[Update]\(更新\)** を選択します。

6. **[Single Sign-On]\(シングル サインオン\)** ダイアログ ボックスで、 **[Add New IdP]\(新しい IdP の追加\)** を選択します。

    ![[Add New IdP]\(新しい IdP の追加\) が強調表示された [Single Sign-On]\(シングル サインオン\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/ic7694976ex.png "Configure single sign-on")

7. **[Add New Identity Provider]\(新しい ID プロバイダーの追加\)** ダイアログ ボックスの **[Configure Identity Provider]\(ID プロバイダーの構成\)** で、次の手順を実行します。

    ![[Add New Identity Provider]\(新しい ID プロバイダーの追加\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/ic7694982ex.png "Configure single sign-on")

    a. **[Name]\(名前\)** に、自分の構成の名前を入力します (例:**SAML 2.0**)。

    b. **[Identity Provider URL]\(ID プロバイダー URL\)** に、Azure portal からコピーした ID プロバイダー ID の値を貼り付けます。

    c. **[Identity Provider's AuthnRequest]\(ID プロバイダーの AuthnRequest\)** に、Azure portal からコピーした認証要求 URL の値を貼り付けます。

    d. **[Identity Provider's SingleLogoutRequest]\(ID プロバイダーの SingleLogoutRequest\)** に、Azure portal からコピーしたログアウト URL の値を貼り付けます。

    e. **[Identity Provider Certificate]\(ID プロバイダー証明書\)** で、前の手順で作成した証明書を選択します。

8. **[Advanced Settings]\(詳細設定\)** を選択します。 **[Additional Identity Provider Properties]\(追加の ID プロバイダーのプロパティ\)** で、次の手順を実行します。

    ![[Advanced Settings]\(詳細設定\) が強調表示された [Add New Identity Provider]\(新しい ID プロバイダーの追加\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/ic7694983ex.png "Configure single sign-on")

    a. **[Protocol Binding for the IDP's SingleLogoutRequest]\(IDP の SingleLogoutRequest のプロトコル バインド\)** に、「**urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**」と入力します。

    b. **[NameID Policy]\(NameID ポリシー\)** に、「**urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**」と入力します。

    c. **[AuthnContextClassRef Method]\(AuthnContextClassRef メソッド\)** に、「`http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`」と入力します。

    d. **[Create an AuthnContextClass]\(Authncontextclass の作成\)** をオフ (選択解除) に切り替えます。

9. **[Additional Service Provider Properties (追加のサービス プロバイダーのプロパティ)]** で、次の手順に従います。

    ![さまざまなプロパティが強調表示された [Add Identity Provider]\(ID プロバイダーの追加\) ダイアログ ボックスのスクリーンショット](./media/servicenow-tutorial/ic7694984ex.png "Configure single sign-on")

    a. **[ServiceNow Homepage]\(ServiceNow のホーム ページ\)** に実際の ServiceNow インスタンス ホーム ページの URL を入力します。

    > [!NOTE]
    > ServiceNow インスタンス ホームページは、**ServiceNow テナント URL** と **/navpage.do** を連結した形式です (例: `https://fabrikam.service-now.com/navpage.do`)。

    b. **[Entity ID / Issuer]\(エンティティ ID/発行者\)** に、実際の ServiceNow テナントの URL を入力します。

    c. **[Audience URI]\(対象ユーザー URI\)** に、実際の ServiceNow テナントの URL を入力します。

    d. **[Clock Skew]\(時刻のずれ\)** に「**60**」と入力します。

    e. **[User Field]\(ユーザー フィールド\)** に「**email**」と入力します。

    > [!NOTE]
    > SAML トークンの一意の識別子として Azure AD ユーザー ID (ユーザーのプリンシパル名) かメール アドレスを出力するように Azure AD を構成できます。 そのためには、Azure portal で **[ServiceNow]**  >  **[属性]**  >  **[シングル サインオン]** セクションの順に移動し、目的のフィールドを **nameidentifier** 属性にマッピングします。 Azure AD に格納される選択した属性 (ユーザー プリンシパル名など) の値と、ServiceNow に格納される入力したフィールド (user_name など) の値が一致している必要があります。

    f. **[保存]** を選択します。

## <a name="test-sso"></a>SSO のテスト

アクセス パネルで [ServiceNow] タイルを選択すると、SSO を設定した ServiceNow に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="test-sso-for-servicenow-classic-mobile"></a>ServiceNow Classic (モバイル) の SSO をテストする

1. **ServiceNow Classic (モバイル)** アプリケーションを開き、次の手順を実行します。

    a. 右下隅にあるプラス記号を選択します。

    ![プラス記号が強調表示された ServiceNow Classic アプリケーションのスクリーンショット](./media/servicenow-tutorial/test-03.png)

    b. 実際の ServiceNow インスタンス名を入力し、 **[Continue]\(続行\)** を選択します。

    ![[Continue]\(続行\) が強調表示された [Add Instance]\(インスタンスの追加\) ページのスクリーンショット](./media/servicenow-tutorial/test-04.png)

    c. **[Log in]\(ログイン\)** ページで、次の手順を実行します。

    ![[Use external login]\(外部ログインの使用\) が強調表示された [Log in]\(ログイン\) ページのスクリーンショット](./media/servicenow-tutorial/test-01.png)

    *  **ユーザー名** (例: B.simon@contoso.com) を入力します。

    *  **[Use external login]\(外部ログインの使用\)** を選択します。 サインインのために Azure AD ページにリダイレクトされます。

    *  資格情報を入力します。 任意のサードパーティの認証またはその他の有効になっているセキュリティ機能がある場合、ユーザーはそれに対応する必要があります。 アプリケーションの **ホーム ページ** が表示されます。

        ![アプリケーションのホーム ページのスクリーンショット](./media/servicenow-tutorial/test-02.png)

## <a name="next-steps"></a>次の手順

ServiceNow を構成したら、自分の組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。
