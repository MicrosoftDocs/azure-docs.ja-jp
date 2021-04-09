---
title: Apple デバイス用の Microsoft Enterprise SSO プラグイン
titleSuffix: Microsoft identity platform | Azure
description: iOS、iPadOS、macOS デバイス用の Azure Active Directory SSO プラグインについて説明します。
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 24a538686e101d40daba008f30a72ffc5078047a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674524"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Apple デバイス用の Microsoft Enterprise SSO プラグイン (プレビュー)

>[!IMPORTANT]
> この機能[!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Apple デバイス用の Microsoft Enterprise SSO プラグイン* は、Apple の[エンタープライズ シングル サインオン](https://developer.apple.com/documentation/authenticationservices)機能をサポートするすべてのアプリケーションで、macOS、iOS、iPadOS 上の Azure Active Directory (Azure AD) アカウントに対するシングル サインオン (SSO) を提供します。 このプラグインにより、業務に必要だが、最新の ID ライブラリやプロトコルはまだサポートしていない古いアプリケーションにも SSO が提供されます。 Microsoft は Apple と密接に連携してこのプラグインを開発し、アプリケーションの使いやすさを向上させ、利用可能な最高の保護を提供しています。

Enterprise SSO プラグインは現在、次のアプリの組み込み機能です。

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): iOS、iPadOS
* Microsoft Intune [ポータル サイト](/mem/intune/apps/apps-company-portal-macos): macOS

## <a name="features"></a>特徴

Apple デバイス用の Microsoft Enterprise SSO プラグインには、次のような利点があります。

- Apple のエンタープライズ SSO 機能をサポートするすべてのアプリケーションで、Azure AD アカウントに対する SSO を提供します。
- 任意のモバイル デバイス管理 (MDM) ソリューションで有効にすることができます。
- Microsoft ID プラットフォーム ライブラリをまだ使用していないアプリケーションに SSO を拡張します。
- OAuth 2、OpenID Connect、SAML を使用するアプリケーションに SSO を拡張します。

## <a name="requirements"></a>必要条件

Apple デバイス用の Microsoft Enterprise SSO プラグインを使用するには:

- Apple デバイス用の Microsoft Enterprise SSO プラグインを含むアプリが、デバイスで *サポート* およびインストールされている必要があります。
  - iOS 13.0 以降: [Microsoft Authenticator アプリ](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 以降: [Microsoft Authenticator アプリ](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 以降: [Intune ポータル サイト アプリ](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Microsoft Intune を使用するなどして、デバイスが *MDM に登録* されている必要があります。
- 構成を *デバイスにプッシュ* して、Enterprise SSO プラグインを有効にする必要があります。 このセキュリティ制約は Apple の要件です。

iOS の要件:
- iOS 13.0 以降がデバイスにインストールされている必要があります。
- Apple デバイス用の Microsoft Enterprise SSO プラグインを提供する Microsoft アプリケーションが、デバイスにインストールされている必要があります。 パブリック プレビューの間は、このアプリケーションは [Microsoft Authenticator アプリ](/intune/user-help/user-help-auth-app-overview.md)です。


macOS の要件:
- macOS 10.15 以降がデバイスにインストールされている必要があります。 
- Apple デバイス用の Microsoft Enterprise SSO プラグインを提供する Microsoft アプリケーションが、デバイスにインストールされている必要があります。 パブリック プレビューの間は、このアプリケーションは [Intune ポータル サイト アプリ](/intune/user-help/enroll-your-device-in-intune-macos-cp.md)です。

## <a name="enable-the-sso-plug-in"></a>SSO プラグインの有効化

MDM を使用して SSO プラグインを有効にするには、次の情報を使用します。
### <a name="microsoft-intune-configuration"></a>Microsoft Intune の構成

MDM サービスとして Microsoft Intune を使用する場合は、組み込みの構成プロファイル設定を使用して、Microsoft Enterprise SSO プラグインを有効にすることができます。

1. 構成プロファイルの [SSO アプリ拡張機能](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension)の設定を構成します。 
1. プロファイルがまだ割り当てられていない場合、[プロファイルをユーザーまたはデバイス グループに割り当てます](/mem/intune/configuration/device-profile-assign)。

SSO プラグインを有効にするプロファイル設定は、各デバイスが次回 Intune でチェックインするときに、グループのデバイスに自動的に適用されます。

### <a name="manual-configuration-for-other-mdm-services"></a>他の MDM サービスの手動構成

Intune を MDM に使用しない場合、Apple デバイス用の Microsoft Enterprise SSO プラグインを構成するには、次のパラメーターを使用します。

iOS の設定:

- **拡張機能 ID**: `com.microsoft.azureauthenticator.ssoextension`
- **チーム ID**: iOS ではこのフィールドは不要です。

macOS の設定:

- **拡張機能 ID**: `com.microsoft.CompanyPortalMac.ssoextension`
- **チーム ID**: `UBF8T346G9`

共通設定:

- **[種類]** :リダイレクト
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="more-configuration-options"></a>その他の構成オプション
構成オプションをさらに追加して、SSO 機能を他のアプリに拡張することができます。

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Microsoft ID プラットフォーム ライブラリを使用しないアプリの SSO を有効にする

SSO プラグインを使用すると、Microsoft 認証ライブラリ (MSAL) などの Microsoft SDK を使用して開発されていないアプリケーションでも、SSO に参加させることができます。

SSO プラグインは、次の条件を満たすデバイスによって自動的にインストールされます。
* Authenticator アプリ (iOS、iPadOS の場合) または Intune ポータル サイト アプリ (macOS の場合) がダウンロード済みである。
* デバイスが組織に登録されている。 

組織では、多要素認証 (MFA)、パスワードレス認証、条件付きアクセスなどのシナリオで Authenticator アプリを使用している可能性があります。 MDM プロバイダーを使用して、アプリケーション用の SSO プラグインを有効にすることができます。 Microsoft では、Intune の Microsoft エンドポイント マネージャー内でプラグインを簡単に構成できるようにしました。 許可リストは、SSO プラグインを使用するようにこれらのアプリケーションを構成するために使用されます。

>[!IMPORTANT]
> Microsoft Enterprise SSO プラグインでは、ネイティブの Apple ネットワーク テクノロジまたは Web ビューを使用するアプリのみをサポートしています。 独自のネットワーク レイヤー実装が組み込まれているアプリケーションはサポートしていません。  

Microsoft ID プラットフォーム ライブラリを使用しないアプリ用の Microsoft Enterprise SSO プラグインを構成するには、次のパラメーターを使用します。

特定のアプリの一覧を指定するには、次のパラメーターを使用します。

- **キー**: `AppAllowList`
- **型**: `String`
- **値**: SSO への参加が許可されているアプリケーションのアプリケーション バンドル ID のコンマ区切りの一覧。
- **例**: `com.contoso.workapp, com.contoso.travelapp`

プレフィックスの一覧を指定するには、次のパラメーターを使用します。
- **キー**: `AppPrefixAllowList`
- **型**: `String`
- **値**: SSO への参加が許可されているアプリケーションのアプリケーション バンドル ID プレフィックスのコンマ区切りの一覧です。 このパラメーターによって、特定のプレフィックスで始まるすべてのアプリを SSO に参加させることができます。
- **例**: `com.contoso., com.fabrikam.`

MDM 管理者が SSO への参加を許可している[同意済みアプリ](./application-consent-experience.md)は、エンド ユーザーのトークンをサイレントで取得できます。 そのため、許可リストには信頼されたアプリケーションのみを追加してください。 

>[!NOTE]
> SSO に参加できるアプリの一覧に、MSAL または ASWebAuthenticationSession を使用するアプリケーションを追加する必要はありません。 これらのアプリケーションは既定で有効になっています。 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>iOS デバイスでのアプリ バンドル ID の確認

Apple では、App Store からバンドル ID を簡単に取得する方法は提供していません。 SSO に使用するアプリのバンドル ID を取得するには、ベンダーまたはアプリ開発者に問い合わせるのが最も簡単です。 この選択肢が使用できない場合、MDM 構成を使用してバンドル ID を確認できます。 

1. MDM 構成で次のフラグを一時的に有効にします。

    - **キー**: `admin_debug_mode_enabled`
    - **型**: `Integer`
    - **値**: 1 または 0
1. このフラグがオンであるときに、バンドル ID を知りたいデバイス上の iOS アプリにサインインします。 
1. Authenticator アプリで、 **[ヘルプ]**  >  **[ログの送信]**  >  **[ログの表示]** を選択します。 
1. ログ ファイルで、次の行を探します: `[ADMIN MODE] SSO extension has captured following app bundle identifiers`。 この行により、SSO 拡張機能から認識可能なすべてのアプリケーション バンドル ID が得られます。 

これらのバンドル ID を使用して、アプリの SSO を構成します。 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>不明なアプリケーションおよび Safari ブラウザーからユーザーがサインインできるようにする

既定では、Microsoft Enterprise SSO プラグインは、MSAL や Azure Active Directory 認証ライブラリ (ADAL) などの Microsoft ID プラットフォーム ライブラリを使用するアプリからユーザーがサインインした場合にのみ、許可されたアプリに対する SSO を提供します。 Microsoft Enterprise SSO プラグインは、新しいトークンの取得中に Microsoft ID プラットフォーム ライブラリを使用する別のアプリによって呼び出されたときに、共有資格情報を取得することもできます。

`browser_sso_interaction_enabled` フラグを有効にすると、Microsoft ID プラットフォーム ライブラリを使用していないアプリで、初期ブートストラップを実行して共有資格情報を取得できるようになります。 Safari ブラウザーでも、初期ブートストラップを実行して共有資格情報を取得できるようになります。 

Microsoft Enterprise SSO プラグインにまだ共有資格情報がない場合、Safari ブラウザー、ASWebAuthenticationSession、SafariViewController、または別の許可されたネイティブ アプリケーション内の Azure AD URL からサインインが要求されると、資格情報の取得が試行されます。 

フラグを有効にするには、次のパラメーターを使用します。 

- **キー**: `browser_sso_interaction_enabled`
- **型**: `Integer`
- **値**: 1 または 0

macOS では、すべてのアプリで一貫したエクスペリエンスを提供できるよう、この設定が必要です。 iOS と iPadOS では、ほとんどのアプリで Authenticator アプリケーションをサインインに使用するため、この設定は必要ありません。 ただし、この設定を有効にすることをお勧めします。iOS または iPadOS で一部のアプリケーションが Authenticator アプリを使用しない場合、このフラグを使用するとエクスペリエンスが向上するからです。 この設定は既定では無効になっています。

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>初期ブートストラップ中の MFA の確認を無効にする

既定では、Microsoft Enterprise SSO プラグインは、最初のブートストラップ中および共有資格情報の取得中、ユーザーに対して常に MFA のプロンプトを表示します。 ユーザーが開いたアプリケーションには必要ない場合でも、ユーザーには MFA のプロンプトが表示されます。 この動作により、後で MFA が必要になった場合にユーザーにプロンプトを表示する必要なしに、他のすべてのアプリケーションで共有資格情報を簡単に使用できます。 全体としてはユーザーに表示されるプロンプトが減るため、これは通常、適切な設定です。

`browser_sso_disable_mfa` を有効にすると、最初のブートストラップ中および共有資格情報の取得中に MFA が無効になります。 この場合、ユーザーには、アプリケーションまたはリソースで MFA が必要な場合にのみプロンプトが表示されます。 

フラグを有効にするには、次のパラメーターを使用します。

- **キー**: `browser_sso_disable_mfa`
- **型**: `Integer`
- **値**: 1 または 0

サインインのプロンプトがユーザーに表示される回数を減らすために、このフラグは無効のままにしておくことをお勧めします。 組織で MFA をめったに使用しない場合、このフラグを有効にすることもできます。 ただし、その代わりに MFA の使用頻度を高めることをお勧めします。 このような理由から、このフラグは既定では無効になっています。

#### <a name="disable-oauth-2-application-prompts"></a>OAuth 2 アプリケーションのプロンプトを無効にする

Microsoft Enterprise SSO プラグインでは、許可されたアプリケーションからのネットワーク要求に共有資格情報を追加することで SSO を提供します。 ただし、OAuth 2 アプリケーションによっては、プロトコル レイヤーでエンドユーザーのプロンプトが誤って強制されることがあります。 この問題が発生した場合、それらのアプリに対しては共有資格情報が無視されることもわかります。 Microsoft Enterprise SSO プラグインが他のアプリケーションに対して機能する場合でも、ユーザーにはサインインのプロンプトが表示されます。  

`disable_explicit_app_prompt` フラグを有効にすると、ネイティブ アプリケーションと Web アプリケーションの両方の機能が制限され、プロトコル レイヤーに対するエンドユーザー プロンプトが強制され、SSO をバイパスされます。 フラグを有効にするには、次のパラメーターを使用します。

- **キー**: `disable_explicit_app_prompt`
- **型**: `Integer`
- **値**: 1 または 0

すべてのアプリで一貫したエクスペリエンスを得るには、このフラグを有効にすることをお勧めします。 既定では無効になっています。 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>特定のアプリケーションに対して Cookie を使用して SSO を有効にする

いくつかのアプリは SSO 拡張機能と互換性がない場合があります。 特に、高度なネットワーク設定を持つアプリで SSO が有効になっていると、予期しない問題が発生する可能性があります。 たとえば、ネットワーク要求が取り消されたか、中断されたことを示すエラーが表示される場合があります。 

「[MSAL を使用しないアプリケーション](#applications-that-dont-use-msal)」で説明されている方法を使用してのサインインで問題が発生した場合は、別の構成を試してみてください。 プラグインを構成するには、次のパラメーターを使用します。

- **キー**: `AppCookieSSOAllowList`
- **型**: `String`
- **値**: SSO への参加が許可されているアプリケーションのアプリケーション バンドル ID プレフィックスのコンマ区切りの一覧です。 一覧に含まれるプレフィックスで始まるすべてのアプリが、SSO への参加を許可されます。
- **例**: `com.contoso.myapp1, com.fabrikam.myapp2`

この設定を使用して SSO を有効にしたアプリケーションを、`AppCookieSSOAllowList` と `AppPrefixAllowList` の両方に追加する必要があります。

この構成は、予期しないサインイン エラーが発生したアプリケーションに対してのみ試してください。 

#### <a name="use-intune-for-simplified-configuration"></a>Intune を使用して構成を簡略化する

Intune を MDM サービスとして使用し、Microsoft Enterprise SSO プラグインの構成を容易にすることができます。 たとえば、Intune を使用して、プラグインを有効にしたり、古いアプリを許可リストに追加して SSO に参加させたりできます。 

詳細については、[Intune の構成に関するドキュメント](/intune/configuration/ios-device-features-settings)を参照してください。

## <a name="use-the-sso-plug-in-in-your-application"></a>アプリケーションで SSO プラグインを使用する

[Apple デバイス用の MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) のバージョン 1.1.0 以降では、Apple デバイス用の Microsoft Enterprise SSO プラグインがサポートされています。 Microsoft Enterprise SSO プラグインのサポートを追加するには、この方法をお勧めします。 これにより、Microsoft ID プラットフォームのすべての機能が試用できるようになります。

フロントライン ワーカーのシナリオ向けにアプリケーションを構築している場合は、「[iOS デバイスの共有デバイス モード](msal-ios-shared-devices.md)」でセットアップ情報を参照してください。

## <a name="understand-how-the-sso-plug-in-works"></a>SSO プラグインのしくみを理解する

Microsoft Enterprise SSO プラグインは、[Apple Enterprise SSO フレームワーク](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)に依存しています。 このフレームワークに参加している ID プロバイダーは、ドメインのネットワーク トラフィックを傍受し、それらの要求の処理方法を強化または変更することができます。 たとえば、SSO プラグインでは、エンドユーザーの資格情報を安全に収集する追加の UI を表示すること、MFA を要求すること、またはアプリケーションにトークンをサイレントで提供することができます。

ネイティブ アプリケーションは、カスタム操作を実装し、SSO プラグインと直接通信することもできます。 詳細については、こちらの [2019 Worldwide Developer Conference のビデオ (Apple 提供)](https://developer.apple.com/videos/play/tech-talks/301/) をご覧ください。

### <a name="applications-that-use-msal"></a>MSAL を使用するアプリケーション

[Apple デバイス用の MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) のバージョン 1.1.0 以降では、職場および学校アカウントに対して、Apple デバイス用の Microsoft Enterprise SSO プラグインがネイティブでサポートされています。 

[すべての推奨される手順](./quickstart-v2-ios.md)を実行し、既定の[リダイレクト URI 形式](./redirect-uris-ios.md)を使用した場合、特別な構成は必要ありません。 SSO プラグインがあるデバイスでは、すべての対話型およびサイレントなトークン要求に対して、このプラグインが MSAL によって自動的に呼び出されます。 これは、アカウント列挙攻撃およびアカウントの削除の操作に対しても呼び出されます。 MSAL にはカスタム操作に依存するネイティブ SSO プラグイン プロトコルが実装されているため、この設定にすると、エンド ユーザーにとって最もスムーズなネイティブ エクスペリエンスが提供されます。 

SSO プラグインが MDM で有効にされていないが、デバイスに Microsoft Authenticator アプリが存在する場合、MSAL では、対話型トークンの要求に対して Authenticator アプリが代わりに使用されます。 SSO プラグインは、Authenticator アプリと SSO を共有します。

### <a name="applications-that-dont-use-msal"></a>MSAL を使用しないアプリケーション

MSAL などの Microsoft ID プラットフォーム ライブラリを使用しないアプリケーションでも、管理者がこれらのアプリケーションを許可リストに追加すると、SSO を取得できます。 

次の条件が満たされている限り、これらのアプリのコードを変更する必要はありません。

- アプリケーションでは、Apple のフレームワークを使用してネットワーク要求を実行している。 これらのフレームワークの例としては、[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) や [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession) があります。 
- アプリケーションで、標準プロトコルを使用して AzureAD と通信している。 これらのプロトコルの例としては、OAuth 2、SAML、WS-Federation などがあります。
- アプリケーションのネイティブ UI で、プレーンテキストのユーザー名とパスワードを収集していない。

この場合、アプリケーションでネットワーク要求を作成し、Web ブラウザーを開いてユーザーをサインインすると、SSO が提供されます。 ユーザーが Azure AD サインイン URL にリダイレクトされると、SSO プラグインによって URL が検証され、その URL の SSO 資格情報が確認されます。 資格情報が見つかった場合、SSO プラグインから Azure AD に渡されます。これにより、ユーザーに資格情報の入力が求められることなく、アプリケーションがネットワーク要求を完了できるようになります。 さらに、デバイスが Azure AD に認識されている場合、デバイスベースの条件付きアクセス チェックを満たすデバイス証明書が SSO プラグインによって渡されます。 

非 MSAL アプリに対する SSO をサポートするために、SSO プラグインには、「[プライマリ更新トークンとは](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)」で説明されている Windows ブラウザー プラグインと同様のプロトコルが実装されています。 

MSAL ベースのアプリと比較すると、SSO プラグインは、非 MSAL アプリに対してさらに透過的に機能します。 アプリで提供される既存のブラウザー ログイン エクスペリエンスと統合します。 

エンド ユーザーには慣れ親しんだエクスペリエンスが提供され、アプリケーションごとにサインインを繰り返す必要はありません。 たとえば、SSO プラグインでは、ネイティブ アカウント ピッカーを表示する代わりに、SSO セッションを Web ベースのアカウント ピッカー エクスペリエンスに追加します。 

## <a name="next-steps"></a>次のステップ

[iOS デバイスの共有デバイス モード](msal-ios-shared-devices.md)について理解します。
