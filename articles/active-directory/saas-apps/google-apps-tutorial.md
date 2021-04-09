---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Google Cloud (G Suite) Connector の統合 | Microsoft Docs
description: Azure Active Directory と Google Cloud (G Suite) Connector の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: jeedes
ms.openlocfilehash: a846899ba8f9b9e7c0d2e54744f5e5044ca7a2d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732057"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Google Cloud (G Suite) Connector の統合

このチュートリアルでは、Google Cloud (G Suite) Connector と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Google Cloud (G Suite) Connector を統合すると、次のことができます。

* Google Cloud (G Suite) Connector にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Google Cloud (G Suite) Connector に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

- Azure AD サブスクリプション。
- Google Cloud (G Suite) Connector でのシングル サインオン (SSO) が有効なサブスクリプション。
- Google Apps サブスクリプションまたは Google Cloud Platform サブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 このドキュメントは、新しいユーザー シングル サインオン エクスペリエンスを使用して作成されました。 まだ古いものを使用している場合、セットアップは異なります。 G-Suite アプリケーションのシングル サインオン設定で、新しいエクスペリエンスを有効にすることができます。 **[Azure AD, Enterprise applications]\(Azure AD Enterprise アプリケーション\)** に移動し、 **[Google Cloud (G Suite) Connector]** 、 **[シングル サインオン]** の順に選択して、 **[新しいエクスペリエンスをお試しください]** をクリックします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

1. **Q:この統合では、Google Cloud Platform と Azure AD の SSO 統合はサポートされていますか。**

    A:はい。 Google Cloud Platform と Google Apps は同じ認証プラットフォームを共有します。 そのため、GCP の統合を実行するには、Google Apps で SSO を構成する必要があります。

2. **Q:Chromebook とその他の Chrome デバイスは、Azure AD シングル サインオンと互換性がありますか。**
  
    A:はい。ユーザーは Azure AD の資格情報を使用して、Chromebook デバイスにサインインすることができます。 ユーザーに資格情報の入力を求めるメッセージが 2 回表示される場合がある理由については、[Google Cloud (G Suite) Connector のこちらのサポート記事](https://support.google.com/chrome/a/answer/6060880)をご覧ください。

3. **Q:シングル サインオンを有効にした場合、ユーザーは Google Classroom、GMail、Google Drive、YouTube などの Google 製品にサインインするために Azure AD 資格情報を使用できますか。**

    A:はい。[Google Cloud (G Suite) Connector](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) ごとに、組織で有効にするか無効にするかを選択します。

4. **Q:Google Cloud (G Suite) Connector ユーザーの一部のみを対象に、シングル サインオンを有効にすることはできますか。**

    A:いいえ。シングル サインオンを有効にすると、直ちにすべての Google Cloud (G Suite) Connector ユーザーが自分の Azure AD 資格情報での認証を要求されるようになります。 Google Cloud (G Suite) Connector は複数の ID プロバイダーの利用をサポートしていないため、Google Cloud (G Suite) Connector 環境の ID プロバイダーは Azure AD か Google であり、同時に両方を利用することはできません。

5. **Q:Windows でサインインしたユーザーは、パスワードの入力を求められることなく、自動的に Google Cloud (G Suite) Connector に対して認証されますか。**

    A:このシナリオを有効にするには、2 つのオプションがあります。 まず、ユーザーは [Azure Active Directory 参加](../devices/overview.md)を通じて Windows 10 デバイスにサインインできます。 また、ユーザーは、 [Active Directory フェデレーション サービス (AD FS)](../hybrid/plan-connect-user-signin.md) デプロイを通じて Azure AD へのシングル サインオンが有効になっているオンプレミスの Active Directory にドメイン参加している Windows デバイスにサインインすることもできます。 どちらのオプションでも、以下のチュートリアルの手順に従って、Azure AD と Google Cloud (G Suite) Connector との間のシングル サインオンを有効にする必要があります。

6. **Q:"無効な電子メール" というエラー メッセージが表示される場合はどうすればよいでしょうか。**

    A:このセットアップでは、ユーザーがサインインできるにはメール属性が必要です。 この属性は手動では設定できません。

    メール属性は、有効な Exchange ライセンスを持つユーザーに自動的に設定されます。 メールが有効になっていないユーザーの場合、アプリケーションでアクセス権を付与するにはこの属性を取得する必要があるため、このエラーを受け取ります。

    管理者アカウントで portal.office.com に移動し、管理センター、課金、サブスクリプションの順にクリックして、Microsoft 365 サブスクリプションを選択します。次に、ユーザーへの割り当てをクリックし、サブスクリプションを確認するユーザーを選択して、右側のペインでライセンスの編集をクリックします。

    Microsoft 365 ライセンスの割り当てが適用されるまでに数分かかる場合があります。 その後、user.mail 属性が自動的に設定されて、問題は解決します。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Google Cloud (G Suite) Connector では、**SP** Initiated SSO がサポートされます

* Google Cloud (G Suite) Connector では、[**自動化された** ユーザー プロビジョニング](./g-suite-provisioning-tutorial.md)がサポートされます

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>ギャラリーからの Google Cloud (G Suite) Connector の追加

Azure AD への Google Cloud (G Suite) Connector の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Google Cloud (G Suite) Connector を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Google Cloud (G Suite) Connector**」と入力します。
1. 結果のパネルから **[Google Cloud (G Suite) Connector]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Google Cloud (G Suite) Connector に対する Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Google Cloud (G Suite) Connector に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Google Cloud (G Suite) Connector の関連ユーザーとの間にリンク関係を確立する必要があります。

Google Cloud (G Suite) Connector に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Google Cloud (G Suite) Connector SSO の構成](#configure-google-cloud-g-suite-connector-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Google Cloud (G Suite) Connector のテスト ユーザーの作成](#create-google-cloud-g-suite-connector-test-user)** - Google Cloud (G Suite) Connector で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Google Cloud (G Suite) Connector** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**Gmail** を構成する場合は次の手順で行います。

    a. **[サインオン URL]** ボックスに、`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次の形式で URL を入力します。

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。 

    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

1. **[基本的な SAML 構成]** セクションで、**Google Cloud Platform** を構成する場合は次の手順で行います。

    a. **[サインオン URL]** ボックスに、`https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、次の形式で URL を入力します。
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. **[応答 URL]** ボックスに、 のパターンを使用して URL を入力します。 
    
    ```http
    https://www.google.com/acs
    https://www.google.com/a/<yourdomain.com>/acs
    ```

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 Google Cloud (G Suite) Connector ではシングル サインオン構成のエンティティ ID も識別子の値も提供されないため、 **[domain specific issuer]\(ドメイン固有の発行者\)** オプションをオフにすると、識別子の値は `google.com` になります。 **[domain specific issuer]\(ドメイン固有の発行者\)** オプションをオンにすると、値は `google.com/a/<yourdomainname.com>` になります。 **[domain specific issuer]\(ドメイン固有の発行者\)** オプションをオンまたはオフにするには、以降のチュートリアルで説明する「**Google Cloud (G Suite) Connector SSO の構成**」セクションに移動する必要があります。 詳細については、[Google Cloud (G Suite) Connector クライアント サポート チーム](https://www.google.com/contact/)にお問い合わせください。

1. Google Cloud (G Suite) Connector アプリケーションでは、特定の形式の SAML アサーションが求められます。そのため、カスタム属性マッピングを SAML トークン属性構成に追加する必要があります。 次のスクリーンショットはその例です。 **[一意のユーザー ID]** の既定値は **user.userprincipalname** ですが、Google Cloud (G Suite) Connector ではこれがユーザーのメール アドレスにマップされることが想定されています。 そのため、一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。

    ![image](common/default-attributes.png)


1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Google Cloud (G Suite) Connector のセットアップ]** セクションで、自分の要件に基づいて適切な URL をコピーします。

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

このセクションでは、Google Cloud (G Suite) Connector へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Google Cloud (G Suite) Connector]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Google Cloud (G Suite) Connector SSO の構成

