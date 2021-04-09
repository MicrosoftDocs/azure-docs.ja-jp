---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cisco Webex Meetings の統合 | Microsoft Docs
description: Azure Active Directory と Cisco Webex Meetings の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: bb8ea637d0353e4efa0cb946f486d68639fc699d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592491"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cisco Webex Meetings の統合

このチュートリアルでは、Cisco Webex Meetings と Azure Active Directory (Azure AD) を統合する方法について説明します。 Cisco Webex Meetings を Azure AD と統合すると、次のことができます。

* Cisco Webex Meetings にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Cisco Webex Meetings に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。


## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Cisco Webex Meetings でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Cisco Webex Meetings では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

* Cisco Webex Meetings では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>ギャラリーからの Cisco Webex Meetings の追加

Azure AD への Cisco Webex Meetings の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cisco Webex Meetings を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Cisco Webex Meetings**」と入力します。
1. 結果のパネルから **[Cisco Webex Meetings]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>Cisco Webex Meetings の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Cisco Webex Meetings に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Cisco Webex Meetings の関連ユーザーとの間にリンク関係を確立する必要があります。

Cisco Webex Meetings に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
   1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
   1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
   
1. **[Cisco Webex Meetings の SSO の構成](#configure-cisco-webex-meetings-sso)** - アプリケーション側でシングル サインオン設定を構成します。
   * **[Cisco Webex Meetings のテスト ユーザーの作成](#create-cisco-webex-meetings-test-user)** - Cisco Webex Meetings で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
    
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Cisco Webex Meetings** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、**サービス プロバイダー メタデータ** ファイルを次のようにアップロードして、**IDP** 開始モードでアプリケーションを構成できます。
   1. **[メタデータ ファイルをアップロードします]** をクリックします。
   1. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。
   1. サービス プロバイダー メタデータ ファイルのアップロードが正常に完了すると、次のように、**[識別子]** と **[応答 URL]** の値が **[基本的な SAML 構成]** セクションに自動的に入力されます。
   
      > [!Note]
      > サービス プロバイダー メタデータ ファイルは、「**Cisco Webex Meetings の SSO の構成**」セクションで取得します。これについては、チュートリアルの後の方で説明します。 

1. **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。  
   1. **[基本的な SAML 構成]** セクションで、編集 (ペン) アイコンをクリックします。

      ![基本的な SAML 構成を編集する](common/edit-urls.png)

   1. **[サインオン URL]** ボックスに、`https://<customername>.my.webex.com` という形式で URL を入力します。

1. Cisco Webex Meetings アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

   ![image](common/edit-attribute.png)

1. その他に、Cisco Webex Meetings アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 [ユーザー属性] ダイアログの [ユーザー要求] セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。 

   | 名前 | ソース属性|
   | ---------------|  --------- |
   |   firstname    | User.givenname |
   |   lastname    | User.surname |
   |   email       | User.mail |
   |   uid    | User.mail |

   1. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。
   1. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
   1. **[名前空間]** は空白のままにします。
   1. [ソース] として **[属性]** を選択します。
   1. **[ソース属性]** の一覧から、その行に表示される属性値をドロップダウン リストから選択します。
   1. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Cisco Webex Meetings のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Cisco Webex Meetings へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Cisco Webex Meetings]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cisco-webex-meetings-sso"></a>Cisco Webex Meetings の SSO の構成

1. 管理者の資格情報を使用して Cisco Webex Meetings にサインインします。
1. **[Common Site Settings]\(共通サイト設定\)** に移動し、**[SSO Configuration]\(SSO 構成\)** に移動します。

   ![スクリーンショットは、[Common Site Settings]\(共通サイト設定\) と [SSO Configuration]\(SSO 構成\) が選択されている [Cisco Webex Administration]\(Cisco Webex 管理\) を示しています。](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. **[Webex Administration]\(Webex 管理\)** ページで、次の手順を実行します。

   ![スクリーンショットは、この手順で説明されている情報を含む [Webex Administration]\(Webex 管理\) ページを示しています。](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. **[Federation Protocol]\(フェデレーション プロトコル\)** として **[SAML 2.0]** を選択します。
   1. **[SAML メタデータのインポート]** リンクをクリックして、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。
   1. **[SSO Profile]\(SSO プロファイル\)** として **[IDP initiated]** を選択し、 **[Export]\(エクスポート\)** ボタンをクリックしてサービス プロバイダー メタデータ ファイルをダウンロードし、Azure portal の **[基本的な SAML 構成]** セクションにアップロードします。
   1. **[AuthContextClassRef]** ボックスに、次のいずれかの値を入力します。
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      Azure AD を使用して MFA を有効にするには、`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509` のように 2 つの値を入力します。

   1. **[アカウントの自動作成]** を選択します。
   
      > [!NOTE]
      > **Just-In-Time** ユーザー プロビジョニングを有効にするには、**[アカウントの自動作成]** をチェックする必要があります。 さらに、SAML トークン属性を、SAML 応答で渡す必要があります。

   1. **[保存]** をクリックします。

      > [!NOTE]
      > この構成は、メール形式の Webex UserID を使用するユーザー専用です。
      > 
      > Cisco Webex Meetings の構成方法について詳しくは、[Webex ドキュメント](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) ページを参照してください。

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco Webex Meetings のテスト ユーザーの作成

このセクションの目的は、Cisco Webex Meetings で B.Simon というユーザーを作成することです。 Cisco Webex Meetings では、**Just-In-Time** プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Cisco Webex Meetings に存在しない場合は、Cisco Webex Meetings にアクセスしようとしたときに新しいユーザーが作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Cisco Webex Meetings のサインオン URL にリダイレクトされます。  

* Cisco Webex Meetings のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Cisco Webex Meetings に自動的にサインインされます。

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Cisco Webex Meetings] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Cisco Webex Meetings に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Cisco Webex Meetings を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。