---
title: Microsoft ID プラットフォームの認証フローとアプリのシナリオ | Azure
description: ID の認証、トークンの取得、保護された API の呼び出しなど、Microsoft ID プラットフォームのアプリケーション シナリオについて説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: ffa52805a5e2680d534b2b24a210465cb3fc7cac
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100557861"
---
# <a name="authentication-flows-and-application-scenarios"></a>認証フローとアプリケーションのシナリオ

Microsoft ID プラットフォームは、さまざまなモダン アプリケーション アーキテクチャのための認証をサポートしています。 アーキテクチャはいずれも、業界標準のプロトコル [OAuth 2.0 and OpenID Connect](active-directory-v2-protocols.md) に基づいています。 アプリケーションでは、[Microsoft ID プラットフォームの認証ライブラリ](reference-v2-libraries.md)を使用して ID が認証され、保護された API にアクセスするためのトークンが取得されます。

この記事では、認証フローと、アプリケーションでそれらを使用するシナリオについて説明します。

## <a name="application-categories"></a>アプリケーションのカテゴリ

トークンを取得できるアプリケーションには、以下をはじめとするいくつかの種類があります。

- Web Apps
- モバイル アプリ
- デスクトップ アプリ
- Web API

また、ブラウザーがインストールされていないデバイスやモノのインターネット (IoT) 上で運用されているデバイスで稼働しているアプリからも、トークンを取得できます。

以降のセクションでは、アプリケーションのカテゴリについて説明します。

### <a name="protected-resources-vs-client-applications"></a>保護されたリソースとクライアント アプリケーション

認証シナリオには、次の 2 つのアクティビティが含まれます。

- **保護された Web API のセキュリティ トークンの取得**:Microsoft が開発し、サポートしている [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) の使用をお勧めします。
- **Web API (または Web アプリ) の保護**:これらのリソースの保護に関する課題の 1 つに、セキュリティ トークンの検証があります。 Microsoft では、一部のプラットフォームについて[ミドルウェア ライブラリ](reference-v2-libraries.md)を提供しています。

### <a name="with-users-or-without-users"></a>ユーザーありまたはユーザーなし

ほとんどの認証シナリオでは、サインインしたユーザーのためにトークンを取得することになります。

![ユーザーありのシナリオ](media/scenarios/scenarios-with-users.svg)

ただし、デーモン アプリも存在します。 それらのシナリオではユーザーが存在せず、アプリケーションが自らのためにトークンを取得します。

