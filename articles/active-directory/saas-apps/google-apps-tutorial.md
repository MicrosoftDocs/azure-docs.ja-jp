---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と G Suite の統合 | Microsoft Docs
description: Azure Active Directory と G Suite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e4449ac3519757bb9670d2d7fec53cb5f3ce152
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948291"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と G Suite の統合

このチュートリアルでは、G Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と G Suite を統合すると、次のことができます。

* G Suite にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して G Suite に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

- Azure AD サブスクリプション。
- G Suite でのシングル サインオン (SSO) が有効なサブスクリプション。
- Google Apps サブスクリプションまたは Google Cloud Platform サブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 このドキュメントは、新しいユーザー シングル サインオン エクスペリエンスを使用して作成されました。 まだ古いものを使用している場合、セットアップは異なります。 G-Suite アプリケーションのシングル サインオン設定で、新しいエクスペリエンスを有効にすることができます。 **[Azure AD, Enterprise applications]\(Azure AD Enterprise アプリケーション\)** に移動し、 **[G Suite]** を選択し、 **[Single Sign-on]\(シングル サインオン\)** を選択し、 **[Try out our new experience]\(新しいエクスペリエンスを試す\)** をクリックします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

1. **Q:この統合では、Google Cloud Platform と Azure AD の SSO 統合はサポートされていますか。**

    A:はい。 Google Cloud Platform と Google Apps は同じ認証プラットフォームを共有します。 そのため、GCP の統合を実行するには、Google Apps で SSO を構成する必要があります。

