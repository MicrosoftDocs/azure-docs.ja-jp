---
title: Microsoft ID プラットフォーム開発者向け用語集 | Azure
description: 一般的に使用される Microsoft ID プラットフォーム開発者の概念と機能に関する用語のリスト。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/28/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ffc9c0ed5787803fff01d929567bda23b698135
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843208"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft ID プラットフォーム開発者向け用語集

この記事には、主要な開発者のいくつかの概念と用語の定義が含まれています。これらは Microsoft ID プラットフォームを使用するアプリケーション開発について学習する場合に役立ちます。

## <a name="access-token"></a>アクセス トークン

[承認サーバー](#authorization-server)によって発行される[セキュリティ トークン](#security-token)の一種。[クライアント アプリケーション](#client-application)が、[保護されたリソース サーバー](#resource-server)にアクセスする目的で使用します。 要求されたレベルのアクセスに関して、[リソース所有者](#resource-owner)がクライアントに付与しているアクセス権限を通常 [JSON Web トークン (JWT)][JWT] の形式で 1 つにまとめたものがこのトークンです。 このトークンは、認証対象に関して当てはまる[要求](#claim)をすべて含んでおり、クライアント アプリケーションが特定のリソースにアクセスする際に一種の資格情報として使用することができます。 また、これを使用すると、リソース所有者がクライアントに資格情報を開示する必要がなくなります。

表現の対象となる資格情報によっては、アクセス トークンを "User+App" や "App-Only" と呼ぶこともあります。 たとえばクライアント アプリケーションが使用する承認付与には、次のようなタイプがあります。

* ["承認コード" 型の承認付与](#authorization-grant): エンド ユーザーはまず、リソース所有者として認証を行い、リソースにアクセスするための承認をクライアントに委任します。 その後クライアントは、アクセス トークンを取得した時点で認証を行います。 このトークンは、クライアント アプリケーションを承認したユーザーとアプリケーションの両方を表すことから、より具体的に "User+App" トークンと呼ばれることがあります。
* ["クライアント資格情報" 型の承認付与](#authorization-grant): クライアントが行うのは単一の認証のみです。クライアントがリソース所有者の認証/承認なしで機能することから、このトークンは、"App-Only" トークンと呼ばれることがあります。

詳細については、[Microsoft ID プラットフォーム トークンのリファレンス][AAD-Tokens-Claims]に関するページを参照してください。

## <a name="application-id-client-id"></a>アプリケーション ID (クライアント ID)

特定のアプリケーションと関連付けられた構成を識別する、Azure AD がアプリケーションの登録に発行する一意識別子。 このアプリケーション ID ([クライアント ID](https://tools.ietf.org/html/rfc6749#page-15)) は、認証要求の実行時に使用され、開発時に認証ライブラリに提供されます。 アプリケーション ID (クライアント ID) はシークレットではありません。

## <a name="application-manifest"></a>アプリケーション マニフェスト

[Azure portal][AZURE-portal] に備わっている機能の 1 つで、アプリケーションの ID 構成が JSON 形式で生成されて表現されます。そのマニフェストが関連付けられている [Application][AAD-Graph-App-Entity] エンティティと [ServicePrincipal][AAD-Graph-Sp-Entity] エンティティを更新するための機構として使用されます。 詳細については、[Azure Active Directory のアプリケーション マニフェストについて][AAD-App-Manifest]のページを参照してください。

## <a name="application-object"></a>アプリケーション オブジェクト

[Azure portal][AZURE-portal] でアプリケーションを登録または更新すると、そのテナントを対象に、アプリケーション オブジェクトおよび対応する[サービス プリンシパル オブジェクト](#service-principal-object)の両方が作成または更新されます。 アプリケーション オブジェクトは、アプリケーションの ID 構成をグローバルに (そのアプリケーションがアクセスできるすべてのテナントに対して) "*定義*" します。このオブジェクトをテンプレートとして、対応するサービス プリンシパル オブジェクトが "*生成*" され、実行時にローカル (特定のテナント) で使用されます。

詳細については、[アプリケーション オブジェクトとサービス プリンシパル オブジェクト][AAD-App-SP-Objects]に関するページを参照してください。

## <a name="application-registration"></a>アプリケーションの登録

アプリケーションの "ID とアクセス管理" の機能を Azure AD で行うためには、そのアプリケーションを Azure AD [テナント](#tenant)に登録する必要があります。 アプリケーションを Azure AD に登録するとき、アプリケーションに使用する ID 構成を指定します。これによって Azure AD との連携が可能となり、次のような機能が使用できるようになります。

* Azure AD Identity Management と [OpenID Connect][OpenIDConnect] プロトコル実装によるシングル サインオンの強固な管理
* [クライアント アプリケーション](#client-application)による[保護されたリソース](#resource-server)への OAuth 2.0 [承認サーバー](#authorization-server)を介したブローカー アクセス
* [同意フレームワーク](#consent)

詳細については、[Azure Active Directory とアプリケーションの統合][AAD-Integrating-Apps]に関するページを参照してください。

## <a name="authentication"></a>認証

特定の当事者に対し、本物の資格情報の提示を要求する行為。ID 管理とアクセス制御に必要なセキュリティ プリンシパルの拠り所となります。 たとえば [OAuth2 承認付与](#authorization-grant)時には、使用する付与形態に応じて、[リソース所有者](#resource-owner)または[クライアント アプリケーション](#client-application)の役割を果たす当事者が、本物であることを証明する側になります。

## <a name="authorization"></a>authorization

認証済みのセキュリティ プリンシパルに対し、何かを実行する権限を付与する行為。 Azure AD プログラミング モデルでは、主に次の 2 つの使用ケースが存在します。

* [OAuth2 承認付与](#authorization-grant)フロー時: [リソース所有者](#resource-owner)が[クライアント アプリケーション](#client-application)に承認を付与すると、その所有者のリソースにクライアントがアクセスできます。
* クライアントによるリソース アクセス時: [リソース サーバー](#resource-server)側で実装されます。[アクセス トークン](#access-token)内に存在する[要求](#claim)値に基づいてアクセス制御の判断を行います。

## <a name="authorization-code"></a>承認コード

"承認コード" 型 (4 種類ある OAuth2 [承認付与](#authorization-grant)の 1 つ) のフローの過程で[承認エンドポイント](#authorization-endpoint)から[クライアント アプリケーション](#client-application)に提供される有効期間の短い "トークン"。 [リソース所有者](#resource-owner)の認証に対する応答としてこのコードがクライアント アプリケーションに返されることで、要求されたリソースにアクセスするための承認がリソース所有者から委任されていることが示されます。 このフローの中で、このコードは後で [アクセス トークン](#access-token)と引き換えられます。

## <a name="authorization-endpoint"></a>承認エンドポイント

[承認サーバー](#authorization-server)によって実装されるエンドポイントの 1 つ。OAuth2 承認付与フローの過程で[承認付与](#authorization-grant)を提供するための、[リソース所有者](#resource-owner)との対話に使用されます。 実際に付与される内容は、使用されている承認付与フローによって異なる場合があります ([承認コード](#authorization-code)、[セキュリティ トークン](#security-token)など)。

詳細については、OAuth2 仕様の[承認付与タイプ][OAuth2-AuthZ-Grant-Types]と[承認エンドポイント][OAuth2-AuthZ-Endpoint]に関するセクションおよび [OpenIDConnect 仕様][OpenIDConnect-AuthZ-Endpoint]を参照してください。

## <a name="authorization-grant"></a>承認付与

[リソース所有者](#resource-owner)の保護されたリソースにアクセスしてよいという[承認](#authorization)を表す資格情報。[クライアント アプリケーション](#client-application)に対して付与されます。 クライアント アプリケーションは、その種類や要件に応じて、[OAuth2 Authorization Framework によって規定された 4 つの付与タイプ][OAuth2-AuthZ-Grant-Types] ("承認コード付与"、"クライアント資格情報付与"、"暗黙的付与"、"リソース所有者パスワード資格情報付与") のいずれかを使ってアクセス許可を得ることができます。 クライアントに返される資格情報は、使用された承認付与のタイプに応じて、[アクセス トークン](#access-token)と[承認コード](#authorization-code) (その後アクセス トークンに交換される) のいずれかになります。

## <a name="authorization-server"></a>承認サーバー

[OAuth2 Authorization Framework][OAuth2-Role-Def] の定義によれば、[リソース所有者](#resource-owner)を認証し、その承認を得た後にアクセス トークンを[クライアント](#client-application)に発行するサーバーをいいます。 [クライアント アプリケーション](#client-application)は実行時に、その[承認エンドポイント](#authorization-endpoint)および[トークン エンドポイント](#token-endpoint)を介し、OAuth2 によって定義された[承認付与](#authorization-grant)に従って承認サーバーと対話します。

Microsoft ID プラットフォーム アプリケーション統合の場合、Azure AD アプリケーションと Microsoft サービス API ([Microsoft Graph API][Microsoft-Graph] など) に使用する承認サーバー ロールは、Microsoft ID プラットフォームで実装されます。

## <a name="claim"></a>要求

[セキュリティ トークン](#security-token)には要求が格納されます。この要求を通じて、一方のエンティティ ([クライアント アプリケーション](#client-application)、[リソース所有者](#resource-owner)など) に関するアサーションが、もう一方のエンティティ ([リソース サーバー](#resource-server)など) に渡されます。 要求は、トークンのサブジェクト ([承認サーバー](#authorization-server)によって認証されたセキュリティ プリンシパルなど) に関する事実を伝達する名前と値のペアです。 特定のトークンとして提示される要求は、いくつかの不確定要素 (トークンの種類、サブジェクトの認証に使用された資格情報の種類、アプリケーション構成など) に依存します。

詳細については、[Microsoft ID プラットフォーム トークンのリファレンス][AAD-Tokens-Claims]に関するページを参照してください。

## <a name="client-application"></a>クライアント アプリケーション

[OAuth2 Authorization Framework][OAuth2-Role-Def] の定義によれば、[リソース所有者](#resource-owner)に代わって、保護されたリソースを要求するアプリケーションをいいます。 "クライアント" という言葉の意味には、特定のハードウェア実装上の特性 (アプリケーションがサーバーで実行されるのか、デスクトップで実行されるのか、またはそれ以外のデバイスで実行されるのか、など) は含まれません。

クライアント アプリケーションは、リソース所有者に[承認](#authorization)を要求することによって、[OAuth2 承認付与](#authorization-grant)フローに参加し、リソース所有者に代わって API やデータにアクセスすることができます。 OAuth2 Authorization Framework では、資格情報の機密維持に対するクライアントの能力に基づき、"confidential" と "public" という [2 種類のクライアントを定義][OAuth2-Client-Types]しています。 アプリケーションは、Web サーバー上で実行される [Web クライアント (confidential)](#web-client)、デバイス上にインストールされる[ネイティブ クライアント (public)](#native-client)、またはデバイスのブラウザーで実行される[ユーザーエージェントベース クライアント (public)](#user-agent-based-client) を実装できます。

## <a name="consent"></a>同意

[リソース所有者](#resource-owner)から[クライアント アプリケーション](#client-application)に承認 (リソース所有者に代わって特定の[権限](#permissions)で保護されたリソースにアクセスするための) を付与するプロセス。 クライアントから要求された権限によっては、組織データまたは個人データへのアクセスを許可することへの同意が管理者 (組織データの場合) またはユーザー (個人データの場合) に求められます。 さらに、[マルチテナント](#multi-tenant-application)のシナリオでは、同意するユーザーのテナントにアプリケーションの[サービス プリンシパル](#service-principal-object)が記録されます。

詳細については、「[同意フレームワーク](consent-framework.md)」を参照してください。

## <a name="id-token"></a>ID トークン

[承認サーバー](#authorization-server)の[承認エンドポイント](#authorization-endpoint)から提供される [OpenID Connect][OpenIDConnect-ID-Token] [セキュリティ トークン](#security-token)。このトークンには、エンド ユーザーの[リソース所有者](#resource-owner)の認証に関連した[要求](#claim)が格納されます。 ID トークンもアクセス トークンと同様、デジタル署名された [JSON Web トークン (JWT)][JWT] として表現されます。 ただし、アクセス トークンとは異なり、ID トークンの要求は、リソース アクセス (特にアクセス制御) に関連した目的には使用されません。

詳細については、[Microsoft ID プラットフォーム トークンのリファレンス][AAD-Tokens-Claims]に関するページを参照してください。

## <a name="microsoft-identity-platform"></a>Microsoft ID プラットフォーム

Microsoft ID プラットフォームは、Azure Active Directory (Azure AD) の ID サービスおよび開発者プラットフォームの進化版です。 これにより、開発者はすべての Microsoft ID にサインインし、Microsoft Graph、その他の Microsoft API、または開発者が作成した API を呼び出すトークンを取得することができます。 これは多彩な機能を備えたプラットフォームであり、認証サービス、ライブラリ、アプリケーションの登録と構成、完全な開発者向けドキュメント、サンプル コード、およびその他の開発者向けコンテンツによって構成されています。 Microsoft ID プラットフォームでは、OAuth 2.0 や OpenID Connect など業界標準のプロトコルがサポートされています。 詳細については、「[Microsoft ID プラットフォームについて](about-microsoft-identity-platform.md)」を参照してください。

## <a name="multi-tenant-application"></a>マルチテナント アプリケーション

クライアントが登録されたテナントに限らず、任意の Azure AD [テナント](#tenant)にプロビジョニングされたユーザーによるサインインと[同意](#consent)を有効にするアプリケーションのクラス。 [ネイティブ クライアント](#native-client) アプリケーションは、既定ではマルチテナントとなります。これに対し、[Web クライアント](#web-client)と [Web リソース/API](#resource-server) アプリケーションは、シングル テナントまたはマルチテナントを選択できるようになっています。 一方シングル テナントとして登録される Web アプリケーションの場合は、アプリケーションの登録先と同じテナントにプロビジョニングされたユーザー アカウントからのみサインインが許可されます。

詳細については、[マルチテナント アプリケーション パターンを使用して Azure AD ユーザーをサインインさせる方法][AAD-Multi-Tenant-Overview]に関するページを参照してください。

## <a name="native-client"></a>ネイティブ クライアント

デバイス上にネイティブでインストールされるタイプの [クライアント アプリケーション](#client-application) 。 すべてのコードがデバイス上で実行され、機密性を保った状態でプライベートに資格情報を保存することができないため、"public" クライアントと見なされます。 詳細については、[OAuth2 のクライアント タイプとプロファイル][OAuth2-Client-Types]に関するページを参照してください。

## <a name="permissions"></a>permissions

[クライアント アプリケーション](#client-application)は、アクセス許可要求を宣言することで[リソース サーバー](#resource-server)へのアクセス権を取得します。 次の 2 種類があります。

* "委任" されたアクセス許可。サインインした[リソース所有者](#resource-owner)から委任された承認を使用して、[スコープに基づく](#scopes)アクセス権を指定します。実行時には、クライアントの[アクセス トークン](#access-token)の ["scp" 要求](#claim)としてリソースに提示されます。
* "アプリケーション" のアクセス許可。クライアント アプリケーションの資格情報/ID を使用して[ロールベース](#roles)のアクセス権を指定します。実行時には、クライアントのアクセス トークンの ["roles" 要求](#claim)としてリソースに提示されます。

これらの要求は [同意](#consent) プロセス時にも出現し、管理者またはリソース所有者には、そのテナント内のリソースに対するクライアント アクセスを許可/拒否する機会が与えられます。

アクセス許可要求の構成は、[Azure portal][AZURE-portal] のアプリケーション用の **[API アクセス許可]** ページで、目的の "委任されたアクセス許可" と "アプリケーションのアクセス許可" (後者には全体管理者ロールのメンバーシップが必要) を選択することによって行います。 [public クライアント](#client-application)は、資格情報を安全に維持できないため、要求できるのは委任されたアクセス許可のみです。一方 [confidential クライアント](#client-application)は、委任されたアクセス許可とアプリケーションのアクセス許可のどちらでも要求することができます。 クライアントの[アプリケーション オブジェクト](#application-object)は、宣言された権限をその [requiredResourceAccess プロパティ][AAD-Graph-App-Entity]に格納します。

## <a name="resource-owner"></a>リソース所有者

[OAuth2 Authorization Framework][OAuth2-Role-Def] の定義によれば、保護されたリソースへのアクセス権を付与することのできるエンティティをいいます。 リソース所有者が人である場合は、"エンド ユーザー" と呼ばれます。 たとえば[クライアント アプリケーション](#client-application)は、[Microsoft Graph API][Microsoft-Graph] を介してユーザーのメールボックスにアクセスする必要があるとき、そのメールボックスのリソース所有者に権限を要求する必要があります。

## <a name="resource-server"></a>リソース サーバー

[OAuth2 Authorization Framework][OAuth2-Role-Def] の定義によれば、保護されたリソースのホストとして、[アクセス トークン](#access-token)を提示する[クライアント アプリケーション](#client-application)からのリソース要求 (保護されたリソースに対する要求) を受理し、応答する機能を備えたサーバーをいいます。 保護されたリソース サーバーまたはリソース アプリケーションと呼ばれることもあります。

リソース サーバーは API を公開しており、そこで保護されているリソースに対しては、OAuth 2.0 Authorization Framework を使用して、[スコープ](#scopes)と[ロール](#roles)を介したアクセスが強制的に適用されます。 たとえば、Azure AD テナント データへのアクセスを提供する Azure AD Graph API や、メール、カレンダーなどのデータへのアクセスを提供する Office 365 API があります。 これらの API はどちらも [Microsoft Graph API][Microsoft-Graph] から利用することができます。

リソース アプリケーションの ID 構成は、クライアント アプリケーションと同様、Azure AD テナントへの [登録](#application-registration) を通じて確立され、アプリケーション オブジェクトとサービス プリンシパル オブジェクトの両方が得られます。 Azure AD Graph API など、Microsoft が提供している一部の API には、あらかじめ登録されているサービス プリンシパルが存在し、プロビジョニング時にすべてのテナントで利用できるようになっています。

## <a name="roles"></a>roles

ロールは、[スコープ](#scopes)と同様、[リソース サーバー](#resource-server)の保護されたリソースへのアクセスを管理するための手段です。 ロールには、"ユーザー" ロールと "アプリケーション" ロールの 2 種類があります。ユーザー ロールでは、リソースへのアクセスを必要とするユーザー/グループに関してロールベースのアクセス制御を実装します。これに対してアプリケーション ロールで実装するのは、アクセスを要求する[クライアント アプリケーション](#client-application)の場合と同じです。

ロールは、リソースによって定義される文字列 ("経費承認者"、"読み取り専用"、"Directory.ReadWrite.All" など) です。[Azure portal][AZURE-portal] からリソースの[アプリケーション マニフェスト](#application-manifest)を介して管理され、リソースの [appRoles プロパティ][AAD-Graph-Sp-Entity]に格納されます。 Azure Portal は、ユーザーを "ユーザー" ロールに割り当てたり、"アプリケーション" ロールに対するクライアント [アプリケーションのアクセス許可](#permissions)を構成したりするためにも使用します。

Azure AD の Graph API によって公開されているアプリケーション ロールの詳しい説明については、[Graph API のアクセス許可スコープ][AAD-Graph-Perm-Scopes]に関するページを参照してください。 実装手順の例については、[RBAC と Azure portal を使用してアクセスを管理する方法][AAD-RBAC]に関するページを参照してください。

## <a name="scopes"></a>スコープ

スコープは、[ロール](#roles)と同様、[リソース サーバー](#resource-server)の保護されたリソースへのアクセスを管理するための手段です。 リソースへの委任されたアクセス権を所有者から付与されている[クライアント アプリケーション](#client-application)に対し、[スコープベース][OAuth2-Access-Token-Scopes]のアクセス制御を実装する目的で使用されます。

スコープは、リソースによって定義される文字列 ("Mail.Read"、"Directory.ReadWrite.All" など) です。[Azure portal][AZURE-portal] からリソースの[アプリケーション マニフェスト](#application-manifest)を介して管理され、リソースの [oauth2Permissions プロパティ][AAD-Graph-Sp-Entity]に格納されます。 Azure Portal は、クライアント アプリケーションの、スコープに対する[委任されたアクセス許可](#permissions)を構成するためにも使用します。

推奨される名前付け規則は、"resource.operation.constraint" 形式です。 Azure AD の Graph API によって公開されているスコープの詳しい説明については、[Graph API のアクセス許可スコープ][AAD-Graph-Perm-Scopes]に関するページを参照してください。 Office 365 サービスによって公開されているスコープについては、[Office 365 API アクセス許可に関するリファレンス][O365-Perm-Ref]を参照してください。

## <a name="security-token"></a>セキュリティ トークン

要求を含んだ署名付きのドキュメント (OAuth2 トークン、SAML 2.0 アサーションなど)。 OAuth2 [承認付与](#authorization-grant)の場合、[アクセス トークン](#access-token) (OAuth2) と [ID トークン](https://openid.net/specs/openid-connect-core-1_0.html#IDToken)がセキュリティ トークンの種類になります。どちらも [JSON Web トークン (JWT)][JWT] として実装されます。

## <a name="service-principal-object"></a>サービス プリンシパル オブジェクト

[Azure portal][AZURE-portal] でアプリケーションを登録または更新すると、そのテナントを対象に、[アプリケーション オブジェクト](#application-object)および対応するサービス プリンシパル オブジェクトの両方が作成または更新されます。 アプリケーション オブジェクトは、アプリケーションの ID 構成をグローバルに (関連するアプリケーションがアクセスできるすべてのテナントに対して) "*定義*" します。このオブジェクトをテンプレートとして、対応するサービス プリンシパル オブジェクトが "*生成*" され、実行時にローカル (特定のテナント) で使用されます。

詳細については、[アプリケーション オブジェクトとサービス プリンシパル オブジェクト][AAD-App-SP-Objects]に関するページを参照してください。

## <a name="sign-in"></a>サインイン

[クライアント アプリケーション](#client-application)がエンド ユーザーの認証を開始し、関連する状態を収集するプロセス。[セキュリティ トークン](#security-token)を取得すると共に、アプリケーションのセッションをその状態に限定することを目的としています。 状態には、ユーザー プロファイル情報などのアーティファクトや、トークンの要求から生成される情報が含まれている場合があります。

アプリケーションのサインイン機能は通常、シングル サインオン (SSO) を実装するために使用されます。 また、この機能は、エンド ユーザーが (初回サインイン時に) アプリケーションにアクセスするためのエントリ ポイントとして "サインアップ" 機能の前に実行されることがあります。 サインアップ機能は、ユーザーごとの特別な状態を収集して永続化するために使用され、 [ユーザーの同意](#consent)を必要とする場合があります。

## <a name="sign-out"></a>サインアウト

エンド ユーザーの認証を取り消し、[サインイン](#sign-in)の過程で[クライアント アプリケーション](#client-application)のセッションに関連付けられたユーザーの状態を解除するプロセス。

## <a name="tenant"></a>tenant

Azure AD ディレクトリのインスタンスを "Azure AD テナント" といいます。 次のような機能が用意されています。

* 統合アプリケーションのレジストリ サービス
* ユーザー アカウントや登録済みアプリケーションの認証
* OAuth2、SAML などの各種プロトコルをサポートするうえで必要な REST エンドポイント ([承認エンドポイント](#authorization-endpoint)、[トークン エンドポイント](#token-endpoint)のほか、[マルチテナント アプリケーション](#multi-tenant-application)によって使用される "共通" エンドポイントなど)

Azure AD テナントはサインアップ時に作成され、Azure サブスクリプションおよび Office 365 サブスクリプションに関連付けられます。これにより、そのサブスクリプションの ID およびアクセス管理機能が提供されます。 Azure サブスクリプション管理者は、Azure Portal を使用して追加の Azure AD テナントを作成することもできます。 テナントを利用するための各種方法について詳しくは、[Azure Active Directory テナントを取得する方法][AAD-How-To-Tenant]に関するページを参照してください。 サブスクリプションと Azure AD テナントの関係について詳しくは、[Azure サブスクリプションを Azure Active Directory に関連付ける方法][AAD-How-Subscriptions-Assoc]に関するページを参照してください。

## <a name="token-endpoint"></a>トークン エンドポイント

[承認サーバー](#authorization-server)によって実装されるエンドポイントの 1 つ。OAuth2 [承認付与](#authorization-grant)をサポートするために使用されます。 付与された承認によっては、[クライアント](#client-application)への[アクセス トークン](#access-token) (と関連する "更新" トークン) を取得したり、[OpenID Connect][OpenIDConnect] プロトコルと共に使用する場合に [ID トークン](#id-token)を取得したりできます。

## <a name="user-agent-based-client"></a>ユーザー エージェント ベースのクライアント

Web サーバーからコードをダウンロードしてユーザー エージェント (Web ブラウザーなど) 内で実行する[クライアント アプリケーション](#client-application)の一種。その例としてシングル ページ アプリケーション (SPA) が挙げられます。 すべてのコードがデバイス上で実行され、機密性を保った状態でプライベートに資格情報を保存することができないため、"public" クライアントと見なされます。 詳細については、[OAuth2 のクライアント タイプとプロファイル][OAuth2-Client-Types]に関するページを参照してください。

## <a name="user-principal"></a>ユーザー プリンシパル

サービス プリンシパル オブジェクトはアプリケーション インスタンスを表現するためのセキュリティ プリンシパルです。一方、ユーザー プリンシパル オブジェクトも、セキュリティ プリンシパルのひとつですが、表現の対象となるのはユーザーです。 ユーザー オブジェクトのスキーマは、Azure AD Graph の[ユーザー エンティティ][AAD-Graph-User-Entity]によって定義されます (姓や名をはじめとするユーザー関連のプロパティ、ユーザー プリンシパル名、ディレクトリ ロール メンバーシップなど)。これにより、実行時にユーザー プリンシパルを設定するための Azure AD のユーザー ID 構成が提供されます。 ユーザー プリンシパルは、シングル サインオン、[同意](#consent)の委任の記録、アクセス制御の意思決定などの際に、認証済みのユーザーを表す目的で使用されます。

## <a name="web-client"></a>Web クライアント

Web サーバーですべてのコードを実行する[クライアント アプリケーション](#client-application)の一種。資格情報をサーバー上に安全に保存することで、"confidential" クライアントとして動作することができます。 詳細については、[OAuth2 のクライアント タイプとプロファイル][OAuth2-Client-Types]に関するページを参照してください。

## <a name="next-steps"></a>次の手順

[Microsoft ID プラットフォーム開発者のガイド][AAD-Dev-Guide]は、[アプリケーション統合][AAD-How-To-Integrate]の概要や [Microsoft ID プラットフォーム認証の基礎とサポートされる認証シナリオ][AAD-Auth-Scenarios]など、Microsoft ID プラットフォーム開発に関連したあらゆるトピックに使用するためのランディング ページとなっています。 また、迅速に開始および実行する方法に関するコード サンプルやチュートリアルは、[GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=) で見つかります。

Microsoft のコンテンツ改善のため、以下のコメント セクションよりご意見をお寄せください。新しい定義に関するリクエストのほか、既存の定義の更新のリクエストもお待ちしております。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: /graph/permissions-reference
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
