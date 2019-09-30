---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Zoom の統合 | Microsoft Docs
description: Azure Active Directory と Zoom 間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f9d727154adf0a2099d7a9144c109cef9c91238
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743967"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Zoom の統合

このチュートリアルでは、Zoom と Azure Active Directory (Azure AD) を統合する方法について説明します。 Zoom と Azure AD を統合すると、次のことができます。

* Zoom にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Zoom に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Zoom のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Zoom では、**SP** Initiated SSO がサポートされます。 
* Zoom では、[**自動化された**ユーザー プロビジョニング](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)がサポートされます。

## <a name="adding-zoom-from-the-gallery"></a>ギャラリーからの Zoom の追加

Azure AD への Zoom の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zoom を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zoom**」と入力します。
1. 結果パネルで **[Zoom]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Zoom の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zoom に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Zoom の関連ユーザーとの間にリンク関係を確立する必要があります。

Zoom で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Zoom SSO の構成](#configure-zoom-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zoom テスト ユーザーの作成](#create-zoom-test-user)** - Zoom で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Zoom** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.zoom.us`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`<companyname>.zoom.us`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 この値を取得するには、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Zoom のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

> [!NOTE]
> Azure AD でロールを構成する方法については、[エンタープライズ アプリケーション用の SAML トークン内に発行されるロール要求を構成する方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)に関するページを参照してください。

> [!NOTE]
> Zoom は、グループ要求が SAML ペイロードに含まれていることを想定している場合があります。 グループを作成した場合は、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/)にグループ情報を連絡し、Zoom クライアント サポート チーム側で、そのグループ情報を構成できるようにしてください。 また、Zoom クライアント サポート チーム側でオブジェクト ID を構成できるよう、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/)にオブジェクト ID を提供する必要もあります。 オブジェクト ID の取得については、[Azure での Zoom の構成](https://support.zoom.us/hc/articles/115005887566)に関するページを参照してください。

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

このセクションでは、B.Simon に Zoom へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Zoom]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-zoom-sso"></a>Zoom SSO の構成

1. 別の Web ブラウザー ウィンドウで、Zoom 企業サイトに管理者としてサインインします。

2. **[シングル サインオン]** タブをクリックします。

    ![[Single sign-on] \(シングル サインオン\) タブ](./media/zoom-tutorial/ic784700.png "シングル サインオン")

3. **[セキュリティ制御]** タブをクリックし、 **[シングル サインオン]** に移動します。

4. [Single Sign-On] セクションで、次の手順に従います。

    ![[Single sign-on] \(シングル サインオン\) セクション](./media/zoom-tutorial/ic784701.png "シングル サインオン")

    a. **[Sign-in page URL]\(サインイン ページの URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    b. **[サインアウト ページの URL]** 値については、Azure portal に移動し、左側の **[Azure Active Directory]** をクリックしてから **[アプリの登録]** に移動する必要があります。

    ![Azure Active Directory のボタン](./media/zoom-tutorial/appreg.png)

    c. **[エンドポイント]** をクリックします

    ![[エンドポイント] ボタン](./media/zoom-tutorial/endpoint.png)

    d. **[SAML-P サインアウト エンドポイント]** をコピーして **[サインアウト ページの URL]** テキストボックスに貼り付けます。

    ![エンドポイントのコピー ボタン](./media/zoom-tutorial/endpoint1.png)

    e. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[ID プロバイダー証明書]** ボックスに貼り付けます。

    f. **[Issuer]\(発行者\)** テキストボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。 

    g. **[Save]** をクリックします。

    > [!NOTE]
    > 詳しくは、Zoom のドキュメント [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566) をご覧ください。

### <a name="create-zoom-test-user"></a>Zoom テスト ユーザーの作成

このセクションの目的は、Zoom で B.Simon というユーザーを作成することです。 Zoom では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)をご覧ください。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/)に問い合わせる必要があります

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Zoom] タイルをクリックすると、SSO を設定した Zoom に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Zoom を試す](https://aad.portal.azure.com/)
