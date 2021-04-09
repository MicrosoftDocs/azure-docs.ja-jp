---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Splashtop の統合 | Microsoft Docs
description: Azure Active Directory と Splashtop の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: b6dda20487caf6fe3ba49578cfdc0b65434a8dfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92520559"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Splashtop の統合

このチュートリアルでは、Splashtop と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Splashtop を統合すると、次のことができます。

* Splashtop にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Splashtop に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Splashtop でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Splashtop では、**SP** Initiated SSO がサポートされます

* Splashtop を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-splashtop-from-the-gallery"></a>ギャラリーからの Splashtop の追加

Azure AD への Splashtop の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Splashtop を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Splashtop**」と入力します。
1. 結果パネルで **[Splashtop]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Splashtop の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Splashtop に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Splashtop の関連ユーザーとの間にリンク関係を確立する必要があります。

Splashtop で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Splashtop の SSO の構成](#configure-splashtop-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Splashtop のテスト ユーザーの作成](#create-splashtop-test-user)** - Splashtop で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Splashtop** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://my.splashtop.com/login/sso`」と入力します。

1. Splashtop アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 TicketManager アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Splashtop のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Splashtop へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Splashtop]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-splashtop-sso"></a>Splashtop の SSO の構成

このセクションでは、[Splashtop Web ポータル](https://my.splashtop.com/login)から新しい SSO 方法を申請する必要があります。
1. Splashtop Web ポータルで、 **[Account info]\(アカウント情報\)**  /  **[Team]\(チーム\)** タブに移動し、下へスクロールして **[Single Sign On]\(シングル サインオン\)** セクションを見つけます。 次に、 **[Apply for new SSO method]\(新しい SSO 方法を申請する\)** をクリックします。

    ![[Single Sign On]\(シングル サインオン\) ページのスクリーンショット。ここで [Apply for new S S O method]\(新しい S S O 方法を申請する\) を選択できます。](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. 申請ウィンドウで、**SSO 名** を指定します。 たとえば、"New Azure" を指定し、IDP タイプとして **[Azure]** を選択します。Azure portal の Splashtop アプリケーションからコピーした **ログイン URL** と **Azure AD 識別子** を入力します。

    ![[Apply for S S O method]\(S S O 方法を申請する\) ページのスクリーンショット。ここで、名前やその他の情報を入力できます。](media/splashtop-tutorial/azure-sso-1.png)

1. 証明書情報については、Azure portal の Splashtop アプリケーションからダウンロードした証明書ファイルを右クリックしてメモ帳で編集してその内容をコピーし、 **[Download Certificate (Base64)]\(証明書のダウンロード (Base64)\)** フィールドに貼り付けます。

    ![証明書ファイルを選択してメモ帳で開く画面のスクリーンショット。](media/splashtop-tutorial/cert-1.png)
    ![証明書ファイルの内容を示すスクリーンショット。](media/splashtop-tutorial/cert-2.png)
    ![[Download Certificate]\(証明書のダウンロード\) テキスト ボックスのスクリーンショット。](media/splashtop-tutorial/azure-sso-2.png)

1. これで完了です。 **[Save]\(保存\)** をクリックします。その後、Splashtop SSO 検証チームから確認情報に関する連絡があり、SSO 方法がアクティブ化されます。

### <a name="create-splashtop-test-user"></a>Splashtop のテスト ユーザーの作成

1. SSO 方法をアクティブにした後、 **[Single Sign On]\(シングル サインオン\)** セクションで新しく作成された SSO 方法をオンにしてこれを有効にします。

    ![[Single Sign On]\(シングル サインオン\) ページのスクリーンショット。ここで新しい方法を有効にすることができます。](media/splashtop-tutorial/enable.png)

1. 新しく作成した SSO 方法を使用して、Splashtop チームにテスト ユーザー (たとえば、`B.Simon@contoso.com`) を招待します。

    ![[ユーザーの招待] ページのスクリーンショット。ここで新しい方法を選択できます。](media/splashtop-tutorial/invite.png)

1. また、既存の Splashtop アカウントを SSO アカウントに変更することもできます。[手順](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-)を参照してください。

1. これで完了です。 SSO アカウントを使用して、Splashtop Web ポータルまたは Splashtop Business アプリにログインできます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Splashtop] タイルをクリックすると、SSO を設定した Splashtop に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Splashtop を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Splashtop を保護する方法](/cloud-app-security/proxy-intro-aad)