2. **Q:Chromebook とその他の Chrome デバイスは、Azure AD シングル サインオンと互換性がありますか。**
  
    A:はい。ユーザーは Azure AD の資格情報を使用して、Chromebook デバイスにサインインすることができます。 ユーザーに資格情報の入力を求めるメッセージが 2 回表示される場合がある理由については、[G Suite のこちらのサポート記事](https://support.google.com/chrome/a/answer/6060880)をご覧ください。

3. **Q:シングル サインオンを有効にした場合、ユーザーは Google Classroom、GMail、Google Drive、YouTube などの Google 製品にサインインするために Azure AD 資格情報を使用できますか。**

    A:はい。[G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) ごとに、組織で有効にするか無効にするかを選択します。

4. **Q:一部の G Suite ユーザーに対してのみ、シングル サインオンを有効にすることはできますか。**

    A:いいえ。シングル サインオンを有効にすると、直ちにすべての G Suite ユーザーが Azure AD の資格情報での認証を要求されるようになります。 G Suite では複数の ID プロバイダーをサポートしていないため、G Suite 環境の ID プロバイダーは Azure AD か Google であり、同時に両方を設定することはできません。

5. **Q:Windows でサインインしたユーザーは、パスワードの入力を求められることなく、G Suite に対して自動的に認証されますか。**

    A:このシナリオを有効にするには、2 つのオプションがあります。 まず、ユーザーは [Azure Active Directory 参加](../device-management-introduction.md)を通じて Windows 10 デバイスにサインインできます。 また、ユーザーは、 [Active Directory フェデレーション サービス (AD FS)](../hybrid/plan-connect-user-signin.md) デプロイを通じて Azure AD へのシングル サインオンが有効になっているオンプレミスの Active Directory にドメイン参加している Windows デバイスにサインインすることもできます。 どちらのオプションでも、以下のチュートリアルの手順に従って、Azure AD と G Suite の間のシングル サインオンを有効にする必要があります。

6. **Q:"無効な電子メール" というエラー メッセージが表示される場合はどうすればよいでしょうか。**

    A:このセットアップでは、ユーザーがサインインできるにはメール属性が必要です。 この属性は手動では設定できません。

    メール属性は、有効な Exchange ライセンスを持つユーザーに自動的に設定されます。 メールが有効になっていないユーザーの場合、アプリケーションでアクセス権を付与するにはこの属性を取得する必要があるため、このエラーを受け取ります。

    管理者アカウントで portal.office.com に移動し、管理センターで課金とサブスクリプションをクリックし、Office 365 サブスクリプションを選択し、ユーザーへの割り当てをクリックし、右側のウィンドウでサブスクリプションを確認するユーザーを選択し、ライセンスの編集をクリックします。

    O365 ライセンスの割り当てが適用されるまで、数分かかる場合があります。 その後、user.mail 属性が自動的に設定されて、問題は解決します。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* G Suite では、**SP** によって開始される SSO がサポートされます

* G Suite では、[**自動化された**ユーザー プロビジョニング](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)がサポートされます

## <a name="adding-g-suite-from-the-gallery"></a>ギャラリーからの G Suite の追加

Azure AD への G Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に G Suite を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**G Suite**」と入力します。
1. 結果のパネルから **[G Suite]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>G Suite の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、G Suite に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと G Suite の関連ユーザーとの間にリンク関係を確立する必要があります。

G Suite に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[G Suite SSO の構成](#configure-g-suite-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[G Suite テスト ユーザーの作成](#create-g-suite-test-user)** - G Suite で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **G Suite** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**Gmail** を構成する場合は次の手順で行います。

    a. **[サインオン URL]** ボックスに、`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次の形式で URL を入力します。

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

1. **[基本的な SAML 構成]** セクションで、**Google Cloud Platform** を構成する場合は次の手順で行います。

    a. **[サインオン URL]** ボックスに、`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次の形式で URL を入力します。
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 G Suite ではシングル サインオン構成のエンティティ ID または識別子の値が提供されないため、 **[domain specific issuer]\(ドメイン固有の発行者\)** オプションをオフにすると、識別子の値は `google.com` になります。 **[domain specific issuer]\(ドメイン固有の発行者\)** オプションをオンにすると、値は `google.com/a/<yourdomainname.com>` になります。 **[domain specific issuer]\(ドメイン固有の発行者\)** オプションをオンまたはオフにするには、チュートリアルの後半で説明する「**G Suite SSO の構成**」セクションに移動する必要があります。 詳細については、[G Suite クライアント サポート チーム](https://www.google.com/contact/)にお問い合わせください。

1. G Suite アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 **[一意のユーザー ID]** の既定値は **user.userprincipalname** ですが、G Suite ではこれがユーザーのメール アドレスにマップされることが想定されています。 そのため、一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。

    ![image](common/edit-attribute.png)

1. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

    | Name | ソース属性 |
    | ---------------| --------------- |
    | 一意のユーザー ID | User.mail |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[G Suite のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に G Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[G Suite]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-g-suite-sso"></a>G Suite SSO の構成

1. ブラウザーで新しいタブを開き、管理者アカウントを使用して、[G Suite 管理コンソール](https://admin.google.com/)にサインインします。

2. **[セキュリティ]** をクリックします。 このリンクが表示されていない場合、画面下部の **[その他の設定]** に隠れていることがあります。

    ![Click Security.][10]

3. **[セキュリティ]** ページで、 **[シングル サインオン (SSO) の設定]** をクリックします。

    ![Click SSO.][11]

4. 次の構成の変更を実行します。

    ![Configure SSO][12]

    a. **[Setup SSO with third-party identity provider]\(サード パーティの ID プロバイダーで SSO を設定する\)** を選択します。

    b. G Suite の **[サインイン ページの URL]** フィールドに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. G Suite の **[サインアウト ページの URL]** フィールドに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    d. G Suite の **[パスワードの URL の変更]** フィールドに、Azure Portal からコピーした **[パスワードの URL の変更]** の値を貼り付けます。

    e. G Suite の **[検証証明書]** に、Azure Portal からダウンロードした証明書をアップロードします。

    f. 上記の Azure AD の **[基本的な SAML 構成]** セクションに記載されている注意事項に従って、 **[Use a domain specific issuer]\(ドメイン固有の発行者を使用する\)** オプションをオンまたはオフにします。

    g. **[変更を保存]** をクリックします。

### <a name="create-g-suite-test-user"></a>G Suite テスト ユーザーの作成

このセクションの目的は、G Suite ソフトウェアで B.Simon というユーザーを作成することです。 G Suite では、自動プロビジョニングがサポートされており、これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 G Suite ソフトウェアにユーザーがまだ存在しない場合は、G Suite ソフトウェアにアクセスしようとしたときに新しいユーザーが作成されます。

> [!NOTE]
> シングル サインオンをテストする前に Azure AD でのプロビジョニングが有効にされていない場合は、既に G Suite にユーザーが存在していることを確認してください。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Google サポート チーム](https://www.google.com/contact/)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [G Suite] タイルをクリックすると、SSO を設定した G Suite に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [[ユーザー プロビジョニングの構成]](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
- [Azure AD で G Suite を試す](https://aad.portal.azure.com/)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png