![デーモン アプリを使ったシナリオ](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>シングルページ、パブリック クライアント、機密クライアント アプリケーション

セキュリティ トークンは、さまざまなアプリケーションから取得できます。 そのようなアプリケーションは多くの場合、次の 3 つのカテゴリに分類されます。 それぞれ、併用するライブラリとオブジェクトが異なります。

- **シングルページ アプリケーション**:SPA とも呼ばれる Web アプリで、ブラウザーで実行している JavaScript または TypeScript アプリからトークンを取得します。 モダン アプリケーションには、主に JavaScript で記述されたシングルページのアプリケーションがフロントエンドに備わっていることが少なくありません。 アプリケーションで Angular、React、Vue などのフレームワークを使用することもよくあります。 MSAL.js は、シングルページ アプリケーションをサポートする唯一の Microsoft 認証ライブラリです。

- **パブリック クライアント アプリケーション**:このカテゴリのアプリは、常にユーザーをサインインさせます。たとえば、次のタイプのアプリがあります。
  - サインイン ユーザーの代わりに Web API を呼び出すデスクトップ アプリケーション
  - モバイル アプリ
  - ブラウザーがインストールされていないデバイス (IoT 上で運用されているデバイスなど) で稼働しているアプリ

- **機密クライアント アプリケーション**:このカテゴリには、次のようなアプリが該当します。
  - Web API を呼び出す Web アプリ
  - Web API を呼び出す Web API
  - デーモン アプリケーション (Linux デーモンや Windows サービスのようにコンソール サービスとして実装されている場合も含む)

### <a name="sign-in-audience"></a>サインイン対象ユーザー

利用できる認証フローは、サインインの対象ユーザーによって異なります。 一部のフローは、職場または学校アカウントでのみ利用できます。 また、職場または学校アカウントと個人用 Microsoft アカウントのどちらでも利用できるフローもあります。

詳細については、「[サポートされているアカウントの種類](v2-supported-account-types.md#account-type-support-in-authentication-flows)」を参照してください。

## <a name="application-scenarios"></a>アプリケーションのシナリオ

Microsoft ID プラットフォームでは、これらのアプリ アーキテクチャのための認証がサポートされています。

- シングルページ アプリ
- Web Apps
- Web API
- モバイル アプリ
- ネイティブ アプリ
- デーモン アプリ
- サーバーサイド アプリ

アプリケーションでは、さまざまな認証フローを使用してユーザーのサインインを行い、トークンを取得して保護された API を呼び出します。

### <a name="single-page-application"></a>シングルページ アプリ

最新の Web アプリの多くは、クライアント側のシングル ページ アプリケーションとして構築されています。 これらのアプリケーションでは、JavaScript またはフレームワーク (Angular、Vue、React など) が使用されています。 このようなアプリケーションは、Web ブラウザー内で稼働します。

シングルページ アプリケーションは、認証の特性の点で、従来からあるサーバー側の Web アプリとは異なります。 Microsoft ID プラットフォームを使うと、シングルページ アプリケーションでユーザーをサインインさせ、バックエンド サービスまたは Web API にアクセスするためのトークンを取得することができます。 Microsoft ID プラットフォームでは、JavaScript アプリケーション用の 2 つの付与タイプが提供されています。

| MSAL.js (2.x) | MSAL.js (1.x) |
|---|---|
| ![シングルページ アプリケーション認証](media/scenarios/spa-app-auth.svg) | ![シングルページ アプリケーション暗黙](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>ユーザーをサインインさせる Web アプリ

![ユーザーをサインインさせる Web アプリ](media/scenarios/scenario-webapp-signs-in-users.svg)

ユーザーをサインインさせる Web アプリを効果的に保護するには、次の方法を使用します。

- 開発に .NET 環境を採用している場合には、ASP.NET OpenID Connect ミドルウェアを使用した ASP.NET または ASP.NET Core を使用します。 リソース保護の一環として発生するセキュリティ トークンの検証処理については、MSAL ライブラリではなく、[.NET 用の IdentityModel 拡張機能](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)が担当します。

- 開発に Node.js を採用している場合には、[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) または [Passport.js](https://github.com/AzureAD/passport-azure-ad) を使用します。

詳細については、[ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)に関するページを参照してください。

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>ユーザーをサインインさせ、そのユーザーに代わって Web API を呼び出す Web アプリ

![Web API を呼び出す Web アプリ](media/scenarios/web-app.svg)

ユーザーに代わって Web アプリから Web API を呼び出すには、承認コード フローを使用し、取得したトークンをトークン キャッシュに格納します。 必要に応じて、MSAL によりトークンが更新されるほか、コントローラーによりキャッシュからトークンが取得されます。

詳細については、[Web API を呼び出す Web アプリ](scenario-web-app-call-api-overview.md)に関するページを参照してください。

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>サインイン済みのユーザーに代わって Web API を呼び出すデスクトップ アプリ

ユーザーをサインインさせるデスクトップ アプリで Web API を呼び出す場合には、MSAL に用意されている対話型のトークン取得メソッドを使用します。 このような対話型メソッドを使用すると、サインイン UI のエクスペリエンスを制御できます。 MSAL では、この対話に Web ブラウザーを使用します。

![Web API を呼び出すデスクトップ アプリ](media/scenarios/desktop-app.svg)

Windows ドメインに参加しているか、Azure Active Directory (Azure AD) を使って参加しているコンピューターで稼働している Windows ホスト アプリケーションについては、もう 1 つ選択肢があります。 このようなアプリケーションでは、[統合 Windows 認証](https://aka.ms/msal-net-iwa)を使用するとトークンを自動的に取得できます。

ブラウザーがインストールされていないデバイス上で稼働しているアプリケーションであっても、ユーザーのために API を呼び出すことは可能です。 認証するには、Web ブラウザーがインストールされている別のデバイス上でユーザーがサインインする必要があります。 このシナリオでは、[デバイス コード フロー](https://aka.ms/msal-net-device-code-flow)を使用する必要があります。

![デバイス コード フロー](media/scenarios/device-code-flow-app.svg)

パブリック クライアント アプリケーションであれば[ユーザー名とパスワードを使ったフロー](scenario-desktop-acquire-token.md#username-and-password)を使用することもできますが、お勧めはしません。 もっとも、DevOps などの一部のシナリオではこのフローが必要になります。

ユーザー名とパスワードを使ったフローを使用すると、アプリケーションに制約が発生します。 たとえば、アプリケーションでは、Azure AD の多要素認証や条件付きアクセス ツールを使用する必要があるユーザーをサインインさせることができなくなります。 また、アプリケーションでシングル サインオン (SSO) のメリットを享受することもできません。 ユーザー名とパスワードを使った認証は先進認証の原則に反しており、レガシへの対応のためにのみ提供されています。

デスクトップ アプリでトークン キャッシュを永続的にする場合は、[トークン キャッシュのシリアル化](scenario-desktop-acquire-token.md#file-based-token-cache)をカスタマイズすることができます。 [デュアル トークン キャッシュのシリアル化](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3)を実装すると、後方互換性と前方互換性を備えたトークン キャッシュを利用できるようになります。 これらのトークンでは、以前の世代の認証ライブラリがサポートされます。 ライブラリの具体例としては、.NET 用 Azure AD 認証ライブラリ (ADAL.NET) のバージョン 3 とバージョン 4 などがあります。

詳細については、[Web API を呼び出すデスクトップ アプリ](scenario-desktop-overview.md)に関するページを参照してください。

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>対話ユーザーに代わって Web API を呼び出すモバイル アプリ

モバイル アプリケーションでは、デスクトップ アプリケーションと同じように、MSAL に用意されている対話型のトークン取得メソッドを呼び出して、Web API を呼び出すためのトークンを取得します。

![Web API を呼び出すモバイル アプリ](media/scenarios/mobile-app.svg)

MSAL iOS と MSAL Android では、既定でシステム Web ブラウザーが使用されます。 もっとも、代わりに埋め込みの Web ビューを使用するように指定することもできます。 モバイル プラットフォームに依存する特異性があります。ユニバーサル Windows プラットフォーム (UWP)、iOS、または Android。

デバイス ID やデバイス登録に関連して条件付きアクセスを使用するシナリオなど、一部のシナリオでは、デバイス上にブローカーをインストールする必要があります。 ブローカーにはたとえば、Microsoft ポータル サイト (Android)、Microsoft Authenticator (Android および iOS) があります。 MSAL はブローカーと対話できるようになりました。 ブローカーの詳細については、「[Android と iOS でブローカーを利用する](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)」を参照してください。

詳細については、[Web API を呼び出すモバイル アプリ](scenario-mobile-overview.md)に関するページを参照してください。

> [!NOTE]
> MSAL.iOS、MSAL.Android、または MSAL.NET on Xamarin を使用しているモバイル アプリでは、アプリ保護ポリシーを適用できます。 このポリシーを使うと、保護されているテキストをユーザーがコピーできないようにしたりすることができます。 モバイル アプリは Intune によって管理され、Intune によりマネージド アプリとして認識されます。 詳細については、「[Microsoft Intune App SDK の概要](/intune/app-sdk)」を参照してください。
>
> [Intune SDK](/intune/app-sdk-get-started) は MSAL ライブラリとは別のものであり、独自に Azure AD と対話します。

### <a name="protected-web-api"></a>保護された Web API

Microsoft ID プラットフォーム エンドポイントを使用すると、アプリの RESTful Web API などの Web サービスをセキュリティで保護できます。 保護された Web API は、アクセス トークンを使用して呼び出されます。 トークンは、API のデータの保護と受信要求の認証に役立てられます。 Web API の呼び出し元によって、HTTP 要求の Authorization ヘッダーにアクセス トークンが付加されます。

ASP.NET または ASP.NET Core Web API を保護する場合は、アクセス トークンを検証します。 この検証には、ASP.NET JWT ミドルウェアを使用します。 検証は MSAL.NET ではなく、[.NET ライブラリ用の IdentityModel 拡張機能](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki)によって行われます。

詳細については、[保護された Web API](scenario-protected-web-api-overview.md)に関するページを参照してください。

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>ユーザーに代わって別の Web API を呼び出す Web API

保護された Web API から、ユーザーに代わって別の Web API を呼び出すには、アプリでダウンストリームの Web API のトークンを取得する必要があります。 このような呼び出しは、"*サービス間*" 呼び出しと呼ばれることがあります。 他の Web API を呼び出す Web API では、カスタム キャッシュのシリアル化を提供する必要があります。

![別の Web API を呼び出す Web API](media/scenarios/web-api.svg)

詳細については、[Web API を呼び出す Web API](scenario-web-api-call-api-overview.md) に関するページを参照してください。

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>独自に Web API を呼び出すデーモン アプリ

長時間実行されるプロセスを含んだアプリや、ユーザーの介入なしで動作するアプリも、セキュリティで保護された Web API になんらかの形でアクセスする必要があります。 そのようなアプリでは、認証やトークンの取得にアプリの ID を使用します。 アプリの ID 証明には、クライアント シークレットまたは証明書が使用されます。

呼び出し元のアプリに代わってトークンを取得するデーモン アプリは、MSAL の[クライアント資格情報](scenario-daemon-acquire-token.md#acquiretokenforclient-api)取得メソッドを使用して作成できます。 これらのメソッドには、Azure AD にアプリの登録を追加するクライアント シークレットが必要です。 そのうえで、そのアプリと呼び出されたデーモンとの間でシークレットが共有されます。 シークレットには、アプリケーションのパスワード、証明書アサーション、クライアント アサーションなどがあります。

![他のアプリと API によって呼び出されるデーモン アプリ](media/scenarios/daemon-app.svg)

詳細については、[Web API を呼び出すデーモン アプリケーション](scenario-daemon-overview.md)に関するページを参照してください。

## <a name="scenarios-and-supported-authentication-flows"></a>シナリオとサポートされている認証フロー

トークンを要求するアプリケーションのシナリオを実装するには、認証フローを使用します。 アプリケーション シナリオと認証フローの間に 1 対 1 の対応関係はありません。

トークンの取得が必要なシナリオは、OAuth 2.0 認証フローにも対応します。 詳細については、「[Microsoft ID プラットフォームにおける OAuth 2.0 プロトコルと OpenID Connect プロトコル](active-directory-v2-protocols.md)」を参照してください。

<table>
 <thead>
  <tr><th>シナリオ</th> <th>詳細なシナリオのチュートリアル</th> <th>OAuth 2.0 のフローと許可</th> <th>対象ユーザー</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">シングルページ アプリ</a></td>
   <td>PKCE を使用した<a href="v2-oauth2-auth-code-flow.md">認可コード</a></td>
   <td>職場または学校アカウント、個人用アカウント、Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">シングルページ アプリ</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">暗黙的</a></td>
   <td>職場または学校アカウント、個人用アカウント、Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">ユーザーをサインインさせる Web アプリ</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">承認コード</a></td>
   <td>職場または学校アカウント、個人用アカウント、Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that calls web APIs" src="media/scenarios/web-app.svg"></a></td>
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
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">デバイス コード</a></td>
   <td>職場または学校アカウント、個人用アカウント、Azure AD B2C</td>
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
   <td><a href="v2-oauth2-on-behalf-of-flow.md">On-Behalf-Of</a></td>
   <td>職場または学校アカウントと個人用アカウント</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>シナリオとサポートされているプラットフォームと言語

Microsoft の認証ライブラリは、さまざまなプラットフォームをサポートしています。

- .NET Core
- .NET Framework
- Java
- JavaScript
- macOS
- ネイティブ Android
- ネイティブ iOS
- Node.js
- Python
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android

また、さまざまな言語を使用してアプリケーションをビルドすることもできます。

> [!NOTE]
> アプリケーションの種類によっては、プラットフォームで利用できないことがあります。

次の表の Windows の列で .NET Core と書いてある場合には、.NET Framework でも対応可能です。 後者は表のスペースの関係で省略しています。

|シナリオ  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [シングルページ アプリ](scenario-spa-overview.md) <br/>[![シングル ページ アプリ認証](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [シングルページ アプリ](scenario-spa-overview.md) <br/>[![シングルページ アプリ暗黙](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md) <br/>[![ユーザーをサインインさせる Web アプリ](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>
| [Web API を呼び出す Web アプリ](scenario-web-app-call-api-overview.md) <br/> <br/>[![Web API を呼び出す Web アプリ](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>
| [Web API を呼び出すデスクトップ アプリ](scenario-desktop-overview.md) <br/> <br/>[![Web API を呼び出すデスクトップ アプリ](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![デバイス コード フロー](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/> ![iOS / Objective C または Swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc |
| [Web API を呼び出すモバイル アプリ](scenario-mobile-overview.md) <br/> [![Web API を呼び出すモバイル アプリ](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Objective C または Swift](media/sample-v2-code/small_logo_iOS.png) MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL.Android
| [デーモン アプリ](scenario-daemon-overview.md) <br/> [![デーモン アプリ](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>
| [Web API を呼び出す Web API](scenario-web-api-call-api-overview.md) <br/><br/> [![Web API を呼び出す Web API](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL Node](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL Node<br/>

詳細については、「[Microsoft ID プラットフォームの認証ライブラリ](reference-v2-libraries.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [認証の基本](./authentication-vs-authorization.md)と [Microsoft ID プラットフォームのアクセス トークン](access-tokens.md)の詳細を学習します。
* [IoT アプリへのアクセスのセキュリティ保護](/azure/architecture/example-scenario/iot-aad/iot-aad)に関する詳細を学習します。
