---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と BeyondTrust Remote Support の統合 | Microsoft Docs
description: Azure Active Directory と BeyondTrust Remote Support の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.openlocfilehash: ecaeea625bc4891c95614292a44d31b616d39eb2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549295"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と BeyondTrust Remote Support の統合

このチュートリアルでは、BeyondTrust Remote Support と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と BeyondTrust Remote Support を統合すると、次のことができます。

* BeyondTrust Remote Support にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して BeyondTrust Remote Support に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* BeyondTrust Remote Support でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* BeyondTrust Remote Support では、**SP** によって開始される SSO がサポートされます
* BeyondTrust Remote Support では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>ギャラリーから BeyondTrust Remote Support を追加する

Azure AD への BeyondTrust Remote Support の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に BeyondTrust Remote Support を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**BeyondTrust Remote Support**」と入力します。
1. 結果のパネルから **[BeyondTrust Remote Support]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>BeyondTrust Remote Support の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、BeyondTrust Remote Support に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと BeyondTrust Remote Support の関連ユーザーとの間にリンク関係を確立する必要があります。

BeyondTrust Remote Support に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[BeyondTrust Remote Support の SSO の構成](#configure-beyondtrust-remote-support-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[BeyondTrust Remote Support のテスト ユーザーの作成](#create-beyondtrust-remote-support-test-user)** - BeyondTrust Remote Support で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **BeyondTrust Remote Support** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<HOSTNAME>.bomgar.com/saml` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<HOSTNAME>.bomgar.com` という形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<HOSTNAME>.bomgar.com/saml/sso` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値については、このチュートリアルの後半で説明します。

1. BeyondTrust Remote Support アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、BeyondTrust Remote Support アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | ---------------| ----------|
    | Givenname | User.givenname |
    | Emailaddress | User.mail |
    | 名前 | user.userprincipalname |
    | ユーザー名 | user.userprincipalname |
    | グループ | user.groups |
    | 一意のユーザー ID | user.userprincipalname |

    > [!NOTE]
    > BeyondTrust Remote Support アプリケーションに Azure AD グループを割り当てる場合、"要求で返されるグループ" オプションを "None" から "SecurityGroup" に変更する必要があります。 グループは、オブジェクト ID としてアプリケーションにインポートされます。 Azure AD グループのオブジェクト ID は見つけるには、Azure Active Directory インターフェイスで [プロパティ] を確認します。 これは、Azure AD グループを参照して、適切なグループ ポリシーに割り当てるために必要です。

1. 一意のユーザー ID を設定するときは、この値を次の NameID の形式に設定する必要があります: **[永続的]** 。 ユーザーを正しく識別し、アクセス許可の適切なグループ ポリシーに関連付けるために、これを永続的な識別子にする必要があります。 [編集] アイコンをクリックして **[ユーザー属性と要求]** ダイアログを開き、[一意のユーザー ID] の値を編集します。

1. **[要求の管理]** セクションで、 **[名前識別子の形式の選択]** をクリックし、値を **[永続的]** に設定して、 **[保存]** をクリックします。

    ![ユーザー属性と要求](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[BeyondTrust Remote Support のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、BeyondTrust Remote Support へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーション一覧で **[BeyondTrust Remote Support]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-beyondtrust-remote-support-sso"></a>BeyondTrust Remote Support の SSO の構成

1. 別の Web ブラウザーのウィンドウで、BeyondTrust Remote Support に管理者としてサインインします。

1. **[STATUS]\(状態\)** メニューをクリックし、**識別子**、**応答 URL**、**サインオン URL** をコピーして、Azure portal の **[基本的な SAML 構成]** セクションでこれらの値を使用します。

    ![BeyondTrust Remote Support の構成](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. `https://support.example.com/login` で BeyondTrust Remote Support の /login インターフェイスに移動します。**support.example.com** はご使用のアプライアンスのプライマリ ホスト名であり、管理者の資格情報を使用して認証を行います。

1. **[Users & Security]\(ユーザーとセキュリティ\)**  >  **[Security Providers]\(セキュリティ プロバイダー\)** に移動します。

1. ドロップダウン メニューで、 **[SAML]** を選択し、 **[Create Provider]\(プロバイダーの作成\)** ボタンをクリックします。

1. [Identity Provider Settings]\(ID プロバイダーの設定\) セクションに、ID プロバイダーのメタデータをアップロードするオプションがあります。 Azure portal からダウンロードしたメタデータ XML ファイルを探して、 **[Upload]\(アップロード\)** ボタンをクリックします。 **[Entity ID]\(エンティティ ID\)** 、 **[Single Sign-On Service URL]\(シングル サインオン サービス URL\)** と証明書が自動的にアップロードされます。 **[Protocol Binding]\(プロトコル バインド\)** は **[HTTP POST]** に変更する必要があります。 次のスクリーンショットをご覧ください。

    ![BeyondTrust Remote Support の構成](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>BeyondTrust Remote Support のテスト ユーザーを作成する

ここでは、ユーザー プロビジョニング設定を構成します。 このセクションで使用される値は、Azure portal の **[ユーザー属性と要求]** セクションから参照されます。 これは、作成時に既にインポートされている既定値に構成しましたが、必要に応じて値をカスタマイズできます。

![ユーザーの作成](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> この実装では、グループおよび電子メールの属性は必要ありません。 Azure AD グループを利用して、それらをアクセス許可の BeyondTrust Remote Support グループ ポリシーに割り当てる場合は、グループのオブジェクト ID を Azure portal のそのプロパティを使用して参照し、[Available Groups]\(使用可能なグループ\) セクションに配置する必要があります。 これが完了すると、オブジェクト ID/AD グループがアクセス許可のグループ ポリシーへの割り当てに使用できるようになります。

![ユーザーの作成](./media/bomgarremotesupport-tutorial/config-user2.png)

![ユーザーの作成](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> または、SAML2 セキュリティ プロバイダーで既定のグループ ポリシーを設定することもできます。 このオプションを定義することで、SAML 経由で認証を行うすべてのユーザーに、グループ ポリシー内で指定されたアクセス許可が割り当てられます。 General Members ポリシーは、アクセス許可が制限されている BeyondTrust Remote Support/Privileged Remote Access に含まれており、認証をテストしてユーザーを適切なポリシーに割り当てるために使用できます。 ユーザーは、認証の試行が最初に成功するまで、/login > [Users & Security]\(ユーザーとセキュリティ\) 経由で SAML2 ユーザーの一覧に追加されません。 グループ ポリシーに関する追加情報については、リンク `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm` を参照してください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [BeyondTrust Remote Support] タイルをクリックすると、SSO を設定した BeyondTrust Remote Support に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で BeyondTrust Remote Support を試す](https://aad.portal.azure.com/)
