---
title: Microsoft ID プラットフォームの認証シナリオ | Azure
description: Microsoft ID プラットフォームの認証フローとアプリケーションのシナリオについて説明します。 ID 認証、トークンの取得、保護された API の呼び出しができるアプリケーションのさまざまな種類を学ぶことができます。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969c7b6c86e09ec73ccf1ffa5cc4dc9703a3d25c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963802"
---
# <a name="authentication-flows-and-application-scenarios"></a>認証フローとアプリケーションのシナリオ

Microsoft ID プラットフォーム (v2.0) エンドポイントは、さまざまなモダン アプリケーション アーキテクチャのための認証をサポートしています。 アーキテクチャはいずれも、業界標準のプロトコル [OAuth 2.0 and OpenID Connect](active-directory-v2-protocols.md) に基づいています。  アプリケーションでは、[認証ライブラリ](reference-v2-libraries.md)を使用して、ID が認証され、保護された API にアクセスするためのトークンが取得されます。

この記事では、各種の認証フローと、アプリケーションでその認証フローを使用するシナリオについて説明します。 このほか、この記事では以下を一覧にして紹介します。
- [アプリケーション シナリオとサポートされている認証フロー](#scenarios-and-supported-authentication-flows)。
- [アプリケーション シナリオとサポートされているプラットフォームおよび言語](#scenarios-and-supported-platforms-and-languages)。

## <a name="application-categories"></a>アプリケーションのカテゴリ

トークンを取得できるアプリケーションには、以下をはじめとするさまざまな種類があります。

- Web Apps
- モバイル アプリ
- デスクトップ アプリ
- Web API

また、ブラウザーがインストールされていないデバイスや IoT 上で運用されているデバイスで稼働しているアプリからも、トークンを取得できます。

アプリケーションの区分としては、次のようなものが考えられます。

- [保護されたリソースとクライアント アプリケーション](#protected-resources-vs-client-applications):Web アプリや Web API などのリソースを保護するシナリオもあれば、 保護された Web API を呼び出すためのセキュリティ トークンを取得するシナリオもあります。
- [ユーザーありとユーザーなし](#with-users-or-without-users):サインイン済みのユーザーが関与するシナリオと、デーモンなどのようにユーザーが関与しないシナリオとがあります。
- [シングルページ、パブリック クライアント、機密クライアント アプリケーション](#single-page-public-client-and-confidential-client-applications):アプリケーションの種類は、これらの 3 つの大きなカテゴリに分類されます。 それぞれ、併用するライブラリとオブジェクトが異なります。
- [サインイン対象ユーザー](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types):利用できる認証フローは、サインインの対象ユーザーによって異なります。 一部のフローは、職場または学校アカウントでのみ利用できます。 一部は、職場または学校アカウントと個人用 Microsoft アカウントのどちらでも利用できます。 許可される対象ユーザーは、認証フローによって異なります。
- [サポートされている OAuth 2.0 フロー](#scenarios-and-supported-authentication-flows):トークンを要求するアプリケーションのシナリオを実装するには、認証フローを使用します。 アプリケーション シナリオと認証フローの間に 1 対 1 の対応関係はありません。
- [サポートされているプラットフォーム](#scenarios-and-supported-platforms-and-languages):すべてのプラットフォームですべてのアプリケーションのシナリオを使用できるわけではありません。

### <a name="protected-resources-vs-client-applications"></a>保護されたリソースとクライアント アプリケーション

認証シナリオには、次の 2 つのアクティビティが含まれます。

- **保護された Web API のセキュリティ トークンの取得**:Microsoft では、トークンの取得に[認証ライブラリ](reference-v2-libraries.md#microsoft-supported-client-libraries)、特に Microsoft 認証ライブラリ (MSAL) ファミリを使用することをお勧めしています。
- **Web API (または Web アプリ) の保護**:Web API または Web アプリ リソースの保護に関する課題の 1 つに、セキュリティ トークンの検証があります。 Microsoft では、一部のプラットフォームについて[ミドルウェア ライブラリ](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)を提供しています。

### <a name="with-users-or-without-users"></a>ユーザーありまたはユーザーなし

ほとんどの認証シナリオでは、サインインしたユーザーのためにトークンを取得することになります。

![ユーザーありのシナリオ](media/scenarios/scenarios-with-users.svg)

これに対して、デーモンアプリのシナリオのように、ユーザーが存在せず、アプリケーションが自らのためにトークンを取得することもあります。

![デーモン アプリを使ったシナリオ](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>シングルページ、パブリック クライアント、機密クライアント アプリケーション

セキュリティ トークンは、さまざまなアプリケーションから取得できます。 そのようなアプリケーションは多くの場合、次の 3 つのカテゴリに分類されます。

- **シングルページ アプリケーション**:SPA とも呼ばれる Web アプリで、ブラウザーで実行している JavaScript または TypeScript アプリからトークンを取得します。 モダン アプリケーションには、主に JavaScript で記述されたシングルページのアプリケーション フロントエンドが備わっていることが少なくありません。 アプリケーションで Angular、React、Vue などのフレームワークを使用することもよくあります。 MSAL.js は、シングルページ アプリケーションをサポートする唯一の Microsoft 認証ライブラリです。

- **パブリック クライアント アプリケーション**:この種のアプリケーションでは、ユーザーのサインインが必ず発生します。
  - サインイン済みのユーザーのために Web API を呼び出すデスクトップ アプリ
  - モバイル アプリ
  - ブラウザーがインストールされていないデバイス (IoT 上で運用されているデバイスなど) で稼働しているアプリ

  これらアプリは、[PublicClientApplication](msal-client-applications.md) という名前の MSAL クラスによって表されます。

- **機密クライアント アプリケーション**:
  - Web API を呼び出す Web アプリ
  - Web API を呼び出す Web API
  - デーモン アプリケーション (Linux デーモンや Windows サービスのようにコンソール サービスとして実装されている場合も含む)

  このような種類のアプリでは、[ConfidentialClientApplication](msal-client-applications.md) クラスを使用します。

## <a name="application-scenarios"></a>アプリケーションのシナリオ

Microsoft ID プラットフォーム エンドポイントは、さまざまなアプリ アーキテクチャのための認証をサポートしています。

- シングルページ アプリ
- Web Apps
- Web API
- モバイル アプリ
- ネイティブ アプリ
- デーモン アプリ
- サーバーサイド アプリ

アプリケーションでは、さまざまな認証フローを使用してユーザーのサインインを行い、トークンを取得して保護された API を呼び出します。

### <a name="a-single-page-application"></a>シングルページ アプリケーション

モダン Web アプリの多くは、JavaScript または SPA フレームワーク (Angular、Vue.js、React.js など) を使用して記述されたクライアントサイドのシングルページ アプリケーションとして構築されています。 このようなアプリケーションは、Web ブラウザー内で稼働します。 その認証には、従来のサーバーサイド Web アプリとは異なる特徴があります。 Microsoft ID プラットフォームを使うと、シングルページ アプリケーションでユーザーをサインインさせ、バックエンド サービスまたは Web API にアクセスするためのトークンを取得することができます。

![シングルページ アプリケーション](media/scenarios/spa-app.svg)

詳細については、[シングルページ アプリケーション](scenario-spa-overview.md)に関するページを参照してください。

### <a name="a-web-app-that-is-signing-in-a-user"></a>ユーザーをサインインさせる Web アプリ

![ユーザーをサインインさせる Web アプリ](media/scenarios/scenario-webapp-signs-in-users.svg)

ユーザーをサインインさせる Web アプリを保護する方法は次のとおりです。

- 開発に .NET 環境を採用している場合には、ASP.NET Open ID Connect ミドルウェアを使用した ASP.NET または ASP.NET Core を使用します。 リソース保護の一環として発生するセキュリティ トークンの検証処理については、MSAL ライブラリではなく、[.NET ライブラリ用の IdentityModel 拡張機能](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)が担当します。

- 開発に Node.js を採用している場合には、Passport.js を使用します。

詳細については、[ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)に関するページを参照してください。

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>ユーザーをサインインさせる Web アプリと、そのユーザーのための Web API の呼び出し

![Web API を呼び出す Web アプリ](media/scenarios/web-app.svg)

ユーザーのために Web アプリから Web API を呼び出すには、MSAL の **ConfidentialClientApplication** クラスを使用します。 使用するのは認可コード フローで、取得したトークンはトークン キャッシュに格納されます。 必要に応じて、MSAL によりトークンが更新されるほか、コントローラーによりキャッシュからトークンが取得されます。

詳細については、[Web API を呼び出す Web アプリ](scenario-web-app-call-api-overview.md)に関するページを参照してください。

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>サインイン済みのユーザーのために Web API を呼び出すデスクトップ アプリ

ユーザーをサインインさせるデスクトップ アプリで Web API を呼び出す場合には、MSAL の **PublicClientApplication** クラスに用意されている対話型のトークン取得メソッドを使用します。 このような対話型メソッドを使用すると、サインイン UI のエクスペリエンスを制御できます。 MSAL では、この対話に Web ブラウザーを使用します。

![Web API を呼び出すデスクトップ アプリ](media/scenarios/desktop-app.svg)

Windows ドメインに参加しているか、Azure Active Directory (Azure AD) を使って参加しているコンピューターで稼働している Windows ホスト アプリケーションについては、もう 1 つ選択肢があります。 このようなアプリケーションでは、[統合 Windows 認証](https://aka.ms/msal-net-iwa)を使用するとトークンを自動的に取得できます。

ブラウザーがインストールされていないデバイス上で稼働しているアプリケーションであっても、ユーザーのために API を呼び出すことは可能です。 認証するには、Web ブラウザーがインストールされている別のデバイス上でユーザーがサインインする必要があります。 このシナリオでは、[デバイス コード フロー](https://aka.ms/msal-net-device-code-flow)を使用する必要があります。

![デバイス コード フロー](media/scenarios/device-code-flow-app.svg)

パブリック クライアント アプリケーションであれば[ユーザー名とパスワードを使ったフロー](https://aka.ms/msal-net-up)を使用することもできますが、お勧めはしません。 もっとも、DevOps などの一部のシナリオではこのフローが必要になります。

ただし、このフローを使用すると、アプリケーションに制約が発生します。 たとえば、アプリでこのフローを使用していると、多要素認証や条件付きアクセスを必要とするユーザーをサインインさせることができなくなります。 また、アプリケーションでシングル サインオン (SSO) のメリットを享受することもできません。

ユーザー名とパスワードを使った認証は先進認証の原則に反しており、レガシへの対応のためにのみ提供されています。

デスクトップ アプリでトークン キャッシュを永続的にする場合は、[トークン キャッシュのシリアル化をカスタマイズする](https://aka.ms/msal-net-token-cache-serialization)必要があります。 [デュアル トークン キャッシュのシリアル化](https://aka.ms/msal-net-dual-cache-serialization)を実装すると、以前の世代の認証ライブラリとの後方互換性と前方互換性のどちらも備えたトークン キャッシュを利用できるようになります。 ライブラリの具体例としては、.NET 用 Azure AD 認証ライブラリ (ADAL.NET) のバージョン 3 とバージョン 4 などがあります。

詳細については、[Web API を呼び出すデスクトップ アプリ](scenario-desktop-overview.md)に関するページを参照してください。

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>対話ユーザーのために Web API を呼び出すモバイル アプリ

モバイル アプリケーションでは、デスクトップ アプリケーションと同じように、MSAL の **PublicClientApplication** クラスに用意されている対話型のトークン取得メソッドを呼び出して、Web API を呼び出すトークンを取得します。

![Web API を呼び出すモバイル アプリ](media/scenarios/mobile-app.svg)

MSAL iOS と MSAL Android では、既定でシステム Web ブラウザーが使用されます。 もっとも、代わりに埋め込みの Web ビューを使用するように指定することもできます。 モバイル プラットフォームに依存する特異性があります。ユニバーサル Windows プラットフォーム (UWP)、iOS、または Android。

デバイス ID やデバイス登録に関連して条件付きアクセスを使用するシナリオなど、一部のシナリオでは、デバイス上に[ブローカー](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)をインストールする必要があります。 ブローカーにはたとえば、Microsoft ポータル サイト (Android)、Microsoft Authenticator (Android および iOS) があります。 このほか、MSAL もブローカーと対話できるようになりました。

> [!NOTE]
> MSAL.iOS、MSAL.Android、または MSAL.NET on Xamarin を使用しているモバイル アプリでは、アプリ保護ポリシーを適用できます。 このポリシーを使うと、保護されているテキストをユーザーがコピーできないようにしたりすることができます。 モバイル アプリは [Intune によって管理され](https://docs.microsoft.com/intune/app-sdk)、Intune によりマネージド アプリとして認識されます。 [Intune SDK](https://docs.microsoft.com/intune/app-sdk-get-started) は MSAL ライブラリとは別のものであり、独自に Azure AD と対話します。

詳細については、[Web API を呼び出すモバイル アプリ](scenario-mobile-overview.md)に関するページを参照してください。

### <a name="a-protected-web-api"></a>保護された Web API

Microsoft ID プラットフォーム エンドポイントを使用すると、アプリの RESTful Web API などの Web サービスをセキュリティで保護できます。 保護された Web API の呼び出しにあたっては、API のデータをセキュリティで保護すると共に、受信要求を認証できるように、アクセス トークンを使用します。 Web API の呼び出し元によって、HTTP 要求の Authorization ヘッダーにアクセス トークンが付加されます。

ASP.NET または ASP.NET Core Web API を保護する場合は、アクセス トークンを検証する必要があります。 この検証には、ASP.NET JWT ミドルウェアを使用します。 検証は MSAL.NET ではなく、[.NET ライブラリ用の IdentityModel 拡張機能](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)によって行われます。

詳細については、[保護された Web API](scenario-protected-web-api-overview.md)に関するページを参照してください。

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>ユーザーのために別の Web API を呼び出す Web API

ASP.NET または ASP.NET Core で保護された Web API からユーザーのために別の Web API を呼び出すには、アプリでダウンストリームの Web API に関するトークンを取得する必要があります。 これは、**ConfidentialClientApplication** クラスの [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) メソッドを呼び出すことによって行われます。 このような呼び出しは、サービス間呼び出しとも呼ばれます。 他の Web API を呼び出す Web API では、カスタム キャッシュのシリアル化を提供する必要があります。

  ![別の Web API を呼び出す Web API](media/scenarios/web-api.svg)

詳細については、[Web API を呼び出す Web API](scenario-web-api-call-api-overview.md) に関するページを参照してください。

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>デーモンの名前で Web API を呼び出すデーモン アプリ

長時間実行されるプロセスを含んだアプリや、ユーザーの介入なしで動作するアプリも、セキュリティで保護された Web API になんらかの形でアクセスする必要があります。 そのようなアプリでは、認証やトークンの取得にユーザーの委任された ID ではなく、アプリの ID を使用します。 アプリの ID 証明には、クライアント シークレットまたは証明書が使用されます。

MSAL の **ConfidentialClientApplication** クラスの[クライアント資格情報](https://aka.ms/msal-net-client-credentials)取得メソッドを使用すると、呼び出し元のアプリのためにトークンを取得するデーモン アプリを作成できます。 この種のメソッドを使用するためには、呼び出し元のアプリのシークレットを Azure AD に登録しておく必要があります。 そのうえで、そのアプリと呼び出されたデーモンとの間でシークレットが共有されます。 シークレットには、アプリケーションのパスワード、証明書アサーション、クライアント アサーションなどがあります。

![他のアプリと API によって呼び出されるデーモン アプリ](media/scenarios/daemon-app.svg)

詳細については、[Web API を呼び出すデーモン アプリケーション](scenario-daemon-overview.md)に関するページを参照してください。

## <a name="scenarios-and-supported-authentication-flows"></a>シナリオとサポートされている認証フロー

トークンの取得に関連するシナリオは、「[Microsoft ID プラットフォームのプロトコル](active-directory-v2-protocols.md)」で詳しく説明している OAuth 2.0 の認証フローにも対応しています。

<table>
 <thead>
  <tr><th>シナリオ</th> <th>詳細なシナリオのチュートリアル</th> <th>OAuth 2.0 のフローと許可</th> <th>対象ユーザー</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">シングルページ アプリ</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">暗黙的</a></td>
   <td>職場または学校アカウント、個人用アカウント、Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">ユーザーをサインインさせる Web アプリ</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">承認コード</a></td>
   <td>職場または学校アカウント、個人用アカウント、Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Web API を呼び出す Web アプリ</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">承認コード</a></td>
   <td>職場または学校アカウント、個人用アカウント、Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Web API を呼び出すデスクトップ アプリ</a></td>
   <td>対話型 (PKCE を使用した<a href="v2-oauth2-auth-code-flow.md">認可コード</a>を利用)</td>
   <td>職場または学校アカウント、個人用アカウント、Azure AD B2C</td>
 </tr>

  <tr>
   <td>統合 Windows 認証</td>
   <td>職場または学校アカウント</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">リソース所有者のパスワード</a></td>
   <td>職場または学校アカウントと Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">デバイス コード</a></td>
   <td>職場または学校アカウント</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Web API を呼び出すモバイル アプリ</a></td>
   <td>対話型 (PKCE を使用した<a href="v2-oauth2-auth-code-flow.md">認可コード</a>を利用)</td>
   <td>職場または学校アカウント、個人用アカウント、Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">リソース所有者のパスワード</a></td>
   <td>職場または学校アカウントと Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Web API を呼び出すデーモン アプリ</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">クライアントの資格情報</a></td>
   <td>ユーザーが介在せず、Azure AD 組織でのみ使用されるアプリ専用アクセス許可</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Web API を呼び出す Web API</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">代理</a></td>
   <td>職場または学校アカウントと個人用アカウント</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>シナリオとサポートされているプラットフォームと言語

Microsoft の認証ライブラリは、さまざまなプラットフォームをサポートしています。

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- ネイティブ iOS
- macOS
- ネイティブ Android
- Java
- Python

また、さまざまな言語を使用してアプリケーションをビルドすることもできます。 アプリケーションの種類によっては、プラットフォームで利用できないことがあります。

次の表の Windows の列で .NET Core と書いてある場合には、.NET Framework でも対応可能です。 後者は表のスペースの関係で省略しています。

|シナリオ  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [シングルページ アプリ](scenario-spa-overview.md) <br/>[![シングルページ アプリ](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md) <br/>[![ユーザーをサインインさせる Web アプリ](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Web API を呼び出す Web アプリ](scenario-web-app-call-api-overview.md) <br/> <br/>[![Web API を呼び出す Web アプリ](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [Web API を呼び出すデスクトップ アプリ](scenario-desktop-overview.md) <br/> <br/>[![Web API](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![デバイス コード フローを呼び出すデスクトップ アプリ](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS / Objective C または Swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Web API を呼び出すモバイル アプリ](scenario-mobile-overview.md) <br/> [![Web API を呼び出すモバイル アプリ](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Objective C または Swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [デーモン アプリ](scenario-daemon-overview.md) <br/> [![デーモン アプリ](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Web API を呼び出す Web API](scenario-web-api-call-api-overview.md) <br/><br/> [![Web API を呼び出す Web API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

「[OS/言語別で Microsoft がサポートするライブラリ](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)」も参照してください。

## <a name="next-steps"></a>次の手順
[認証の基本](authentication-scenarios.md)と[アクセス トークン](access-tokens.md)の詳細を学習します。
