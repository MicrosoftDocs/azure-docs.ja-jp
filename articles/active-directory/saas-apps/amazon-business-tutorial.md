---
title: チュートリアル:Azure Active Directory と Amazon ビジネスの統合 | Microsoft Docs
description: Azure Active Directory と Amazon ビジネスの間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 1424669af9fe28257e66c8c5438f6e1d171954e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97672901"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>チュートリアル:Amazon ビジネスと Azure Active Directory の統合

このチュートリアルでは、Amazon ビジネスと Azure Active Directory (Azure AD) を統合する方法について説明します。 [Amazon ビジネス](https://www.amazon.com/b2b/info/amazon-business?layout=landing)を Azure AD に統合すると、次のことができます。

* Amazon ビジネスにアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Amazon ビジネスに自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* Amazon ビジネスでのシングル サインオン (SSO) が有効なサブスクリプション。 [Amazon ビジネス](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp)のページに移動して、Amazon ビジネス アカウントを作成します。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、既存の Amazon ビジネス アカウント内で Azure AD SSO を構成してテストします。

* Amazon ビジネスでは、**SP および IDP** Initiated SSO がサポートされます
* Amazon ビジネスでは、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-amazon-business-from-the-gallery"></a>ギャラリーからの Amazon ビジネスの追加

Azure AD への Amazon ビジネスの統合を構成するには、ギャラリーからご自分のマネージド SaaS アプリの一覧に Amazon ビジネスを追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Amazon Business**」と入力します。
1. 結果ウィンドウで **[Amazon Business]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Amazon ビジネスに対する Azure AD SSO を構成してテストします。

Amazon ビジネスで Azure AD SSO を構成してテストするには、次の構成手順を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Amazon ビジネス SSO の構成](#configure-amazon-business-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Amazon ビジネスのテスト ユーザーの作成](#create-amazon-business-test-user)** - Amazon ビジネスで B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Amazon ビジネス** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**IDP** Initiated モードで構成する場合は、次の手順を実行します。

    1. **[識別子 (エンティティ ID)]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

       | URL | リージョン |
       |-|-|
       | `https://www.amazon.com`| 北米 |
       | `https://www.amazon.co.jp`| 東アジア |
       | `https://www.amazon.de`| ヨーロッパ |

    1. **[応答 URL]** ボックスに、次のいずれかのパターンを使用して URL を入力します。

       | URL | リージョン |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| 北米 |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| 東アジア |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| ヨーロッパ |

       > [!NOTE]
       > 応答 URL 値は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 `<idpid>` の値は、このチュートリアルで後述する「Amazon ビジネス SSO の構成」セクションで得られます。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **SP** 開始モードでアプリケーションを構成する場合は、Amazon ビジネスの構成に指定されている完全な URL を、 **[追加の URL を設定します]** セクションの **[サインオン URL]** に追加する必要があります。

1. 次のスクリーンショットには、既定の属性一覧が示されています。 **[ユーザー属性と要求]** セクションで、 **[編集]** アイコンをクリックして、属性を編集します。

    ![スクリーンショットは [User Attributes & Claims]\(ユーザー属性とクレーム\) を示しています。[Givenname]\(名\) の user.givenname や [Emailaddress]\(メール アドレス\) の user.mail など、既定値が表示されています。](media/amazon-business-tutorial/map-attribute3.png)

1. 属性を編集し、これらの属性の **名前空間** の値をメモ帳にコピーします。

    ![このスクリーンショットは、[クレーム名] 列と [値] 列が表示された状態の [User Attributes & Claims]\(ユーザー属性と要求\) を示しています。](media/amazon-business-tutorial/map-attribute4.png)

1. その他に、Amazon ビジネス アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[グループ要求]** ダイアログの **[ユーザー属性と要求]** セクションで、次の手順を実行します。

    1. **[Groups returned in claim]\(要求で返されるグループ\)** の横にある **ペン** をクリックします。

        ![このスクリーンショットは、[クレームで返されるグループ] のアイコンが選択された状態の [User Attributes & Claims]\(ユーザー属性と要求\) を示しています。](./media/amazon-business-tutorial/config04.png)

        ![このスクリーンショットは、この手順で説明されている値が表示された状態の [グループ クレーム] を示しています。](./media/amazon-business-tutorial/config05.png)

    1. ラジオ ボタンのリストから **[すべてのグループ]** を選択します。

    1. **[ソース属性]** として **[グループ ID]** を選択します。

    1. **[グループ要求の名前をカスタマイズする]** チェック ボックスをオンにし、ご自分の組織の要件に従ってグループ名を入力します。

    1. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Amazon Business のセットアップ]** セクションで、各自の要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Amazon ビジネス SSO の構成

