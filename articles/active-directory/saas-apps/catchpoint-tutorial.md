---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Catchpoint の統合
description: Azure Active Directory と Catchpoint の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: a8515920985c569df74b1e328d6bfe1c4ec97195
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735316"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>チュートリアル:Azure Active Directory シングル サインオンと Catchpoint の統合

このチュートリアルでは、Catchpoint と Azure Active Directory (Azure AD) を統合する方法について説明します。 Catchpoint を Azure AD と統合すると、次のことができます。

* Azure AD から Catchpoint へのユーザー アクセスを制御する。
* Azure AD アカウントを持つユーザーに対して Catchpoint への自動サインインを有効にする。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Catchpoint サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Catchpoint では、SP Initiated SSO と IDP Initiated SSO がサポートされます。
* Catchpoint では、Just-In-Time (JIT) ユーザー プロビジョニングがサポートされます。

## <a name="add-catchpoint-from-the-gallery"></a>ギャラリーからの Catchpoint の追加

Azure AD への Catchpoint の統合を構成するには、マネージド SaaS アプリの一覧に Catchpoint を追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Catchpoint**」と入力します。
1. 結果パネルから **[Catchpoint]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-catchpoint"></a>Catchpoint の Azure AD SSO の構成とテスト

SSO を機能させるには、Azure AD ユーザーを Catchpoint のユーザーにリンクする必要があります。 このチュートリアルでは、**B.Simon** というテスト ユーザーを構成します。 

次のセクションを完了します。

1. [Azure AD SSO の構成](#configure-azure-ad-sso)。ユーザーがこの機能を使用できるようにします。
    * [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)。B.Simon で Azure AD のシングル サインオンをテストします。
    * [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)。B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [Catchpoint の SSO の構成](#configure-catchpoint-sso)。アプリケーション側でシングル サインオン設定を構成します。
    * [Catchpoint のテスト ユーザーの作成](#create-a-catchpoint-test-user)。B.Simon Azure AD テスト アカウントを Catchpoint の類似のユーザー アカウントにリンクできるようにします。
1. [SSO のテスト](#test-sso)。構成が機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で次の手順に従って、Azure AD SSO を有効にします。

1. Azure portal にサインインします。
1. **Catchpoint** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンの設定]** ページで、鉛筆アイコンを選択して **[基本的な SAML 構成]** の設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. Catchpoint の開始モードを構成します。
   - **IDP** 開始モードの場合は、次のフィールドの値を入力します。
     - **[識別子]** : `https://portal.catchpoint.com/SAML2`
     - **[応答 URL]** : `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - **SP** 開始モードの場合は、 **[追加の URL を設定します]** を選択して、次の値を入力します。
     - **[サインオン URL]** : `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint アプリケーションは、特定の形式の SAML アサーションを想定しています。 カスタム属性マッピングを SAML トークン属性の構成に追加します。 次の表に、既定の属性の一覧を示します。

    | 名前 | ソース属性|
    | ------------ | --------- |
    | Givenname | user.givenneame |
    | Surname | User.surname |
    | Emailaddress | User.mail |
    | 名前 | user.userprincipalname |
    | 一意のユーザー ID | user.userprincipalname |

    ![[ユーザー属性とクレーム] リストのスクリーンショット](common/default-attributes.png)

1. また、Catchpoint アプリケーションは、SAML 応答で別の属性が渡されることを想定しています。 次の表を参照してください。 この属性も値が事前に設定されますが、その値を確認し、要件に合わせて更新することができます。

    | 名前 | ソース属性|
    | ------------ | --------- |
    | namespace | user.assignedrole |

    > [!NOTE]
    > `namespace` 要求は、アカウント名でマップする必要があります。 このアカウント名は、SAML 応答で返される、Azure AD のロールを使用して設定する必要があります。 Azure AD のロールの詳細については、[エンタープライズ アプリケーション用の SAML トークン内に発行されるロール要求を構成する方法](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)に関するページを参照してください。

1. **[SAML によるシングル サインオンのセットアップ]** ページに移動します。 **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけます。 **[ダウンロード]** を選択して証明書をお使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Catchpoint のセットアップ]** セクションで、後の手順で必要な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal を使用して、B.Simon という Azure AD テスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、`B.Simon@contoso.com` と入力します。
   1. **[パスワードを表示]** チェック ボックスを選択します。 表示されているパスワード値をメモします。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Catchpoint へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Catchpoint]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 画面の下部にある **[選択]** をクリックします。
1. 前述のロールを設定した場合、 **[ロールの選択]** ボックスの一覧からそれを選択できます。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-catchpoint-sso"></a>Catchpoint の SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者として Catchpoint アプリケーションにサインインします。

1. **[Settings]\(設定\)** アイコンを選択し、 **[SSO Identity Provider]\(SSO ID プロバイダー\)** を選択します。

    ![SSO ID プロバイダーが選択された Catchpoint 設定のスクリーンショット](./media/catchpoint-tutorial/configuration1.png)

1. **[Single sign-on]\(シングル サインオン\)** ページで、次のフィールドに入力します。

   ![Catchpoint の [Single sign-on]\(シングル サインオン\) ページのスクリーンショット](./media/catchpoint-tutorial/configuration2.png)

   フィールド | 値
   ----- | ----- 
   **Namespace** | 有効な名前空間の値。
   **[Identity Provider Issuer]\(ID プロバイダーの発行者\)** | Azure portal から取得した `Azure AD Identifier` 値。
   **[Single Sign On Url]\(シングル サインオン URL\)** | Azure portal から取得した `Login URL` 値。
   **[MSSQLSERVER のプロトコルのプロパティ]** | Azure portal からダウンロードした `Certificate (Base64)` ファイルの内容。 メモ帳を使用して表示およびコピーします。

   **[Upload Metadata]\(メタデータのアップロード\)** オプションを選択して、**フェデレーション メタデータ XML** をアップロードすることもできます。

1. **[保存]** を選択します。

### <a name="create-a-catchpoint-test-user"></a>Catchpoint のテスト ユーザーの作成

Catchpoint では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 B.Simon が Catchpoint のユーザーとしてまだ存在していない場合は、認証後に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Catchpoint のサインオン URL にリダイレクトされます。  

* Catchpoint のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Catchpoint に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Catchpoint] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Catchpoint に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


> [!NOTE]
> ログイン ページから Catchpoint アプリケーションにサインインしたら、**Catchpoint の資格情報** を入力した後、 **[Company Credentials(SSO)]\(会社の資格情報 (SSO)\)** フィールドに有効な **名前空間** の値を入力し、 **[Login]\(ログイン\)** を選択します。
> 
> ![Catchpoint の構成](./media/catchpoint-tutorial/loginimage.png)

## <a name="next-steps"></a>次のステップ

Catchpoint を構成した後、セッション制御を適用できます。 この予防措置により、組織の機密データを流出と侵入からリアルタイムで保護することができます。 セッション制御は、条件付きアクセスの拡張機能です。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
