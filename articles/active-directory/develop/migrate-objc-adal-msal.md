---
title: ADAL から MSAL への移行に関するガイド (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: iOS および macOS 用の MSAL と Azure AD Authentication Library for ObjectiveC (ADAL.ObjC) の違いと、iOS および macOS 用の MSAL に移行する方法について学習します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 7dc3241198fbc6eeddba059251f28c6dc35c8a29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98754934"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>アプリケーションを iOS および macOS 用の MSAL に移行する

Azure Active Directory Authentication Library ([ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) は、v1.0 エンドポイント経由で Azure Active Directory アカウントと連携するために作成されました。

iOS および macOS 用の Microsoft Authentication Library (MSAL) は、Azure Active Directory (Azure AD) アカウント、個人用 Microsoft アカウント、Azure AD B2C アカウントなど、Microsoft ID プラットフォーム (正式には Azure AD v2.0 エンドポイント) を経由するすべての Microsoft ID と連携するように構築されています。

Microsoft ID プラットフォームには、Azure Active Directory v1.0 と大きな相違点がいくつかあります。 この記事では、これらの相違点に焦点を当て、ADAL から MSAL にアプリを移行するためのガイダンスを提供します。

## <a name="adal-and-msal-app-capability-differences"></a>ADAL と MSAL アプリの機能の相違点

### <a name="who-can-sign-in"></a>サインインできるユーザー

* ADAL では、職場と学校のアカウント (Azure AD アカウントとも呼ばれます) のみがサポートされています。
* MSAL では、Hotmail.com、Outlook.com、Live.com などの個人用 Microsoft アカウント (MSA アカウント) がサポートされています。
* MSAL では、職場と学校のアカウントと Azure AD B2C アカウントがサポートされています。

### <a name="standards-compliance"></a>標準へのコンプライアンス

* Microsoft ID プラットフォームは、OAuth 2.0 および OpenId Connect 標準に従っています。

### <a name="incremental-and-dynamic-consent"></a>増分および動的な同意

* Azure Active Directory v1.0 エンドポイントでは、アプリケーションの登録時にすべてのアクセス許可を事前に宣言する必要があります。 これは、これらのアクセス許可が静的であることを意味します。
* Microsoft ID プラットフォームを使用すると、アクセス許可を動的に要求できます。 アプリでは必要な場合にのみアクセス許可を要求でき、アプリで必要な場合にはさらに要求できます。

Azure Active Directory v1.0 と Microsoft ID プラットフォームの相違点の詳細については、「[Microsoft ID プラットフォームに更新する理由](../azuread-dev/azure-ad-endpoint-comparison.md)」を参照してください。

## <a name="adal-and-msal-library-differences"></a>ADAL と MSAL ライブラリの相違点

MSAL パブリック API には、Azure AD v1.0 と Microsoft ID プラットフォームとの主な相違点がいくつか反映されています。

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>ADAuthenticationContext ではなく MSALPublicClientApplication

`ADAuthenticationContext` は、ADAL アプリによって作成される最初のオブジェクトです。 これは ADAL のインスタンス化を表します。 アプリにより、Azure Active Directory クラウドとテナント (機関) の組み合わせごとに `ADAuthenticationContext` の新しいインスタンスが作成されます。 同じ `ADAuthenticationContext` を使用して、複数のパブリック クライアント アプリケーションのトークンを取得できます。

MSAL では、主な対話は `MSALPublicClientApplication` オブジェクトを介して行われます。これは、[OAuth 2.0 パブリック クライアント](https://tools.ietf.org/html/rfc6749#section-2.1)の後にモデル化されます。 `MSALPublicClientApplication` の 1 つのインスタンスを使用して、複数の AAD クラウドとテナントとの対話を行うことができます。機関ごとに新しいインスタンスを作成する必要はありません。 ほとんどのアプリでは、1 つの `MSALPublicClientApplication` インスタンスで十分です。

### <a name="scopes-instead-of-resources"></a>リソースではなくスコープ

ADAL では、Azure Active Directory v1.0 エンドポイントからトークンを取得するために、アプリで `https://graph.microsoft.com` のような "*リソース*" 識別子を提供する必要がありました。 リソースでは、認識できるスコープの数 (アプリ マニフェスト内の oAuth2Permissions) を定義できます。 これにより、クライアント アプリで、アプリの登録時に事前に定義された特定の一連のスコープに対して、そのリソースのトークンを要求できます。

MSAL では、単一のリソース識別子ではなく、アプリにより要求ごとに一連のスコープが提供されます。 スコープとは、リソース/アクセス許可の形式で、リソース識別子の後にアクセス許可名を続けたものです。 たとえば、`https://graph.microsoft.com/user.read` のように指定します。

MSAL でスコープを提供するには、次の 2 つの方法があります。

* アプリに必要なすべてのアクセス許可の一覧を提供する。 次に例を示します。 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    この場合、アプリによって `directory.read` と `directory.write` のアクセス許可が要求されます。 ユーザーは、これらのアクセス許可に同意するように求められます (このアプリに対して事前にこれらのアクセス許可に同意していない場合)。 アプリケーションでは、ユーザーがアプリケーションに対して既に同意している追加のアクセス許可も受け取ることができます。 ユーザーには、新しいアクセス許可、または付与されていないアクセス許可についてのみ、同意を求めるメッセージが表示されます。

* `/.default` スコープ。

これは、すべてのアプリケーションの組み込みのスコープです。 これは、アプリケーションが登録されたときに構成されたアクセス許可の静的リストを参照します。 その動作は、`resource` の動作と似ています。 これは、同様の一連のスコープとユーザー エクスペリエンスが維持されるように移行する場合に便利です。

`/.default` スコープを使用するには、リソース識別子に `/.default` を追加します。 (例: `https://graph.microsoft.com/.default`)。 リソースがスラッシュ (`/`) で終わっている場合は、先頭のスラッシュを含めて `/.default` を追加する必要があります。これにより、2 つのスラッシュ (`//`) を持つスコープが作成されます。

"/.default" スコープの使用方法について詳しくは、[こちら](./v2-permissions-and-consent.md#the-default-scope)を参照してください。

### <a name="supporting-different-webview-types--browsers"></a>異なる WebView の種類とブラウザーのサポート

ADAL では、iOS 用の UIWebView/WKWebView と macOS 用の WebView のみがサポートされています。 iOS 用の MSAL では、認証コードを要求するときに Web コンテンツを表示するためにさらに多くのオプションがサポートされています。また、`UIWebView` はサポートされなくなりました。これにより、ユーザー エクスペリエンスとセキュリティが向上します。

既定では、iOS 上の MSAL では [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) が使用されます。これは、iOS 12 以降のデバイスで認証を行うために Apple が推奨している Web コンポーネントです。 これは、アプリと Safari ブラウザーの間で cookie を共有することにより、シングル サインオン (SSO) の利点を提供します。

アプリの要件と必要なエンド ユーザー エクスペリエンスに応じて、異なる Web コンポーネントの使用を選択できます。 その他のオプションについては、[サポートされている Web ビューの種類](customize-webviews.md)に関するページを参照してください。

ADAL から MSAL に移行する場合、`WKWebView` により、iOS と macOS の ADAL によく似たユーザー エクスペリエンスが提供されます。 可能な場合は、iOS の `ASWebAuthenticationSession` に移行することをお勧めします。 macOS の場合は、`WKWebView` を使用することをお勧めします。

### <a name="account-management-api-differences"></a>アカウント管理 API の相違点

ADAL メソッド `acquireToken()` または `acquireTokenSilent()` を呼び出すと、認証されているアカウントを表す `id_token` からの要求の一覧を含む `ADUserInformation` オブジェクトが返されます。 さらに、`ADUserInformation` では、`upn` 要求に基づいて `userId` が返されます。 最初の対話型トークンが取得されると、開発者は `userId` をすべてサイレント呼び出しで提供するように ADAL から要求されます。

ADAL では、既知のユーザー ID を取得するための API は提供されません。 これらのアカウントの保存および管理は、アプリに依存しています。

MSAL には、トークンを取得しなくても、MSAL で認識されているすべてのアカウントを一覧表示するための一連の API が用意されています。

ADAL と同様に、MSAL により `id_token` からの要求の一覧を保持するアカウント情報が返されます。 これは、`MSALResult` オブジェクト内の `MSALAccount` オブジェクトの一部です。

MSAL では、アカウントを削除するための一連の API が用意されており、削除されたアカウントにアプリからアクセスできないようにします。 アカウントを削除すると、後でトークンを取得する呼び出しによって、ユーザーに対話型トークンの取得を求めるメッセージが表示されます。 アカウントの削除は、それを開始したクライアント アプリケーションにのみ適用され、デバイスで実行されている他のアプリから、またはシステム ブラウザーからアカウントが削除されることはありません。 これにより、個々のアプリからサインアウトした後でも、ユーザーが引き続きデバイスで SSO エクスペリエンスを利用できるようにしています。

さらに、MSAL により、後でトークンを暗黙的に要求するために使用できるアカウント識別子も返されます。 ただし、(`MSALAccount` オブジェクトの `identifier` プロパティを使用してアクセス可能な) アカウント識別子は表示できません。また、使用される形式を推測することもできず、それを解釈したり解析しようとするべきではありません。

### <a name="migrating-the-account-cache"></a>アカウント キャッシュの移行

ADAL から移行するときに、アプリでは通常、ADAL の `userId` が格納されます。これには MSAL に必要な `identifier` はありません。 1 回限りの移行の手順として、次の API で ADAL の userId を使用して、アプリで MSAL アカウントを照会できます。

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

この API では、MSAL と ADAL の両方のキャッシュを読み取って、ADAL userId (UPN) によるアカウント検索が行われます。

アカウントが見つかった場合、開発者は、そのアカウントを使用して、トークンの取得をサイレントに行う必要があります。 最初のサイレント トークンの取得により、アカウントが実質的にアップグレードされ、開発者は MSAL の結果 (`identifier`) で MSAL と互換性のあるアカウント識別子を取得します。 その後は、次の API を使用したアカウントの参照には、`identifier` のみを使用する必要があります。

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

MSAL のすべての操作で ADAL の `userId` を使用し続けることもできますが、`userId` は UPN に基づいているため、ユーザー エクスペリエンスを低下させる複数の制限が課されます。 たとえば、UPN が変更されると、ユーザーはもう一度サインインする必要があります。 すべてのアプリで、すべての操作に対して、表示不可能なアカウント `identifier` を使用することをお勧めします。

詳細については、[キャッシュ状態の移行](sso-between-adal-msal-apps-macos-ios.md)に関するページをご覧ください。

### <a name="token-acquisition-changes"></a>トークン取得の変更

MSAL では、いくつかのトークン取得呼び出しの変更が導入されています。

* ADAL と同様、`acquireTokenSilent` は常にサイレント要求になります。
* ADAL とは異なり、`acquireToken` は常に、Web ビューまたは Microsoft Authenticator アプリのいずれかを通じて、ユーザーがアクション可能な UI になります。 Webview/Microsoft Authenticator 内の SSO 状態によっては、ユーザーは自身の資格情報の入力を求められる場合があります。
* ADAL では、`AD_PROMPT_AUTO` の `acquireToken` により最初にサイレント トークンの取得が試行され、サイレント要求が失敗した場合にのみ UI が表示されます。 MSAL では、最初に `acquireTokenSilent` を呼び出し、サイレント取得が失敗した場合にのみ `acquireToken` を呼び出すことによって、このロジックが実現されます。 これにより、開発者は対話型トークンの取得を開始する前にユーザー エクスペリエンスをカスタマイズできます。

### <a name="error-handling-differences"></a>エラー処理の相違点

MSAL では、アプリで処理できるエラーと、ユーザーによる介入が必要なエラーがより明確になっています。 開発者が処理する必要があるエラーの数は限られています。

* `MSALErrorInteractionRequired`:ユーザーは対話型の要求を行う必要があります。 これは、有効期限が切れた認証セッション、条件付きアクセス ポリシーが変更された、更新トークンが期限切れになったまたは失効した、キャッシュ内に有効なトークンがないなど、さまざまな理由で発生する可能性があります。
* `MSALErrorServerDeclinedScopes`:要求が完全に完了しなかったため、一部のスコープでアクセスが許可されませんでした。 これは、ユーザーが 1 つ以上のスコープに対する同意を拒否したことで発生する場合があります。

[`MSALError` リスト](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)内の他のすべてのエラーの処理は、省略可能です。 これらのエラーの情報を使用して、ユーザー エクスペリエンスを向上させることができます。

MSAL エラー処理の詳細については、[MSAL を使用した例外とエラーの処理](msal-error-handling-ios.md)に関するページを参照してください。

### <a name="broker-support"></a>ブローカー サポート

MSAL (バージョン v0.3.0 以降) では、Microsoft Authenticator アプリを使用したブローカー認証がサポートされています。 Microsoft Authenticator では、条件付きアクセス シナリオのサポートも有効になります。 条件付きアクセス シナリオの例としては、ユーザーが Intune でデバイスを登録したり、トークンを取得するために AAD に登録したりする必要があるデバイス コンプライアンス ポリシーなどがあります。 また、モバイル アプリケーション管理 (MAM) の条件付きアクセス ポリシーでは、アプリがトークンを取得する前に、コンプライアンスの証明が求められます。

アプリケーションに対してブローカーを有効にするには:

1. アプリケーションにブローカーと互換性のあるリダイレクト URI 形式を登録します。 ブローカーと互換性のあるリダイレクト URI 形式は `msauth.<app.bundle.id>://auth` です。 `<app.bundle.id>` をご使用のアプリケーションのバンドル ID に置き換えます。 ADAL から移行していて、アプリケーションが既にブローカーに対応している場合は、追加の作業は必要ありません。 前のリダイレクト URI は MSAL と完全に互換性があるため、ステップ 3 に進むことができます。

2. アプリケーションのリダイレクト URI スキームをご自分の info.plist ファイルに追加します。 既定の MSAL リダイレクト URI の場合、形式は `msauth.<app.bundle.id>` です。 次に例を示します。

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. 次のスキームを LSApplicationQueriesSchemes の下のアプリの Info.plist に追加します。

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. コールバックが処理されるように、次の内容を AppDelegate.m ファイルに追加します。Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>企業間 (B2B)

ADAL で、アプリがトークンを要求するテナントごとに `ADAuthenticationContext` の個別インスタンスを作成します。 これは、MSAL の要件ではなくなりました。 MSAL では、`MSALPublicClientApplication` の単一インスタンスを作成し、acquireToken と acquireTokenSilent の呼び出しに別の機関を指定することで、それを任意の AAD クラウドおよび組織に使用できます。

## <a name="sso-in-partnership-with-other-sdks"></a>他の SDK と連携した SSO

iOS 用の MSAL では、次の SDK を使用して、統合キャッシュ経由で SSO を実現できます。

- ADAL Objective-C 2.7.x 以降
- MSAL.NET for Xamarin 2.4 x 以降
- ADAL.NET for Xamarin 4.4.x 以降

SSO は、iOS キーチェーン共有によって実現され、同じ Apple Developer アカウントから発行されたアプリ間でのみ使用できます。

iOS キーチェーン共有による SSO は、唯一のサイレント SSO の種類です。

macOS では、MSAL は、iOS および macOS 用の他の MSAL ベースのアプリケーションと ADAL Objective-C ベースのアプリケーションを使用して SSO を実現できます。

iOS の MSAL では、次の 2 種類の SSO もサポートされています。

* Web ブラウザーを使用した SSO。 iOS 用 MSAL では `ASWebAuthenticationSession` がサポートされています。これにより、デバイス上の他のアプリと特に Safari ブラウザーの間で共有される cookie を使用して SSO が提供されます。
* 認証ブローカーを使用した SSO。 iOS デバイスでは、Microsoft Authenticator は認証ブローカーとして機能します。 これは準拠デバイスの要求などの条件付きアクセス ポリシーに従って、登録済みデバイスに SSO を提供できます。 バージョン 0.3.0 以降の MSAL SDK では、既定でブローカーがサポートされています。

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[Intune MAM SDK](/intune/app-sdk-get-started) では、バージョン [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2) 以降の iOS 用 MSAL がサポートされています。

## <a name="msal-and-adal-in-the-same-app"></a>同じアプリ内の MSAL と ADAL

同じアプリケーション内で ADAL バージョン 2.7.0 以降を MSAL と共存させることはできません。 主な理由は、共有サブモジュールの共通コードです。 Objective-C では名前空間がサポートされていないため、ADAL と MSAL の両方のフレームワークをアプリケーションに追加すると、同じクラスの 2 つのインスタンスが存在することになります。 実行時にどちらが選択されるかは保証されません。 両方の SDK で競合しているクラスの同じバージョンが使用されている場合でも、アプリが引き続き機能する可能性はあります。 ただし、バージョンが異なる場合は、診断が難しい予期しないクラッシュがアプリで発生する場合があります。

同じ運用アプリケーションで ADAL と MSAL を実行することはサポートされていません。 ただし、テストと、ユーザーを ADAL Objective-C から iOS および macOS 用の MSAL に移行させるだけの場合は、[ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10) を引き続き使用することができます。 これは、同じアプリケーションで MSAL が動作する唯一のバージョンです。 この ADAL バージョンに対する新機能の更新プログラムは存在しないため、移行とテストの目的でのみ使用してください。 アプリで ADAL と MSAL の共存に長期間依存しないでください。

同じアプリケーション内で ADAL と MSAL を共存させることはサポートされていません。
複数のアプリケーション間で ADAL と MSAL を共存させることは完全にサポートされています。

## <a name="practical-migration-steps"></a>実際の移行手順

### <a name="app-registration-migration"></a>アプリ登録の移行

MSAL に切り替えて、AAD アカウントを有効にするために、既存の AAD アプリケーションを変更する必要はありません。 ただし、ご使用の ADAL ベースのアプリケーションでブローカー認証がサポートされていない場合は、MSAL に切り替える前に、新しいリダイレクト URI をアプリケーションに登録する必要があります。

リダイレクト URI は、`msauth.<app.bundle.id>://auth` の形式にする必要があります。 `<app.bundle.id>` をご使用のアプリケーションのバンドル ID に置き換えます。 [Azure portal](https://aka.ms/MobileAppReg) でリダイレクト URI を指定します。

iOS の場合のみ、証明書ベースの認証をサポートするには、追加のリダイレクト URI を `msauth://code/<broker-redirect-uri-in-url-encoded-form>` の形式でご使用のアプリケーションと Azure portal に登録する必要があります。 たとえば、`msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth` のように指定します。

すべてのアプリで両方のリダイレクト URI を登録することをお勧めします。

増分同意のサポートを追加する場合は、 **[API のアクセス許可]** タブのアプリの登録で、アプリがアクセスを要求するように構成されている API とアクセス許可を選択します。

ADAL から移行していて、AAD と MSA の両方のアカウントをサポートする場合は、既存のアプリケーション登録を、両方をサポートするように更新する必要があります。 AAD と MSA の両方をサポートするために、既存の運用アプリをすぐに更新することはお勧めしません。 代わりに、テスト用に AAD と MSA の両方をサポートする別のクライアント ID を作成し、すべてのシナリオが機能することを確認してから、既存のアプリを更新します。

### <a name="add-msal-to-your-app"></a>アプリに MSAL を追加する

お好みのパッケージ管理ツールを使用して、MSAL SDK をアプリに追加できます。 詳細な手順は[こちら](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation)を参照してください。

### <a name="update-your-apps-infoplist-file"></a>アプリの Info.plist ファイルを更新する

iOS の場合のみ、アプリケーションのリダイレクト URI スキームをご自分の info.plist ファイルに追加します。 ADAL ブローカーの互換アプリの場合、これは既に存在しているはずです。 既定の MSAL リダイレクト URI スキームは、`msauth.<app.bundle.id>` の形式になります。  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

次のスキームを `LSApplicationQueriesSchemes` の下でアプリの Info.plist に追加します。

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>AppDelegate コードを更新する

iOS の場合のみ、次の内容を AppDelegate.m ファイルに追加します。

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Xcode 11 を使用している場合は**、代わりに MSAL コールバックを `SceneDelegate` ファイルに配置する必要があります。
以前の iOS との互換性を保持するために UISceneDelegate と UIApplicationDelegate の両方をサポートしている場合は、MSAL コールバックを両方のファイルに配置する必要があります。

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

これにより、MSAL でブローカーと Web コンポーネントからの応答を処理することができます。
これによりアプリのデリゲート メソッドが自動的に "スウィズル" されていたため、これは ADAL では必要ありませんでした。 これを手動で追加すると、エラーが減り、アプリケーションの制御が強化されます。

### <a name="enable-token-caching"></a>トークンのキャッシュの有効化

MSAL では、既定でアプリのトークンが iOS または macOS のキーチェーンにキャッシュされます。 

トークンのキャッシュを有効化するには:
1. アプリケーションが正しく署名されていることを確認します。
2. Xcode プロジェクトの設定 > **[機能] タブ** >  **[Enable Keychain Sharing]\(キーチェーン共有を有効にする\)** に移動します。
3. [ **+** ] をクリックし、次の **[キーチェーン グループ]** エントリを入力します。3.a iOS の場合は `com.microsoft.adalcache` を入力します。3.b macOS の場合は `com.microsoft.identity.universalstorage` を入力します。

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>MSALPublicClientApplication を作成し、その acquireToken と acquireTokeSilent の呼び出しに切り替える

次のコードを使用すると、`MSALPublicClientApplication` を作成できます。

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

次に、アカウント管理 API を呼び出して、キャッシュ内にアカウントがあるかどうかを確認します。

Objective-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



または、すべてのアカウントを読み取ります。

Objective-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



アカウントが見つかった場合は、MSAL `acquireTokenSilent` API を呼び出します。

Objective-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>次のステップ

[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)の詳細を確認します