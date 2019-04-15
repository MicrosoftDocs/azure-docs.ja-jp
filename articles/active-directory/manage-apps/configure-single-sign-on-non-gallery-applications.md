---
title: シングル サインオン - 非ギャラリー アプリケーション - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) の非ギャラリー アプリケーションへのシングル サインオンを構成する
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 01/08/2019
ms.author: celested
ms.reviewer: asmalser,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f003ec847ab3777a2174a1078a2d07eb012bb34
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484100"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-azure-active-directory"></a>Azure Active Directory の非ギャラリー アプリケーションへのシングル サインオンを構成する

この記事では、管理者が *コードを記述せずに*Azure Active Directory アプリケーション ギャラリーに存在しないアプリケーションへのシングル サインオンを構成できるようにする機能について説明します。 コードを使用してカスタム アプリケーションと Azure AD を統合する方法に関する開発者向けガイダンスをお探しの場合は、「 [Azure AD の認証シナリオ](../develop/authentication-scenarios.md)」を参照してください。

[この記事](what-is-single-sign-on.md)で説明されているように、Azure Active Directory アプリケーション ギャラリーには、Azure Active Directory によるシングル サインオンの形式をサポートすることがわかっているアプリケーションの一覧が表示されます。 (組織内の IT スペシャリストまたはシステム インテグレーターとして) 接続するアプリケーションを見つけたら、Azure Portal に示される詳細な手順に従って、シングル サインオンを有効にできます。

使用許諾契約書に従ってこれらの機能を使用することもできます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。 

