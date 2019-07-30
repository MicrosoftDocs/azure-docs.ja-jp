---
title: クライアント アプリケーションの構成 (Microsoft Authentication Library) | Azure
description: Microsoft Authentication Library (MSAL) でのパブリック クライアント アプリケーションと機密クライアント アプリケーションの構成オプションについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 767f7362a6c46d864ba17f23f6506bf6cdb71414
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304731"
---
# <a name="application-configuration-options"></a>アプリケーション構成オプション

コードで、認証してトークンを取得するために、新しいパブリックまたは機密クライアント アプリケーション (または MSAL.js のユーザー エージェント) を初期化します。 Microsoft Authentication Library (MSAL) でクライアント アプリを初期化するときに、さまざまな構成オプションを設定できます。 これらのオプションは、次の 2 つのグループに分類されます。

- 登録オプション。以下が含まれます。
    - [機関](#authority) (アプリ用の ID プロバイダー [インスタンス](#cloud-instance)とサインイン[対象ユーザー](#application-audience)で構成されます。場合により、テナント ID も含まれます)。
    - [クライアント ID](#client-id)。
    - [リダイレクト URI](#redirect-uri)。
    - [クライアント シークレット](#client-secret) (機密性の高いクライアント アプリケーション用)。
- [ログ オプション](#logging) (ログ レベル、個人データの制御、およびライブラリを使用するコンポーネントの名前など)。

## <a name="authority"></a>Authority
機関は、MSAL がトークンを要求できるディレクトリを示す URL です。 一般的な機関を次に示します。

- https\://login.microsoftonline.com/\<テナント\>/。&lt;テナント&gt;は、Azure Active Directory (Azure AD) テナントのテナント ID か、この Azure AD テナントに関連付けられたドメインです。 特定の組織のユーザーをサインインする場合にのみ使用されます。
- https\://login.microsoftonline.com/common/。 職場および学校アカウント、または個人用 Microsoft アカウントを持つユーザーのサインインに使用されます。
- https\://login.microsoftonline.com/organizations/。 職場および学校アカウントを持つユーザーをサインインする場合に使用されます。
- https\://login.microsoftonline.com/consumers/。 個人用 Microsoft アカウント (以前の Windows Live ID アカウント) のみを持つユーザーのサインインに使用されます。

機関の設定は、アプリケーション登録ポータルで宣言されているものと一致している必要があります。

機関 URL は、インスタンスと対象ユーザーで構成されます。

以下の機関が可能です。
- Azure AD クラウド機関。
- Azure AD B2C 機関。 「[B2C specifics (B2C の詳細)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)」を参照してください。
- Active Directory フェデレーション サービス (AD FS) 機関。 [AD FS のサポート](https://aka.ms/msal-net-adfs-support)に関するページを参照してください。

Azure AD クラウド機関には、2 つの部分があります。
- ID プロバイダー "*インスタンス*"
- アプリのサインイン "*対象ユーザー*"

インスタンスと対象ユーザーを連結して、機関 URL として指定できます。 MSAL 3.*x* より前のバージョンの MSAL.NET では、ターゲットにしたいクラウドとサインイン対象ユーザーに応じて、機関を自分で構成する必要がありました。  次の図は、機関 URL がどのように構成されるかを示しています。

![機関 URL の構成方法](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>クラウド インスタンス
"*インスタンス*" を使用して、アプリでサインインするユーザーが Azure パブリック クラウドのユーザーであるか各国のクラウドのユーザーであるかを指定します。 コードで MSAL を使用すると、列挙型を使用するか、[各国のクラウド インスタンス](authentication-national-cloud.md#azure-ad-authentication-endpoints)に `Instance` メンバーとして URL を渡すことで (URL がわかっている場合)、Azure クラウド インスタンスを設定できます。

`Instance` と `AzureCloudInstance` の両方が指定された場合、MSAL.NET では明示的な例外がスローされます。

インスタンスの指定がない場合、アプリのターゲットは Azure パブリック クラウド インスタンスになります (URL `https://login.onmicrosoftonline.com` インスタンス)。

## <a name="application-audience"></a>アプリケーションの対象ユーザー

サインイン対象ユーザーは、アプリのビジネス ニーズによって異なります。
- 基幹業務 (LOB) 開発者であれば、ご自分の組織内でのみ使用されるシングル テナント アプリケーションを作成することになると思われます。 その場合、テナント ID (Azure AD インスタンスの ID) か、Azure AD インスタンスに関連付けられたドメイン名で組織を指定する必要があります。
- ISV の方であれば、ユーザーに、すべての組織または一部の組織で職場および学校アカウントを使用してサインインしてもらうケースが考えられます (マルチテナント アプリ)。 ただし、ユーザーに個人用 Microsoft アカウントでサインインしてもらうこともあります。

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>コード/構成で対象ユーザーを指定する方法
コードで MSAL を使用する場合は、次のいずれかの値を使用して対象ユーザーを指定します。
- Azure AD 機関の対象ユーザーの列挙
- テナント ID (次のいずれか)
  - シングルテナント アプリケーションの GUID (Azure AD インスタンスの ID)
  - Azure AD インスタンスに関連付けられているドメイン名 (これもシングルテナント アプリケーション用)
- Azure AD 機関の対象ユーザーの列挙に代わるテナント ID として、次のいずれかのプレースホルダー:
    - `organizations`: マルチテナント アプリケーションの場合
    - `consumers`: ユーザーの個人アカウントでのみサインインする場合
    - `common`: 職場および学校アカウント、または個人用 Microsoft アカウントを持つユーザーのサインインに使用

Azure AD 機関の対象ユーザーとテナント ID の両方を指定した場合、MSAL では意味のある例外がスローされます。

対象ユーザーを指定しない場合、アプリの対象ユーザーは Azure AD および個人用 Microsoft アカウントになります (つまり、`common` を指定した場合と同様の動作になります)。

### <a name="effective-audience"></a>有効な対象ユーザー
アプリケーションで有効な対象ユーザーは、アプリに設定した対象ユーザーとアプリの登録で指定された対象ユーザーの最小値 (共通集合がある場合) になります。 実際に、[アプリの登録](https://aka.ms/appregistrations)エクスペリエンスでは、アプリの対象ユーザー (サポートされるアカウントの種類) を指定できます。 詳細については、「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)」を参照してください。

現時点では、個人用 Microsoft アカウントのみを持つユーザーのサインインをアプリで実行する唯一の方法は、次の両方の設定を構成することです。
- アプリの登録の対象ユーザーを `Work and school accounts and personal accounts` に設定する。
- コードまたは構成で、対象ユーザーを `AadAuthorityAudience.PersonalMicrosoftAccount` (または `TenantID` = "consumers") に設定する。

## <a name="client-id"></a>クライアント ID
クライアント ID は、アプリの登録時に Azure AD によってアプリに割り当てられた一意のアプリケーション (クライアント) ID です。

## <a name="redirect-uri"></a>リダイレクト URI
リダイレクト URI は、ID プロバイダーがセキュリティ トークンを送り返す URI です。

### <a name="redirect-uri-for-public-client-apps"></a>パブリック クライアント アプリ用のリダイレクト URI
MSAL を使用してパブリック クライアント アプリを開発している場合:
- デスクトップまたは UWP アプリケーションで `.WithDefaultRedirectUri()` を使用することがあります (MSAL.NET 4.1 以上)。 このメソッドは、パブリック クライアント アプリケーションのリダイレクト URI プロパティを、パブリック クライアント アプリケーションの既定の推奨リダイレクト URI に設定します。 

  プラットフォーム  | リダイレクト URI  
  ---------  | --------------
  デスクトップ アプリ (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` の値。 これは、登録する必要がある WebAuthenticationBroker.GetCurrentApplicationCallbackUri() の結果に値を設定することによって、ブラウザーでの SSO を有効にします
  .NET Core | `https://localhost` 今のところ、埋め込み Web ビュー用の UI が .NET Core には存在しないため、これによって、ユーザーはシステム ブラウザーを使用して対話型認証を実行できるようになります。

- ブローカーをサポートしていない Xamarin Android および iOS アプリケーションを構築している場合は、リダイレクト URI を追加する必要はありません (Xamarin Android および iOS では、リダイレクト URI は自動的に `msal{ClientId}://auth` に設定されます)

- [アプリの登録](https://aka.ms/appregistrations)でリダイレクト URI を構成する必要があります。

   ![アプリの登録でのリダイレクト URI](media/msal-client-application-configuration/redirect-uri.png)

`RedirectUri` プロパティを使用して、このリダイレクト URI をオーバーライドできます (ブローカーを使用する場合など)。 そのシナリオでのリダイレクト URI の例を次に示します。

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

詳細については、[Android と iOS 用のドキュメント](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)を参照してください。

### <a name="redirect-uri-for-confidential-client-apps"></a>機密性の高いクライアント アプリ用のリダイレクト URI
Web アプリの場合、リダイレクト URI (または応答 URI) は、Azure AD がアプリケーションにトークンを戻すために使用する URI です。 機密性の高いアプリが Web アプリと Web API のどちらかである場合は、その URL を使用できます。 リダイレクト URI は、アプリの登録で登録する必要があります。 この登録は、最初にローカルでテストを行ったアプリをデプロイするときに特に重要です。 アプリケーション登録ポータルで、デプロイ対象のアプリの応答 URL を追加する必要があります。

デーモン アプリの場合は、リダイレクト URI を指定する必要はありません。

## <a name="client-secret"></a>クライアント シークレット
このオプションでは、機密性の高いクライアント アプリ用のクライアント シークレットを指定します。 このシークレット (アプリ パスワード) は、アプリケーション登録ポータルによって提供されるか、PowerShell AzureAD、PowerShell AzureRM、Azure CLI のいずれかを使用したアプリの登録時に Azure AD に対して提供されます。

## <a name="logging"></a>ログの記録
その他の構成オプションを使用して、ログ記録とトラブルシューティングを有効にすることができます。 これらの使用方法の詳細については、「[ログの記録](msal-logging.md)」の記事を参照してください。

## <a name="next-steps"></a>次の手順
[MSAL.NET を使用したクライアント アプリケーションのインスタンス化](msal-net-initializing-client-applications.md)について確認します。

[MSAL.js を使用したクライアント アプリケーションのインスタンス化](msal-js-initializing-client-applications.md)について確認します。
