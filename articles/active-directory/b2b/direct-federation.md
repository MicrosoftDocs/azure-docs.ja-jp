---
title: B2B 用の ID プロバイダーとの直接フェデレーションの設定 - Azure Active Directory | Microsoft Docs
description: ご自身の Azure AD アプリにゲストがサインインできるようにするために、SAML または WS-Fed ID プロバイダーと直接連携します
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4dadc68e78fbaa979751d5bcd04ef481c3ab886
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544637"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>ゲスト ユーザーのための AD FS およびサード パーティ プロバイダーとの直接フェデレーション (プレビュー)
|     |
| --- |
| 直接フェデレーションは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

この記事では、B2B コラボレーションのために別の組織との直接フェデレーションを設定する方法について説明します。 任意の組織の ID プロバイダー (IdP) が SAML 2.0 または WS-Fed プロトコルをサポートしていれば、その組織との直接フェデレーションを設定することができます。
パートナーの IdP との直接フェデレーションを設定すると、そのドメインに属する新しいゲスト ユーザーがご自身の Azure AD テナントに、そのユーザー自身の組織アカウント (IdP が管理する) を使用してサインインし、コラボレーションを開始できます。 ゲスト ユーザーが別個の Azure AD アカウントを作成する必要はありません。
> [!NOTE]
> 直接フェデレーションのゲスト ユーザーは、テナント コンテキストが含まれたリンク (例: `https://myapps.microsoft.com/?tenantid=<tenant id>` または `https://portal.azure.com/<tenant id>`、または検証済みのドメインの場合は `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`) を使用してサインインする必要があります。 アプリケーションとリソースへの直接リンクも、テナント コンテキストが含まれている限り機能します。 直接フェデレーションのユーザーは現在、テナント コンテキストが含まれていない共通エンドポイントを使用してサインインできません。 たとえば、`https://myapps.microsoft.com`、`https://portal.azure.com`、または `https://teams.microsoft.com` を使用するとエラーが発生します。
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>ゲスト ユーザーが直接フェデレーションを使用で認証されるタイミング
組織との直接フェデレーションを設定した後、招待した新しいゲスト ユーザーは、直接フェデレーションを使用して認証されます。 重要なこととして、直接フェデレーションを設定しても、ご自身からの招待を既に利用済みのゲスト ユーザーに対する認証方法は変更されないことに注意してください。 次に例をいくつか示します。
 - ゲスト ユーザーがご自身からの招待を既に利用済みであり、その後にゲスト ユーザーの組織との直接フェデレーションを設定した場合、それらのゲスト ユーザーは、直接フェデレーション設定前に使用していたのと同じ認証方法を引き続き使用することになります。
 - 取引先組織との直接フェデレーションを設定してゲスト ユーザーを招待し、それ以降にその取引先組織が Azure AD に移行した場合、ご自身のテナント内に直接フェデレーション ポリシーが存在する限り、既に招待を利用済みのゲスト ユーザーは、引き続き直接フェデレーションを使用します。
 - 取引先組織との直接フェデレーションを削除すると、直接フェデレーションを現在使用しているすべてのゲスト ユーザーがサインインできなくなります。

これらのどのシナリオでも、ご自身のディレクトリからゲスト ユーザー アカウントを削除してから再招待することで、ゲスト ユーザーの認証方法を更新できます。

直接フェデレーションは、contoso.com や fabrikam.com などのドメイン名前空間に関連付けられます。 組織では、AD FS またはサード パーティの IdP との直接フェデレーション構成を確立するときに、これらの Idp に 1 つまたは複数のドメイン名前空間を関連付けます。 