- SAML 2.0 ID プロバイダーをサポートする任意のアプリケーションのセルフサービス統合 (SP または IdP によって開始)
-  [パスワードベースの SSO](what-is-single-sign-on.md#password-based-sso)
- ユーザー プロビジョニング用の SCIM プロトコルを使用するアプリケーションのセルフサービス接続 ([ここで説明](use-scim-to-provision-users-and-groups.md))
- [Office 365 アプリ ランチャー](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)または [Azure AD アクセス パネル](what-is-single-sign-on.md#linked-sso)での任意のアプリケーションへのリンクの追加機能

これには、使用するが Azure AD アプリケーション ギャラリーにはまだ追加されていない SaaS アプリケーションだけでなく、組織がクラウドまたはオンプレミスで制御対象のサーバーにデプロイしたサードパーティの Web アプリケーションも含まれる場合があります。

これらの機能は、 *アプリ統合テンプレート*とも呼ばれ、SAML、SCIM、またはフォーム ベース認証をサポートするアプリに標準ベースの接続ポイントを提供し、さまざまなアプリケーションとの互換性のために柔軟なオプションや設定を用意しています。 

## <a name="adding-an-unlisted-application"></a>一覧にないアプリケーションの追加
アプリケーション統合テンプレートを使用してアプリケーションを接続するには、Azure Active Directory 管理者アカウントを使用して Azure Portal にサインインします。 **[Active Directory] > [エンタープライズ アプリケーション] > [新しいアプリケーション] > [ギャラリー以外のアプリケーション]** セクションに移動し、**[追加]** を選択し、**[ギャラリーからアプリケーションを追加します]** を選択します。

  ![[アプリケーションの追加]](./media/configure-single-sign-on-non-gallery-applications/customapp1.png)

アプリ ギャラリーで、一覧にないアプリを追加するには、検索結果に表示されている **[ギャラリー以外のアプリケーション]** タイルを選択します (必要なアプリが見つからない場合)。 アプリケーションの [名前] を入力すると、シングル サインオン オプションとその動作を構成できます。 

**ポップ ヒント**:ベスト プラクティスとして、検索機能を使用して、アプリケーションがアプリケーション ギャラリーに既に存在するかどうかを確認します。 アプリが検出され、その説明でシングル サインオンに言及している場合、そのアプリケーションはフェデレーション シングル サインオンで既にサポートされています。

  ![Search](./media/configure-single-sign-on-non-gallery-applications/customapp2.png)

この方法でアプリケーションを追加すると、事前に統合されたアプリケーションに似たエクスペリエンスが提供されます。 開始するには、**[シングル サインオンの構成]** を選択するか、アプリケーションの左側のナビゲーション メニューの **[シングル サインオン]** をクリックします。 次の画面は、シングル サインオンを構成するためのオプションを示しています。 これらのオプションについて、この記事の以降のセクションで説明します。
  
![構成オプション](./media/configure-single-sign-on-non-gallery-applications/customapp3.png)

## <a name="saml-based-single-sign-on"></a>SAML ベースのシングル サインオン
アプリケーションの SAML ベースの認証を構成するには、このオプションを選択します。 アプリケーションが SAML 2.0 をサポートしている必要があります。 先に進む前に、アプリケーションの SAML 機能の使用方法に関する情報を収集しておく必要があります。 アプリケーションと Azure AD 間のシングル サインオンを構成するには、以下のセクションを完了します。

### <a name="enter-basic-saml-configuration"></a>基本的な SAML 構成を入力する

Azure AD を設定するには、基本的な SAML 構成を入力します。 値を手動で入力するか、メタデータ ファイルをアップロードしてフィールドの値を抽出できます。

  ![Litware ドメインと URL](./media/configure-single-sign-on-non-gallery-applications/customapp4.png)

- **サインオン URL (SP によって開始される場合のみ)** - このアプリケーションにサインインするときにユーザーがアクセスする場所。 サービス プロバイダーによって開始されるシングル サインオンを実行するようにアプリケーションが構成されている場合は、ユーザーがこの URL に移動すると、認証とログオンを行うために、そのユーザーはサービス プロバイダーによって Azure AD にリダイレクトされます。 このフィールドに URL が入力されている場合、Azure AD はその URL を使用して Office 365 と Azure AD アクセス パネルからアプリケーションを起動します。 このフィールドへの入力が省略されている場合、Azure AD は、アプリケーションが Office 365、Azure AD アクセス パネル、または Azure AD シングル サインオン URL ([ダッシュボード] タブからコピー可能) から起動されたときに、ID プロバイダーによって開始されるサインオンを実行します。
- **識別子** - シングル サインオンが構成されるアプリケーションを一意に識別する必要があります。 この値は、アプリケーションによって送信された AuthRequest (SAML 要求) の Issuer 要素として見つけることができます。 また、この値はアプリケーションによって提供される SAML メタデータ内に **Entity ID** として表示されます。 SAML トークンの Entity ID または Audience 値の詳細については、アプリケーションの SAML ドキュメントを確認してください。 

    次に示すのは、アプリケーションが Azure AD に送信した SAML 要求で Identifier または Issuer がどのように表示されるかの例です。

    ```
    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>
    ```

- **[応答 URL]** - 応答 URL は、アプリケーションが SAML トークンを受け取ることになっている場所です。 これは Assertion Consumer Service (ACS) URL とも呼ばれています。 SAML トークンの応答 URL または ACS URL の詳細については、アプリケーションの SAML ドキュメントを確認してください。 

    複数の応答 URL を構成するには、次の PowerShell スクリプトを使用できます。

    ```powershell
    $sp = Get-AzureADServicePrincipal -SearchString "<Exact App  name>"
    $app = Get-AzureADApplication -SearchString "<Exact app name>"
    Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls "<ReplyURLs>"
    Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls "<ReplyURLs>"
    ```

詳細については、[Azure Active Directory (Azure AD) がサポートする SAML 2.0 の認証要求と応答](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)に関する記事を参照してください。


### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>SAML トークンで発行された要求を確認またはカスタマイズする

アプリケーションに対するユーザーの認証時に、Azure AD は、一意に識別するユーザーに関する情報 (または要求) を含む SAML トークンをアプリに発行します。 既定では、ユーザーのユーザー名、電子メール アドレス、名、および姓が含まれます。 

**[属性]** タブで、アプリケーションに SAML トークンで送信された要求を表示または編集できます。

  ![属性](./media/configure-single-sign-on-non-gallery-applications/customapp7.png)

SAML トークンで発行された要求を編集する必要がある理由は、2 つあります。

- アプリケーションが、別の要求 URI または要求値のセットを必要とするように記述されている。
- NameIdentifier 要求が、Azure Active Directory に格納されているユーザー名 (ユーザー プリンシパル名) 以外の何かであることを必要とする方法で、アプリケーションがデプロイされている。 

詳細については、「[エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](./../develop/../develop/active-directory-saml-claims-customization.md)」を参照してください。 



### <a name="review-certificate-expiration-data-status-and-email-notification"></a>証明書の有効期限データ、状態、および電子メール通知を確認する

ギャラリーまたはギャラリー以外のアプリケーションを作成すると、Azure AD によって、有効期限が作成日の 3 年後であるアプリケーション固有の証明書が作成されます。 この証明書を使用して、Azure AD とアプリケーション間の信頼を設定する必要があります。 証明書の形式の詳細については、アプリケーションの SAML ドキュメントを参照してください。 

Azure AD から、Base64 または未加工形式で証明書をダウンロードできます。 さらに、証明書は、アプリケーションのメタデータ XML ファイルをダウンロードするか、アプリのフェデレーション メタデータ URL を使用して取得できます。

  ![証明書](./media/configure-single-sign-on-non-gallery-applications/certificate.png)

証明書に以下が含まれていることを確認します。

- 期待どおりの有効期限。 最大 3 年間の有効期限を構成できます。
- アクティブな状態。 状態が非アクティブの場合は、アクティブに変更します。 状態を変更するには、**[アクティブ]** をオンにし、構成を保存します。 
- 適切な通知電子メール。 アクティブな証明書の有効期限が近づくと、Azure AD は、このフィールドに構成されている電子メール アドレスに通知を送信します。  

詳細については、「[Manage certificates for federated single sign-on in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md)」(Azure Active Directory でフェデレーション シングル サインオンの証明書を管理する) を参照してください。

### <a name="set-up-target-application"></a>ターゲット アプリケーションを設定する

シングル サインオン用にアプリケーションを構成するには、アプリケーションのドキュメントを探します。 ドキュメントを見つけるには、[SAML ベースのサインオンの構成] ページを最後までスクロールし、**[\<アプリケーション名> の構成]** をクリックします。 

必要な値は、アプリケーションによって異なります。 詳細については、アプリケーションの SAML ドキュメントを参照してください。 サインオン サービスとサインアウト サービスの URL の両方が、同じエンドポイント (Azure AD のインスタンスの SAML 要求処理エンドポイント) に解決されます。 SAML エンティティ ID は、アプリケーションに発行される SAML トークンに Issuer として表示される値です。


### <a name="assign-users-and-groups-to-your-saml-application"></a>SAML アプリケーションにユーザーとグループを割り当てる

SAML ベースの ID プロバイダーとして Azure AD を使用するようにアプリケーションを構成すれば、テストの準備はほぼできています。 セキュリティ コントロールとして、Azure AD は、Azure AD がユーザーにアクセス権を付与している場合を除いて、ユーザーのアプリケーションへのサインインを許可するトークンを発行しません。 ユーザーには、直接、またはメンバーシップを介してアクセス権を付与できます。 

アプリケーションにユーザーまたはグループを割り当てるには、 **[ユーザーの割り当て]** ボタンをクリックします。 割り当てるユーザーまたはグループを選択してから、 **[割り当て]** ボタンをクリックします。

  ![ユーザーの割り当て](./media/configure-single-sign-on-non-gallery-applications/customapp6.png)

ユーザーを割り当てると、Azure AD によるユーザーへのトークンの発行が可能になります。 さらに、ユーザーのアクセス パネルに、このアプリケーションのタイルも表示されます。 アプリケーション タイルは、ユーザーが Office 365 を使用している場合、Office 365 アプリケーション ランチャーでも表示されます。 

> [!NOTE] 
> アプリケーションの **[構成]** タブにある **[ロゴのアップロード]** ボタンを使用して、アプリケーションのタイル ロゴをアップロードできます。 


### <a name="test-the-saml-application"></a>SAML アプリケーションをテストする

SAML アプリケーションをテストする前に、Azure AD でアプリケーションを設定し、アプリケーションにユーザーまたはグループを割り当てる必要があります。 SAML アプリケーションをテストするには、[Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](../develop/howto-v1-debug-saml-sso-issues.md)に関するページを参照してください。

## <a name="password-single-sign-on"></a>パスワード シングル サインオン

HTML サインイン ページがある Web アプリケーションの [パスワード ベースのシングル サインオン](what-is-single-sign-on.md) を構成するには、このオプションを選択します。 パスワード ベースの SSO (パスワード保管ともいう) では、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、たとえば、組織のソーシャル メディア アプリ アカウントなどに、1 つのアカウントを共有する必要があるシナリオにも便利です。 

**[次へ]** を選択すると、アプリケーションの Web ベースのサインイン ページの URL を入力するように求められます。 このページには、ユーザー名とパスワードの入力フィールドが含まれている必要があることに注意してください。 入力すると、Azure AD が、ユーザー名とパスワードを入力するためのサインイン ページを解析するプロセスを開始します。 プロセスが失敗した場合は、フィールドを手動でキャプチャできるようにするブラウザー拡張機能 (Internet Explorer、Chrome、または Firefox が必要) をインストールする代替プロセスが示されます。

サインイン ページをキャプチャしたら、ユーザーとグループを割り当てることができ、資格情報ポリシーは通常の [パスワード SSO アプリ](what-is-single-sign-on.md)の場合と同様に設定できます。

> [!NOTE] 
> アプリケーションの **[構成]** タブにある **[ロゴのアップロード]** ボタンを使用して、アプリケーションのタイル ロゴをアップロードできます。 
>

## <a name="existing-single-sign-on"></a>既存のシングル サインオン
組織の Azure AD アクセス パネルまたは Office 365 ポータルにアプリケーションへのリンクを追加するには、このオプションを選択します。 これを使用して、認証用に Azure AD の代わりに現在 Active Directory フェデレーション サービス (AD FS) (または他のフェデレーション サービス) を使用しているカスタム Web アプリへのリンクを追加することができます。 または、ユーザーのアクセス パネルに表示するだけの特定の SharePoint ページまたは他の Web ページにディープ リンクを追加することもできます。 

**[次へ]** を選択すると、リンクするアプリケーションの URL を入力するように求められます。 入力したら、ユーザーとグループをアプリケーションに割り当てることができます。これにより、アプリケーションが [Office 365 アプリ ランチャー](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/)またはユーザーの [Azure AD アクセス パネル](end-user-experiences.md)に表示されるようになります。

> [!NOTE] 
> アプリケーションの **[構成]** タブにある **[ロゴのアップロード]** ボタンを使用して、アプリケーションのタイル ロゴをアップロードできます。 
>

## <a name="related-articles"></a>関連記事
- [Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)
- [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](../develop/howto-v1-debug-saml-sso-issues.md)