1. ブラウザーで新しいタブを開き、管理者アカウントを使用して [Google Cloud (G Suite) Connector の管理コンソール](https://admin.google.com/)にサインインします。

2. **[セキュリティ]** をクリックします。 このリンクが表示されていない場合、画面下部の **[その他の設定]** に隠れていることがあります。

    ![Click Security.][10]

3. **[セキュリティ]** ページで、 **[シングル サインオン (SSO) の設定]** をクリックします。

    ![Click SSO.][11]

4. 次の構成の変更を実行します。

    ![Configure SSO][12]

    a. **[Setup SSO with third-party identity provider]\(サード パーティの ID プロバイダーで SSO を設定する\)** を選択します。

    b. Google Cloud (G Suite) Connector の **[Sign-in page URL]\(サインイン ページの URL\)** フィールドに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    c. Google Cloud (G Suite) Connector の **[Sign-out page URL]\(サインアウト ページの URL\)** フィールドに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    > [!NOTE]
    > Google Cloud (G Suite) は、SAML ログアウト プロトコルに基づいています。 そのため、 **[Sign-out page URL]\(サインアウト ページの URL\)** フィールドでは、その値として SAML ログアウト URL (つまり、ログイン URL) を使用する必要があります。

    d. Google Cloud (G Suite) Connector の **[Verification certificate]\(検証証明書\)** に、Azure portal からダウンロードした証明書をアップロードします。   

    e. 上記の Azure AD の **[基本的な SAML 構成]** セクションに記載されている注意事項に従って、 **[Use a domain specific issuer]\(ドメイン固有の発行者を使用する\)** オプションをオンまたはオフにします。

    f. Google Cloud (G Suite) Connector の **[Change password URL]\(パスワードの URL の変更\)** フィールドに、Azure portal からコピーした **[パスワードの URL の変更]** の値を貼り付けます。

    g. **[保存]** をクリックします。

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Google Cloud (G Suite) Connector のテスト ユーザーの作成

このセクションの目的は、B.Simon という[ユーザーを Google Cloud (G Suite) Connector で作成する](https://support.google.com/a/answer/33310?hl=en)ことです。 Google Cloud (G Suite) Connector で手動で作成されたユーザーは、Microsoft 365 のログイン資格情報を使用してサインインできるようになります。

Google Cloud (G Suite) Connector では、自動ユーザー プロビジョニングがサポートされています。 自動ユーザープロビジョニングを構成するには、まず [Google Cloud (G Suite) Connector を構成し、自動ユーザー プロビジョニングに対応させる](./g-suite-provisioning-tutorial.md)必要があります。

> [!NOTE]
> シングル サインオンをテストする前に Azure AD でのプロビジョニングが有効にされていない場合は、既に Google Cloud (G Suite) Connector にユーザーが存在していることを確認してください。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Google サポート チーム](https://www.google.com/contact/)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Google Cloud (G Suite) Connector のサインオン URL にリダイレクトされます。 

* Google Cloud (G Suite) Connector のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Google Cloud (G Suite) Connector] タイルをクリックすると、Google Cloud (G Suite) Connector のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Google Cloud (G Suite) Connector を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
