---
title: MSAL 認証フロー | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) で使用される認証フローと許可について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 78932e5852453fe996e26a278f8a1859a8ecf546
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755012"
---
# <a name="authentication-flows"></a>認証フロー

Microsoft Authentication Library (MSAL) では、さまざまなアプリケーションのシナリオで使用できる複数の認証フローがサポートされています。

| Flow | 説明 | 使用される場所 |
|--|--|--|
| [承認コード](#authorization-code) | デバイスにインストールされているアプリで、Web API などの保護されたリソースにアクセスするために使用されます。 モバイル アプリおよびデスクトップ アプリにサインインおよび API アクセスを追加できます。 | [デスクトップ アプリ](scenario-desktop-overview.md)、[モバイル アプリ](scenario-mobile-overview.md)、[Web アプリ](scenario-web-app-call-api-overview.md) |
| [クライアントの資格情報](#client-credentials) | アプリケーションの ID を使って Web でホストされているリソースにアクセスできます。 バックグラウンドでの実行が必要なサーバー間の相互作用に使用され、ユーザーとの即時の相互動作は必要ありません。 | [デーモン アプリ](scenario-daemon-overview.md) |
| [デバイス コード](#device-code) | ユーザーは、スマート TV、IoT デバイス、プリンターなどの入力制限のあるデバイスにサインインできます。 | [デスクトップ/モバイル アプリ](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [暗黙的な許可](#implicit-grant) | バックエンド サーバーと資格情報をやり取りせずに、アプリはトークンを取得できます。 アプリは、ユーザーのサインイン、セッションの維持、他の Web API へのトークンの取得を、すべてクライアント JavaScript コード内で実行できます。 | [シングル ページ アプリケーション (SPA)](scenario-spa-overview.md) |
| [On-Behalf-Of](#on-behalf-of) | アプリケーションでサービスまたは Web API を呼び出し、それがさらに別のサービスまたは Web API を呼び出す必要があります。 その考え方は、委任されたユーザー ID とアクセス許可を要求チェーン経由で伝達するというものです。 | [Web API](scenario-web-api-call-api-overview.md) |
| [ユーザー名/パスワード](#usernamepassword) | アプリケーションはパスワードを直接処理することによって、ユーザーをサインインさせることができます。 このフローは推奨されません。 | [デスクトップ/モバイル アプリ](scenario-desktop-acquire-token.md#username-and-password) |
| [統合 Windows 認証](#integrated-windows-authentication) | ドメインまたは Azure Active Directory (Azure AD) に参加しているコンピューターのアプリケーションは、サイレントで (ユーザーからの UI 操作なしで) トークンを取得できます。 | [デスクトップ/モバイル アプリ](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>各フローがトークンとコードを生成する方法

クライアント アプリケーションの構築方法に応じて、Microsoft ID プラットフォームでサポートされている認証フローの 1 つまたは複数を使用できます。 これらのフローでは、いくつかの種類のトークンと承認コードを生成でき、それらを機能させるためにさまざまなトークンが必要です。

| Flow                                                                               | 必要            | id_token | アクセス トークン | 更新トークン | 承認コード |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [承認コード フロー](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [クライアントの資格情報](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (アプリのみ) |               |                    |
| [デバイス コード フロー](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [暗黙的なフロー](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [On-Behalf-Of フロー](v2-oauth2-on-behalf-of-flow.md)                                | アクセス トークン        | x        | x            | x             |                    |
| [ユーザー名/パスワード](v2-oauth-ropc.md) (ROPC)                                       | ユーザー名とパスワード | x        | x            | x             |                    |
| [ハイブリッド OIDC フロー](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [更新トークンの使用](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | 更新トークン       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>対話型と非対話型の認証

これらのフローのいくつかでは、対話型と非対話型の両方のトークンの取得がサポートされています。

  - **対話型** とは、ユーザーに入力を求めることができることを意味します。 たとえば、ユーザーにログインを要求したり、多要素認証 (MFA) を実行したり、リソースに対する追加の同意を付与したりすることができます。
  - **非対話型**、つまり *サイレント* の認証では、ログイン サーバーがユーザーに追加情報の入力を求めることが *できない* 方法でトークンを取得しようとします。

MSAL ベースのアプリケーションでは、最初にトークンを *サイレントで* 取得しようとします。その後、非対話型方法が失敗した場合にのみ対話型で取得しようとします。 このパターンの詳細については、「[Microsoft Authentication Library (MSAL) を使用してトークンを取得し、キャッシュする](msal-acquire-cache-tokens.md)」を参照してください。

## <a name="authorization-code"></a>Authorization code (承認コード)

デバイスにインストールされているアプリに、Web API など、保護されているリソースにアクセスする権利を与えるために [OAuth 2 認証コード付与](v2-oauth2-auth-code-flow.md)を利用できます。 これにより、モバイル アプリおよびデスクトップ アプリにサインインおよび API アクセスを追加できます。

ユーザーが Web アプリケーション (Web サイト) にサインインするとき、Web アプリケーションは承認コードを受け取ります。 承認コードと引き換えに、Web API を呼び出すためのトークンを取得します。

![承認コード フローの図](media/msal-authentication-flows/authorization-code.png)

前の図で、アプリケーションは:

1. アクセス トークンと引き換えられる承認コードを要求します。
2. アクセス トークンを使用して Web API を呼び出します。

### <a name="considerations"></a>考慮事項

- 承認コードは、トークンの引き換えに 1 回だけ使用できます。 同じ承認コードで何回もトークンを取得しないでください。これは、プロトコル標準の仕様で明確に禁止されているためです。 意図的に、またはフレームワークでも自動的に行われていたことを知らなかったために、何回もコードを引き換えた場合は、次のエラーが発生します。

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>クライアントの資格情報

[OAuth 2 クライアントの資格情報フロー](v2-oauth2-client-creds-grant-flow.md)により、アプリケーションの ID を使って Web でホストされているリソースにアクセスできます。 この種類の許可は、バックグラウンドでの実行が必要なサーバー間の相互作用に使用され、ユーザーとの即時の相互動作は必要ありません。 これらのアプリケーションは、デーモンまたはサービス アカウントと呼ばれます。

クライアント資格情報許可フローでは、Web サービス (Confidential クライアント) が別の Web サービスを呼び出すときに、ユーザーを偽装する代わりに、独自の資格情報を使用して認証することができます。 このシナリオでは、クライアントは通常、中間層の Web サービス、デーモン サービス、または Web サイトです。 高いレベルの保証では、Microsoft ID プラットフォームにより、呼び出し元サービスが、資格情報として (共有シークレットではなく) 証明書を使用することもできます。

> [!NOTE]
> モバイル プラットフォーム (UWP、Xamarin.iOS、Xamarin.Android など) では、パブリック クライアント アプリケーションのみがサポートされているため、機密性の高いクライアント フローは利用できません。 パブリック クライアント アプリケーションでは、ID プロバイダーに対してアプリケーションの ID を証明する方法がわかりません。 Web アプリまたは Web API バックエンドでは、証明書をデプロイすることで、セキュリティ保護された接続を実現できます。

### <a name="application-secrets"></a>アプリケーション シークレット

![パスワードを使用する機密クライアントの図](media/msal-authentication-flows/confidential-client-password.png)

前の図で、アプリケーションは:

1. アプリケーションのシークレットまたはパスワード資格情報を使用してトークンを取得します。
2. トークンを使用してリソースの要求を行います。

### <a name="certificates"></a>証明書

![証明書を使用する機密クライアントの図](media/msal-authentication-flows/confidential-client-certificate.png)

前の図で、アプリケーションは:

1. 証明書の資格情報を使用してトークンを取得します。
2. トークンを使用してリソースの要求を行います。

これらのクライアント資格情報では以下のことが必要です。

- Azure AD に登録されている。
- コード内の機密クライアント アプリケーションのオブジェクトの構築時に渡される。

## <a name="device-code"></a>デバイス コード

[OAuth 2 デバイス コード フロー](v2-oauth2-device-code.md)では、ユーザーがスマート TV、IoT デバイス、プリンターなどの入力制限のあるデバイスにサインインできます。 Azure AD による対話型認証には Web ブラウザーが必要です。 Web ブラウザーを提供しないデバイスまたはオペレーティング システムでは、ユーザーはデバイス コード フローによって別のデバイス (コンピューターや携帯電話など) を使用して対話形式でサインインできます。

デバイス コード フローを使用すると、アプリケーションでは、これらのデバイスおよびオペレーティング システム用に設計された 2 ステップ プロセスを通じてトークンを取得します。 このようなアプリケーションには、IoT デバイスで実行されているアプリケーションやコマンドライン インターフェイス (CLI) ツールなどがあります。

![デバイス コード フローの図](media/msal-authentication-flows/device-code.png)

前の図で:

1. ユーザー認証が必要になるたびに、アプリがコードを提供し、ユーザーに別のデバイス (インターネットに接続されたスマートフォンなど) を使用して特定の URL (たとえば、`https://microsoft.com/devicelogin`) に移動するよう求めます。 その後、ユーザーはコードの入力を求められ、必要に応じて、同意のプロンプトや[多要素認証](../authentication/concept-mfa-howitworks.md)を含む通常の認証エクスペリエンスが実行されます。
1. 認証が成功すると、コマンドライン アプリはバック チャネル経由で必要なトークンを受信し、それらを使用して必要な Web API の呼び出しを実行します。

### <a name="constraints"></a>制約

- デバイス コード フローは、パブリック クライアント アプリケーションでのみ使用できます。
- パブリック クライアント アプリケーションを構築するときに渡される機関は、次のいずれかである必要があります。
  - テナント化 (`https://login.microsoftonline.com/{tenant}/,` の形式)。`{tenant}` は、テナント ID を表す GUID またはテナントに関連付けられているドメイン名。
  - `https://login.microsoftonline.com/organizations/` の形式の職場および学校のアカウントの場合。

## <a name="implicit-grant"></a>Implicit grant (暗黙的な付与)

[OAuth 2 の暗黙的な付与のフロー](v2-oauth2-implicit-grant-flow.md)では、バックエンド サーバーとの資格情報交換を実行しなくても、アプリが Microsoft ID プラットフォームからトークンを取得できます。 このフローにより、アプリは、ユーザーのサインイン、セッションの維持、他の Web API のトークンの取得を、すべてクライアント JavaScript コード内で実行できます。

![暗黙的な許可のフローの図](media/msal-authentication-flows/implicit-grant.svg)

最近の Web アプリケーションの多くは、JavaScript または SPA フレームワーク (Angular、Vue.js、React.js など) で記述されたクライアント側のシングルページ アプリケーションとして構築されています。 これらのアプリケーションは、Web ブラウザーで実行され、従来のサーバー側 Web アプリケーションとは異なる認証特性を持ちます。 Microsoft ID プラットフォームにより、シングルページ アプリケーションでユーザーをサインインさせ、暗黙的な許可のフローを使用してバックエンド サービスまたは Web API にアクセスするトークンを取得できます。 暗黙的フローにより、アプリケーションから、認証されたユーザーを表す ID トークンを取得し、保護された API の呼び出しに必要なトークンにもアクセスできます。

この認証フローには、Electron や React-Native などのクロスプラットフォーム JavaScript フレームワークを使用するアプリケーションのシナリオは含まれません。これらでは、さらにネイティブ プラットフォームと対話する機能が必要であるためです。

暗黙的モードで発行されたトークンには、URL によってブラウザーに返されるために **長さの制限** があります (`response_mode` は `query` または `fragment` のいずれか)。 一部のブラウザーでは、ブラウザーのバー内の URL の長さが制限され、長すぎると失敗します。 そのため、これらの暗黙的なフロー トークンには `groups` または `wids` 要求が含まれていません。

## <a name="on-behalf-of"></a>On-Behalf-Of

[OAuth 2 の On-Behalf-Of 認証フロー](v2-oauth2-on-behalf-of-flow.md)は、アプリケーションでサービスまたは Web API を呼び出し、それがさらに別のサービスまたは Web API を呼び出す必要がある場合に、使用されます。 その考え方は、委任されたユーザー ID とアクセス許可を要求チェーン経由で伝達するというものです。 中間層サービスがダウンストリーム サービスに認証済み要求を発行するには、そのサービスは Microsoft ID プラットフォームからのアクセス トークンをユーザーに *代わって* セキュリティ保護する必要があります。

![on-behalf-of フローの図](media/msal-authentication-flows/on-behalf-of.png)

前の図で:

1. アプリケーションは Web API 用のアクセス トークンを取得します。
2. クライアント (Web、デスクトップ、モバイル、またはシングルページ アプリケーション) が保護された Web API を呼び出し、HTTP 要求の認証ヘッダーのベアラー トークンとしてアクセス トークンを追加します。 Web API がユーザーを認証します。
3. クライアントが Web API を呼び出すと、Web API はユーザーの代わりに別のトークンを要求します。
4. 保護された Web API は、このトークンを使用して、ユーザーの代わりにダウンストリームの Web API を呼び出します。 Web API は、他のダウンストリーム API のトークンを (ただし、ここでも同じユーザーの代わりとして) 後で要求することもできます。

## <a name="usernamepassword"></a>ユーザー名/パスワード

[OAuth 2 のリソース所有者のパスワード資格情報](v2-oauth-ropc.md) (ROPC) の付与により、アプリケーションでは、ユーザーのパスワードを直接処理することでユーザーをサインインさせることができます。 デスクトップ アプリケーションでは、ユーザー名/パスワードのフローを使ってサイレントにトークンを取得できます。 アプリケーションを使用するときに UI は必要ありません。

![ユーザー名/パスワードのフローの図](media/msal-authentication-flows/username-password.png)

前の図で、アプリケーションは:

1. ID プロバイダーにユーザー名とパスワードを送信することによって、トークンを取得します。
2. トークンを使用して Web API を呼び出します。

> [!WARNING]
> このフローは推奨されません。 高度な信頼と資格情報の公開が要求されます。 このフローは、より安全なフローを使用できない場合 *にのみ* 使用してください。 詳細については、[深刻化するパスワードの問題の解決策](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)に関する記事を参照してください。

Windows ドメイン参加済みマシン上でトークンを自動的に取得するために推奨されるフローは、[統合 Windows 認証](#integrated-windows-authentication)です。 それ以外の場合は、[デバイス コード フロー](#device-code)を使用してください。

DevOps のような一部のシナリオではユーザー名/パスワードのフローが役立つ場合もありますが、独自の UI を提供する対話形式のシナリオでユーザー名/パスワードを使用する場合は、それを避けてください。

ユーザー名/パスワードを使用すると:

- 対話式操作がないため、多要素認証を実行する必要のあるユーザーがサインインできない。
- ユーザーがシングル サインオンできない。

### <a name="constraints"></a>制約

[統合 Windows 認証の制約](#integrated-windows-authentication)以外に、次の制約も適用されます。

- ユーザー名/パスワードのフローは、条件付きアクセスと多要素認証との互換性がありません。 その結果、テナント管理者が多要素認証を必要とする Azure AD テナントでアプリを実行する場合は、このフローを使用できません。 これは多くの組織に該当します。
- ROPC は職場および学校アカウントにのみ有効です。 Microsoft アカウント (MSA) に ROPC を使用することはできません。
- そのフローは、.NET デスクトップと .NET Core では使用できますが、ユニバーサル Windows プラットフォームでは使用できません。
- Azure AD B2C では、ROPC フローはローカル アカウントに対してのみ機能します。 MSAL.NET と Azure AD B2C の ROPC の詳細については、[Azure AD B2C での ROPC の使用](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)に関する記事を参照してください。

## <a name="integrated-windows-authentication"></a>統合 Windows 認証

MSAL では、ドメイン参加済みまたは Azure AD 参加済みの Windows コンピューターで実行されているデスクトップやモバイル アプリケーションに対して統合 Windows 認証 (IWA) がサポートされています。 IWA を使用して、これらのアプリケーションは、ユーザーによる UI 操作なしでトークンをサイレントに取得できます。

![統合 Windows 認証の図](media/msal-authentication-flows/integrated-windows-authentication.png)

前の図で、アプリケーションは:

1. 統合 Windows 認証を使用してトークンを取得します。
2. トークンを使用してリソースの要求を行います。

### <a name="constraints"></a>制約

統合 Windows 認証 (IWA) では、フェデレーション ユーザー (Active Directory で作成され、Azure AD によってサポートされているユーザー) *のみ* がサポートされています。 Azure AD で直接作成され、Active Directory による支援のないユーザー (マネージド ユーザー) は、この認証フローを使用できません。 この制限は、[ユーザー名/パスワードのフロー](#usernamepassword)には影響しません。

IWA は、.NET Framework、.NET Core、およびユニバーサル Windows プラットフォームのアプリケーションを対象としています。

IWA では多要素認証はバイパスされません。 多要素認証が構成されていると、多要素認証チャレンジが必要な場合に IWA が失敗する可能性があります。 多要素認証はユーザーの対話を要求します。

2 要素認証の実行を ID プロバイダーが要求するタイミングは制御できません。 テナント管理者が行います。 通常、2 要素認証が必要なのは、他の国またはリージョンからサインインする場合、VPN 経由で企業ネットワークに接続していない場合、および、VPN 経由で接続している特定の状況下です。 Azure AD では、AI を使用して、2 要素認証が必要かどうかを継続的に学習します。 IWA が失敗した場合は、[対話ユーザー プロンプト](#interactive-and-non-interactive-authentication)にフォール バックする必要があります。

パブリック クライアント アプリケーションを構築するときに渡される機関は、次のいずれかである必要があります。

- テナント化 (`https://login.microsoftonline.com/{tenant}/,` の形式)。`{tenant}` は、テナント ID を表す GUID またはテナントに関連付けられているドメイン名。
- 任意の職場および学校アカウント用 (`https://login.microsoftonline.com/organizations/`)。 Microsoft の個人用アカウント (MSA) はサポートされていません。`/common` テナントまたは `/consumers` テナントを使用することはできません。

IWA はサイレント フローであるため、次のいずれかが当てはまる必要があります。

- アプリケーションのユーザーが、アプリケーションの使用に事前に同意しておく必要があります。
- テナント管理者が、テナント内のすべてのユーザーによるアプリケーションの使用に事前に同意しておく必要があります。

これは、次のいずれかが当てはまることを意味します。

- 開発者が Azure portal で自分用に **[許可]** を選択しておきます。
- テナント管理者が Azure portal のアプリ登録の **[API のアクセス許可]** タブにある **[{テナント ドメイン} の管理者の同意を付与/取り消す]** を選択しておきます (「[Web API にアクセスするためのアクセス許可を追加する](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)」を参照)。
- ユーザーがアプリケーションに同意する方法を指定しておきます (「[個々のユーザーの同意を要求する](v2-permissions-and-consent.md#requesting-individual-user-consent)」を参照)。
- テナント管理者がアプリケーションに同意する方法を指定しておきます ([管理者の同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)に関するセクションを参照)。

IWA フローは、.NET デスクトップ、.NET Core、および Windows ユニバーサル プラットフォームの各アプリに対して有効です。

同意の詳細については、[v2.0 のアクセス許可と同意](v2-permissions-and-consent.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Microsoft Authentication Library (MSAL) でサポートされている認証フローを確認したので、次のフローで使用されるトークンの取得とキャッシュについて確認します。

[Microsoft Authentication Library (MSAL) を使用してトークンを取得し、キャッシュする](msal-acquire-cache-tokens.md)