1. 別の Web ブラウザー ウィンドウで、ご使用の Amazon ビジネス企業サイトに管理者としてサインインします。

1. **ユーザー プロファイル** をクリックし、 **[Business Settings]\(ビジネス設定\)** を選択します。

    ![ユーザー プロファイル](media/amazon-business-tutorial/user-profile.png)

1. **System integrations (システム統合)** ウィザードで、 **[Single Sign-On (SSO)]\(シングルサインオン (SSO)\)** を選択します。

    ![シングル サインオン (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. **Set up SSO (SSO の設定)** ウィザードで、ご自分の組織の要件に従ってプロバイダーを選択し、 **[Next]\(次へ\)** をクリックします。

    ![このスクリーンショットは、[Microsoft Azure AD] と [次へ] が選択された状態の [Set up S S O]\(S S O の設定\) を示しています。](media/amazon-business-tutorial/default-group1.png)

    > [!NOTE]
    > Microsoft ADFS はオプションとして表示されていますが、Azure AD SSO では機能しません。

1. **New user account defaults (新しいユーザー アカウントの既定値)** ウィザードで、 **[Default Group]\(既定のグループ\)** を選択し、ご自分の組織内のユーザー ロールに従って **[Default Buying Role]\(既定の購入ロール\)** を選択し、 **[Next]\(次へ\)** をクリックします。

    ![このスクリーンショットは、[Microsoft S S O]、[Requisitioner]\(要求者\)、および [次へ] が選択された状態の [New user account defaults]\(新しいユーザー アカウントの既定値\) を示しています。](media/amazon-business-tutorial/dafault-group2.png)

1. **Upload your metadata file (メタデータ ファイルのアップロード)** ウィザードで、 **[Browse]\(参照\)** をクリックして、Azure portal からダウンロードした **メタデータ XML** ファイルをアップロードします。 **[Upload]\(アップロード\)** をクリックします。

    ![このスクリーンショットは、[Upload your metadata file]\(メタデータ ファイルのアップロード\) を示しています。ここで、xml ファイルを参照してアップロードできます。](media/amazon-business-tutorial/connection-data1.png)

1. ダウンロードしたメタデータ ファイルをアップロードすると、 **[Connection data]\(接続データ\)** セクション内のフィールドが自動的に設定されます。 その後、 **[Next]\(次へ\)** をクリックします。

    ![このスクリーンショットは、[Connection data]\(接続データ\) を示しています。ここで、Azure A D 識別子、ログイン U R L、および SAML 署名証明書を指定できます。](media/amazon-business-tutorial/connection-data2.png)

1. **Upload your Attribute statement (属性のアップロード)** ウィザードで、 **[Skip]\(スキップ\)** をクリックします。

    ![このスクリーンショットは、[Upload your Attribute statement]\(属性ステートメントのアップロード\) を示しています。ここで、属性ステートメントを参照できますが、この場合は [スキップ] を選択します。](media/amazon-business-tutorial/map-attribute1.png)

1. **Attribute mapping (属性マッピング)** ウィザードで **[+ Add a field]\(+ フィールドの追加\)** オプションをクリックして、要件フィールドを追加します。 Azure portal の **[ユーザー属性と要求]** セクションからコピーした、名前空間を含む属性値を **[SAML AttributeName]** フィールドに追加し、 **[Next]\(次へ\)** をクリックします。

    ![このスクリーンショットは、[属性マッピング] を示しています。ここで、Amazon データの SAML 属性名を編集できます。](media/amazon-business-tutorial/map-attribute2.png)

1. **Amazon connection data (Amazon 接続データ)** ウィザードで、 **[Next]\(次へ\)** をクリックします。

    ![このスクリーンショットは [Amazon connection data]\(Amazon 接続データ\) を示しています。[次へ] をクリックして続行できます。](media/amazon-business-tutorial/amazon-connect.png)

1. 構成されている手順の **[Status]\(状態\)** を確認し、 **[Start testing]\(テストの開始\)** をクリックします。

    ![このスクリーンショットは、[Start testing]\(テストの開始\) オプションを備えた [S S O Connection details]\(S S O 接続の詳細\) を示しています。](media/amazon-business-tutorial/sso-connection1.png)

1. **Test SSO Connection (SSO 接続のテスト)** ウィザードで、 **[Test]\(テスト\)** をクリックします。

    ![このスクリーンショットは、[テスト] ボタンを備えた [Test S S O Connection]\(S S O 接続のテスト\) を示しています。](media/amazon-business-tutorial/sso-connection2.png)

1. **IDP initiated URL** ウィザードで、 **[Activate]\(アクティブ化\)** をクリックする前に、**idpid** に割り当てられている値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションにある **[応答 URL]** の **idpid** パラメーターに貼り付けます。

    ![このスクリーンショットは、[I D P initiated U R L] を示しています。ここで、テストに必要な U R L を取得し、[アクティブ化] を選択できます。](media/amazon-business-tutorial/sso-connection3.png)

1. **Are you ready to switch to active SSO? (アクティブな SSO に切り替える準備ができましたか?)** ウィザードで、 **[I have fully tested SSO and am ready to go live]\(SSO は完全にテスト済みで、ライブにする準備ができている\)** チェック ボックスをオンにし、 **[Switch to active]\(アクティブに切り替える\)** をクリックします。

    ![このスクリーンショットは、[Are you ready to switch to active S S O]\(アクティブな S S O に切り替える準備ができましたか\) 確認を示しています。ここで、[Switch to active]\(アクティブに切り替える\) を選択できます。](media/amazon-business-tutorial/sso-connection4.png)

1. 最後に、 **[SSO Connection details]\(SSO 接続の詳細\)** セクションで、 **[Status]\(状態\)** が **[Active]\(アクティブ\)** として表示されます。

    ![このスクリーンショットは、状態が [アクティブ] と表示された [S S O Connection details]\(S S O 接続の詳細\) を示しています。](media/amazon-business-tutorial/sso-connection5.png)

    > [!NOTE]
    > **SP** 開始モードでアプリケーションを構成する場合は、次の手順を実行して、上のスクリーンショットのサインオン URL を、Azure portal の **[追加の URL を設定します]** セクションの **[サインオン URL]** テキスト ボックスに貼り付けます。 次の形式を使用します。
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<uniqueid>`

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

> [!NOTE]
> 管理者は、必要に応じて、ご自分のテナント内にテスト ユーザーを作成する必要があります。 次の手順は、テスト ユーザーを作成する方法を示しています。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure portal で Azure AD セキュリティ グループを作成する

1. **[Azure Active Directory] > [すべてのグループ]** をクリックします。

    ![このスクリーンショットは、[Azure Active Directory] が選択され、[グループ] ペインで [すべてのグループ] が選択された状態の Azure portal メニューを示しています。](./media/amazon-business-tutorial/all-groups-tab.png)

1. **[新しいグループ]** をクリックします。

    ![このスクリーンショットは、[新しいグループ] ボタンを示しています。](./media/amazon-business-tutorial/new-group-tab.png)

1. **[グループの種類]** 、 **[グループ名]** 、 **[グループの説明]** 、 **[メンバーシップの種類]** を入力します。 矢印をクリックしてメンバーを選択し、グループに追加するメンバーを検索またはクリックします。 **[選択]** をクリックして選択したメンバーを追加した後、 **[作成]** をクリックします。

    ![このスクリーンショットは、メンバーの選択や外部ユーザーの招待などのオプションを含む [グループ] ペインを示しています。](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Amazon ビジネスへのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Amazon Business]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![このスクリーンショットは、[ユーザーの追加] ボタンを示しています。](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

    >[!NOTE]
    > Azure AD 内でユーザーを割り当てないと、次のエラーが表示されます。

    ![このスクリーンショットは、サインインできないことを示すエラー メッセージを示しています。](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Azure portal で Azure AD セキュリティ グループを割り当てる

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Amazon Business]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**Amazon Business**」と入力し、選択します。

    ![アプリケーションの一覧の [Amazon Business] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックします。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. 使用するセキュリティ グループを検索してから、グループをクリックして [メンバーの選択] セクションに追加します。 **[選択]** をクリックし、 **[割り当て]** をクリックします。

    ![セキュリティ グループの検索](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > メニュー バーの通知で、グループが Azure portal のエンタープライズ アプリケーションに正常に割り当てられたことを確認します。

### <a name="create-amazon-business-test-user"></a>Amazon ビジネスのテスト ユーザーの作成

このセクションでは、B. Simon というユーザーを Amazon ビジネス内に作成します。 Amazon ビジネスでは、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Amazon ビジネス内にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Amazon Business] タイルをクリックすると、SSO を設定した Amazon ビジネスに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)