## <a name="end-user-experience"></a>エンド ユーザー エクスペリエンス 
直接フェデレーションを使用すると、ご自身の Azure AD テナントに、ゲスト ユーザーが各自の組織アカウントを使用してサインインします。 共有リソースへのアクセス時にサインインを求められると、直接フェデレーションのユーザーは IdP にリダイレクトされます。 サインインが成功した後、Azure AD に戻ってリソースにアクセスします。 直接フェデレーションのユーザーの更新トークン有効期間は 12 時間です。これは、Azure AD での[パススルー更新トークンの既定の長さ](../develop/active-directory-configurable-token-lifetimes.md#exceptions)です。 連携した IdP で SSO が有効な場合、ユーザーは SSO を体験することになり、初回認証後にサインインを求めるメッセージは表示されません。

## <a name="limitations"></a>制限事項

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD での DNS 検証済みドメイン
直接フェデレーションは、Azure AD での DNS 検証の***対象外***であるドメインに対してのみ使用できます。 直接フェデレーションは、アンマネージド (電子メールで検証済み、または "バイラル") の Azure AD テナントに対してのみ使用できます。理由は、それらが DNS で検証されないためです。
### <a name="authentication-url"></a>認証 URL
直接フェデレーションをポリシーで使用できるのは、認証 URL のドメインがターゲット ドメインと一致する場合か、認証 URL がこれらの許可されている ID プロバイダーのうちの 1 つである場合のみです (この一覧は変更される場合があります)。
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

たとえば、**fabrikam.com** に対して直接フェデレーションを設定する場合、`https://fabrikam.com/adfs` という認証 URL は検証に合格します。 同じドメイン内のホストも合格します (たとえば `https://sts.fabrikam.com/adfs`)。 ただし、同じドメインの `https://fabrikamconglomerate.com/adfs` または `https://fabrikam.com.uk/adfs` という認証 URL は、合格しません。

### <a name="signing-certificate-renewal"></a>署名証明書の更新
ID プロバイダーの設定でメタデータ URL を指定した場合、署名証明書が有効期限切れになると、Azure AD によって自動的に更新されます。 ただし、証明書が有効期限切れになる前に何らかの理由でローテーションされた場合、またはメタデータ URL を指定しなかった場合には、Azure AD による更新はできません。 この場合、署名証明書を手動で更新する必要があります。
## <a name="frequently-asked-questions"></a>よく寄せられる質問
### <a name="can-i-set-up-direct-federation-with-an-unmanaged-email-verified-tenant"></a>アンマネージド (電子メールで検証済み) のテナントとの直接フェデレーションを設定することはできますか。 
はい。 ドメインが検証されておらず、テナントで[管理者の引き継ぎ](../users-groups-roles/domains-admin-takeover.md)が実施されていない場合は、直接フェデレーションを設定することができます。 アンマネージドまたは電子メールで検証済みのテナントは、ユーザーが B2B の招待を利用したとき、または現時点で存在しないドメインを使用して Azure AD のセルフサービス サインアップを実行したときに作成されます。 これらのドメインとの直接フェデレーションを設定することができます。 Azure portal 上で、または PowerShell を使用して、DNS で検証済みのドメインとの直接フェデレーションを設定しようとすると、エラーが表示されます。
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>直接フェデレーションと電子メールのワンタイム パスコード認証の両方が有効な場合、どちらの方法が優先されますか。
取引先組織との直接フェデレーションが確立すると、その組織に属する新しいゲスト ユーザーに対して、電子メールのワンタイム パスコード認証よりも直接フェデレーションが優先されます。 直接フェデレーションを設定する前に、ゲスト ユーザーがワンタイム パスコード認証を使用して招待を利用した場合、それらのゲスト ユーザーは引き続きワンタイム パスコード認証を使用することになります。 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>直接フェデレーションは、部分的に同期されたテナントに起因するサインインの問題に対応していますか。
いいえ。このシナリオでは、[電子メールのワンタイム パスコード](one-time-passcode.md)機能を使用する必要があります。 "部分的に同期されたテナント" とは、オンプレミスのユーザー ID がクラウドに完全には同期されていないパートナーの Azure AD テナントのことです。 クラウド上に ID がまだ存在していないゲストが、B2B の招待を利用しようとした場合、そのゲストはサインインできません。 ワンタイム パスコード機能であれば、このゲストをサインインさせることができます。 直接フェデレーション機能は、IdP によって管理される独自の組織アカウントをゲストが持っているが、その組織に Azure AD の存在がまったくないというシナリオに対応しています。

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>手順 1:取引先組織の ID プロバイダーを構成する
最初に、取引先組織において、必須の要求と証明書利用者信頼を指定して ID プロバイダーを構成する必要があります。 

> [!NOTE]
> 直接フェデレーション用の ID プロバイダーを構成する方法を示すために、例として Active Directory フェデレーション サービス (AD FS) を使用します。 [AD FS との直接フェデレーションの構成](direct-federation-adfs.md)に関する記事を参照してください。直接フェデレーションの準備として、AD FS を SAML 2.0 または WS-Fed の ID プロバイダーとして構成する方法の例が示されています。

### <a name="saml-20-configuration"></a>SAML 2.0 の構成

Azure AD B2B は、以下に示す特定の要件に従って、SAML プロトコルを使用する ID プロバイダーと連携するように構成できます。 ご利用の SAML ID プロバイダーと Azure AD の間に信頼を設定する方法の詳細については、「[シングル サインオンに SAML 2.0 ID プロバイダー (IdP) を使用する](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)」を参照してください。  

> [!NOTE]
> 注 直接フェデレーションのターゲット ドメインを Azure AD 上で DNS によって検証することはできない点に注意してください。 認証 URL のドメインは、ターゲット ドメインに一致する必要があります。または、許可されている ID プロバイダーのドメインである必要があります。 詳細については、「[制限事項](#limitations)」のセクションを参照してください。 

#### <a name="required-saml-20-attributes-and-claims"></a>必須の SAML 2.0 属性および要求
次の表では、サード パーティの ID プロバイダーに構成する必要がある、特定の属性と要求の要件を示します。 直接フェデレーションを設定するには、ID プロバイダーからの SAML 2.0 応答において以下の属性が受け取られる必要があります。 これらの属性は、オンライン セキュリティ トークン サービスの XML ファイルにリンクするか手動で入力することによって、構成できます。

IdP からの SAML 2.0 応答に必須の属性:

|Attribute  |値  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|対象ユーザー     |`urn:federation:MicrosoftOnline`         |
|発行者     |パートナー IdP の発行者 URI (たとえば `http://www.example.com/exk10l6w90DHM0yi...`)         |


IdP によって発行される SAML 2.0 トークンに必須の要求:

|Attribute  |値  |
|---------|---------|
|NameID の形式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed の構成 
Azure AD B2B は、以下に示すいくつかの特定の要件に従って、WS-Fed プロトコルを使用する ID プロバイダーと連携するように構成できます。 現時点で 2 つの WS-Fed プロバイダー (AD FS と Shibboleth) が、Azure AD との互換性テスト済みです。 WS-Fed 準拠のプロバイダーと Azure AD の間に証明書利用者信頼を確立する方法の詳細については、[Azure AD ID プロバイダーの互換性に関するドキュメント](https://www.microsoft.com/download/details.aspx?id=56843)で「STS Integration Paper using WS Protocols」(WS プロトコルを使用した STS 統合に関する文書) を参照してください。

> [!NOTE]
> 直接フェデレーションのターゲット ドメインを Azure AD 上で DNS によって検証することはできません。 認証 URL のドメインは、ターゲット ドメインまたは許可されている ID プロバイダーのドメインに一致する必要があります。 詳細については、「[制限事項](#limitations)」のセクションを参照してください。 

#### <a name="required-ws-fed-attributes-and-claims"></a>必須の WS-Fed 属性および要求

次の表では、サード パーティの WS-Fed ID プロバイダーに構成する必要がある、特定の属性と要求の要件を示します。 直接フェデレーションを設定するには、ID プロバイダーからの WS-Fed メッセージにおいて以下の属性が受け取られる必要があります。 これらの属性は、オンライン セキュリティ トークン サービスの XML ファイルにリンクするか手動で入力することによって、構成できます。

IdP からの WS-Fed メッセージに必須の属性:
 
|Attribute  |値  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|対象ユーザー     |`urn:federation:MicrosoftOnline`         |
|発行者     |パートナー IdP の発行者 URI (たとえば `http://www.example.com/exk10l6w90DHM0yi...`)         |

IdP によって発行される WS-Fed トークンに必須の要求:

|Attribute  |値  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>手順 2:Azure AD で直接フェデレーションを構成する 
次に、手順 1 で構成した ID プロバイダーとのフェデレーションを Azure AD 上で構成します。 Azure AD ポータルまたは PowerShell のいずれかを使用できます。 直接フェデレーション ポリシーが有効になるまで 5 分から 10 分かかる場合があります。 この間、直接フェデレーション ドメインの招待を利用することを試みないでください。 次の属性は必須です。
- パートナー IdP の発行者 URI
- パートナー IdP のパッシブ認証エンドポイント (サポートされているのは https のみ)
- 証明書

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Azure AD ポータルで直接フェデレーションを構成するには

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 左ウィンドウで、 **[Azure Active Directory]** を選択します。 
2. **[組織の関係]** を選択します。
3. **[ID プロバイダー]** を選択し、次に **[新しい SAML/WS-Fed IdP]** を選択します。

    ![新しい SAML または WS-Fed IdP を追加するためのボタンを示すスクリーンショット](media/direct-federation/new-saml-wsfed-idp.png)

4. **[新しい SAML/WS-Fed IdP]** ページの **[ID プロバイダー プロトコル]** で、 **[SAML]** または **[WS-FED]** を選択します。

    ![SAML または WS-Fed IdP ページの解析ボタンを示すスクリーンショット](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 取引先組織のドメイン名を入力します。これは直接フェデレーションのターゲット ドメイン名になります。
6. メタデータの詳細を設定するメタデータ ファイルをアップロードすることができます。 メタデータを手動で入力する場合は、次の情報を入力します。
   - パートナー IdP のドメイン名
   - パートナー IdP のエンティティ ID
   - パートナー IdP のパッシブな要求元エンドポイント
   - 証明書
   > [!NOTE]
   > メタデータ URL は省略可能ですが、強くお勧めします。 メタデータ URL を指定すると、署名証明書が有効期限切れになったときに、Azure AD によって自動的に更新することができます。 証明書が有効期限切れになる前に何らかの理由でローテーションされた場合、またはメタデータ URL を指定しなかった場合には、Azure AD による更新はできません。 この場合、署名証明書を手動で更新する必要があります。

7. **[保存]** を選択します。 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>PowerShell を使用して Azure AD で直接フェデレーションを構成するには

1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 (詳細な手順が必要な場合は、ゲスト ユーザー追加のクイックスタートに、「[最新の AzureADPreview モジュールをインストールする](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)」セクションがあります。) 
2. 次のコマンドを実行します。 
   ```powershell
   Connect-AzureAD
   ```
1. サインイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。 
2. フェデレーション メタデータ ファイルにある値に置き換えたうえで、次のコマンドを実行します。 AD FS サーバーと Okta の場合、フェデレーション ファイルは federationmetadata.xml です (例: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`)。 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>手順 3:Azure AD で直接フェデレーションをテストする
次に、新しい B2B ゲスト ユーザーを招待して直接フェデレーションの設定をテストします。 詳細については、「[Azure Portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](add-users-administrator.md)」を参照してください。
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>直接フェデレーション関係を編集する方法

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 左ウィンドウで、 **[Azure Active Directory]** を選択します。 
2. **[組織の関係]** を選択します。
3. **[ID プロバイダー]** を選択します。
4. **SAML/WS-Fed ID プロバイダー** でプロバイダーを選択します。
5. ID プロバイダーの詳細ウィンドウで、値を更新します。
6. **[保存]** を選択します。


## <a name="how-do-i-remove-direct-federation"></a>直接フェデレーションを削除する方法
直接フェデレーション設定を削除できます。 実行した場合、既に招待を利用済みの直接フェデレーションのゲスト ユーザーは、サインインできなくなります。 しかし、ディレクトリから削除し、再招待することで、リソースへのアクセス権をもう一度付与することができます。 Azure AD ポータルで ID プロバイダーとの直接フェデレーションを削除するには:

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 左ウィンドウで、 **[Azure Active Directory]** を選択します。 
2. **[組織の関係]** を選択します。
3. **[Identity Providers]** を選択します。
4. ID プロバイダーを選択し、 **[削除]** を選択します。 
5. **[はい]** を選択して削除を確定します。 

PowerShell を使用して ID プロバイダーとの直接フェデレーションを削除するには:
1. 最新バージョンの Azure AD PowerShell for Graph モジュールをインストールします ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 次のコマンドを実行します。 
   ```powershell
   Connect-AzureAD
   ```
3. サインイン プロンプトで、マネージド グローバル管理者アカウントを使用してサインインします。 
4. 次のコマンドを入力します。
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
