---
title: 開発するクライアント アプリケーションで認証と認可の回復性を向上させる
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームを使用してクライアント アプリケーションで認証と認可の回復性を向上させるためのガイダンス
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: bc3b041e44fad66a4edc6ff34c0e534dc423de86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226592"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>開発するクライアント アプリケーションで認証と認可の回復性を向上させる

このセクションでは、Microsoft ID プラットフォームと Azure Active Directory を使用してユーザーのサインインを行い、それらのユーザーに代わってアクションを実行するクライアント アプリケーションで回復性を強化するためのガイダンスを提供します。

## <a name="use-the-microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL) を使用する

[Microsoft Authentication Library (MSAL)](../develop/msal-overview.md) は、[Microsoft ID プラットフォーム](../develop/index.yml)の重要な部分です。 これにより、トークンの取得、管理、キャッシュ、および更新が簡素化されて管理され、回復性のためのベスト プラクティスが使用されます。 MSAL は、開発者が実装の詳細について心配することなく、セキュリティで保護されたソリューションを実現できるように設計されています。

MSAL では、トークンがキャッシュされ、サイレント トークン取得パターンが使用されます。 また、Windows UWP、iOS、Android など、セキュリティで保護されたストレージがネイティブに提供されるプラットフォームではトークン キャッシュが自動的にシリアル化されます。 開発者は、[Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization)、[MSAL.NET](../develop/msal-net-token-cache-serialization.md)、[MSAL for Java](../develop/msal-java-token-cache-serialization.md)、および [MSAL for Python](../develop/msal-python-token-cache-serialization.md) を使用している場合、シリアル化の動作をカスタマイズできます。

![MSAL を使用して Microsoft ID を呼び出すアプリケーションを含むデバイスの画像](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

MSAL を使用する場合、トークンのキャッシュ、更新、およびサイレント取得が自動的にサポートされます。 単純なパターンを使用して、先進認証に必要なトークンを取得できます。 多くの言語がサポートされます。また、言語とシナリオに応じたサンプルを、[[サンプル]](../develop/sample-v2-code.md) ページで見つけることができます。

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

MSAL では、場合によってトークンが事前に更新されることがあります。 Microsoft ID は、有効期間が長いトークンを発行するときに、トークンを更新するための最適なタイミングに関する情報をクライアントに送信できます ("refresh\_in")。 MSAL は、この情報に基づいてトークンを事前に更新します。 古いトークンが有効な間、アプリは引き続き実行されますが、次の正常なトークン取得までの期間が長くなります。

### <a name="stay-up-to-date"></a>最新情報を入手する

開発者は、最新の MSAL リリースに更新するためのプロセスを用意する必要があります。 認証はアプリのセキュリティの一部であり、アプリは、新しい MSAL リリースに含まれるセキュリティの強化によって、最新の状態を維持する必要があります。 これは一般に、継続的に開発されているライブラリにとって良い習慣であり、これを行うことで、アプリの回復性に関して最新のコードを確実に取得できます。 Microsoft ID では、アプリケーションの回復性を高める方法に関するイノベーションが継続的に行われているため、最新の MSAL が使用されているアプリは、これらのイノベーションに基づいて構築を行う準備が最も整っています。

[最新の MSAL.js のバージョンとリリース ノートを確認する](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[最新の MSAL .NET のバージョンとリリース ノートを確認する](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[最新の MSAL Python のバージョンとリリース ノートを確認する](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[最新の MSAL Java のバージョンとリリース ノートを確認する](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[最新の MSAL iOS と macOS のバージョンおよびリリース ノートを確認する](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[最新の MSAL Android のバージョンとリリース ノートを確認する](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[最新の MSAL Angular のバージョンとリリース ノートを確認する](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[最新の Microsoft.Identity.Web のバージョンとリリース ノートを確認する](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>回復性があるパターンをトークン処理に使用する

MSAL を使用していない場合は、これらの回復性があるパターンをトークン処理に使用できます。 これらのベスト プラクティスは、MSAL ライブラリによって自動的に実装されます。 

一般に、先進認証が使用されているアプリケーションでは、エンドポイントを呼び出して、ユーザーを認証し、保護された API を呼び出すことをアプリケーションに認可するトークンを取得します。 MSAL は、認証の詳細を処理するように意図されており、このプロセスの回復性を向上させるためにいくつかのパターンを実装します。 MSAL 以外のライブラリを使用することを選択した場合は、このセクションのガイダンスを使用してベスト プラクティスを実装してください。 MSAL を使用する場合は、これらのベスト プラクティスは MSAL によって自動的に実装されるため、すべて無料で利用できます。

### <a name="cache-tokens"></a>トークンをキャッシュする

アプリは、Microsoft ID から受け取ったトークンを適切にキャッシュする必要があります。 アプリがトークンを受け取ると、トークンが含まれている HTTP 応答に "expires_in" プロパティも含まれます。これは、トークンをキャッシュし、再利用する期間をアプリケーションに通知します。 アプリケーションで "expires_in" プロパティを使用して、トークンの有効期間が決定されることが重要です。 アプリケーションで API アクセス トークンのデコードが試行されてはなりません。

![アプリケーションによって Microsoft ID への呼び出しが行われても、その呼び出しは、アプリケーションが実行されているデバイス上のトークン キャッシュを通過します](media/resilience-client-app/token-cache.png)

キャッシュされたトークンを使用することで、アプリと Microsoft ID 間の不要なトラフィックを防止し、トークン取得呼び出しの回数を減らすことにより、アプリがトークン取得失敗の影響を受けにくくすることができます。 キャッシュされたトークンを使用することで、アプリでトークン取得をブロックする必要性が減るため、アプリケーションのパフォーマンスも向上します。 ユーザーは、そのトークンの有効期間中、アプリケーションにサインインした状態を維持できます。

### <a name="serialize-and-persist-tokens"></a>トークンをシリアル化および永続化する

アプリは、トークン キャッシュを安全にシリアル化して、アプリのインスタンス間でトークンが永続化されるようにする必要があります。 トークンは、有効期間内である限り再利用できます。 [更新トークン](../develop/v2-oauth2-auth-code-flow.md#refresh-the-access-token)、そして次第に[アクセス トークン](../develop/access-tokens.md)が何時間も発行されています。 この有効期間中に、ユーザーによってアプリケーションが何度も起動される可能性があります。 アプリが起動されると、使用できる有効なアクセスまたは更新トークンがあるかどうかが確認されます。 これにより、Microsoft ID への不要な呼び出しが回避されるため、アプリの回復性とパフォーマンスが向上します。

![アプリケーションによって Microsoft ID への呼び出しが行われても、その呼び出しは、アプリケーションが実行されているデバイス上のトークン キャッシュとトークン ストアを通過します](media/resilience-client-app/token-store.png)

永続的トークン ストレージは、所有ユーザーまたはプロセス ID に対してアクセスが制御され、暗号化されている必要があります。 モバイル、Windows、Mac などのプラットフォームでは、開発者は資格情報を格納するための組み込み機能を利用する必要があります。

### <a name="acquire-tokens-silently"></a>トークンをサイレントに取得する

ユーザーを認証したり、API を呼び出すための認可を取得したりするプロセスでは、Microsoft ID で複数の手順が必要になる場合があります。 たとえば、ユーザーが初めてサインインするときに、資格情報を入力し、テキスト メッセージを使用した多要素認証を実行する必要がある場合があります。 各手順により、そのサービスを提供するリソースへの依存関係が追加されます。 ユーザーにとって最良のエクスペリエンスであり、依存関係が最も少ないものは、ユーザー操作を要求する前に、トークンのサイレント取得を試行して、これらの追加手順を回避することです。

![ユーザーの認証または認可のプロセスを完了するために実行する必要がある可能性がある Microsoft ID 内のさまざまなサービスを示す図](media/resilience-client-app/external-dependencies.png)

トークンのサイレント取得は、アプリのトークン キャッシュにある有効なトークンの使用から開始されます。 有効なトークンがない場合、アプリは更新トークン (使用可能な場合) とトークン エンドポイントを使用してトークンの取得を試行します。 これらのオプションのいずれも使用できない場合、アプリは "prompt=none" パラメーターを使用してトークンを取得します。 これにより、承認エンドポイントが使用されますが、ユーザーに対して UI は表示されません。 Microsoft ID は、ユーザーと対話せずにアプリにトークンを提供できる場合、そのようにします。 このいずれの方法でもトークンが取得されない場合は、ユーザーが対話形式で再認証する必要があります。

> [!NOTE]
> 一般に、アプリは、「ログイン」や「同意」などのプロンプトの使用を避ける必要があります。それによって、操作が必要ないときでもユーザーによる操作が強制されるからです。

## <a name="handle-service-responses-properly"></a>サービスの応答を適切に処理する

アプリケーションはすべてのエラー応答を処理する必要がありますが、応答によっては、回復性に影響を与える可能性があるものがあります。 アプリケーションが HTTP 429 応答コード (Too Many Requests) を受信した場合、Microsoft ID によって要求が調整されています。 アプリによって行われる要求が多すぎる状態が続くと、トークンを受信できないように、アプリは調整されたままになります。 [Retry-After]\(再試行までの時間\) 応答フィールドの時間 (秒単位) が経過するまでは、アプリケーションはトークンの再取得を試行するべきではありません。 429 応答の受信は、多くの場合、アプリケーションでトークンのキャッシュと再利用が正しく行われていないことを示しています。 開発者は、アプリケーションでどのようにトークンがキャッシュされて再利用されているかを確認する必要があります。

アプリケーションが HTTP 5xx 応答コードを受信した場合、アプリは高速再試行ループに入ってはなりません。 存在する場合、アプリケーションは、429 応答の場合と同じ Retry-After (再試行までの時間) 処理に従う必要があります。 応答によって "Retry-After" ヘッダーが提供されていない場合は、応答後 5 秒以上経過してから最初の再試行を行うようにして、指数バックオフ再試行を実装することをお勧めします。

要求がタイムアウトになったときに、アプリケーションでは直ちに再試行すべきではありません。 応答から 5 秒以上経過した後に最初の再試行を行うようにして、指数バックオフ再試行を実装します。

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>認可関連情報を取得するためのオプションを評価する

多くのアプリケーションと API には、認可の決定を行うために、ユーザーに関する特定の情報が必要です。 アプリケーションでこの情報を取得するには、いくつかの方法があります。 それぞれの方法には長所と短所があります。 開発者は、それらを比較して、アプリの回復性に最適な戦略を決定する必要があります。

### <a name="tokens"></a>トークン

ID トークンとアクセス トークンには、サブジェクトに関する情報を提供する標準の要求が含まれています。 これらについては、「[Microsoft ID プラットフォームの ID トークン](../develop/id-tokens.md)」と「[Microsoft ID プラットフォーム アクセス トークン](../develop/access-tokens.md)」で説明されています。 アプリに必要な情報が既にトークンに含まれている場合、そのデータを取得する最も効率的な方法は、トークン要求を使用することです。それにより、情報を個別に取得するための追加ネットワーク呼び出しのオーバーヘッドが削減されるためです。 ネットワーク呼び出し回数が減るということは、アプリケーションの全体的な回復性が向上することを意味します。

> [!NOTE]
> 一部のアプリケーションでは、UserInfo エンドポイントを呼び出して、認証したユーザーに関する要求が取得されます。 アプリが受信できる ID トークンで利用できる情報は、UserInfo エンドポイントから取得できる情報の上位集合です。 アプリは、UserInfo エンドポイントを呼び出す代わりに、ID トークンを使用してユーザーに関する情報を取得する必要があります。

アプリ開発者は、[オプションの要求](../develop/active-directory-optional-claims.md)によって標準のトークン要求を補強できます。 一般的なオプションの要求の 1 つは、[グループ](../develop/active-directory-optional-claims.md#configuring-groups-optional-claims)です。 グループ要求を追加するには、いくつかの方法があります。 "Application Group" オプションでは、アプリケーションに割り当てられたグループのみが含まれます。 "All" または "Security groups" オプションでは、同じテナント内のすべてのアプリのグループが含まれます。これにより、多くのグループをトークンに追加できます。 自分のケースに応じて効果を評価することが重要です。トークンが肥大化し、グループの完全な一覧を取得するための追加呼び出しが必要になることで、トークンでグループを要求することによって得られる効率性が打ち消される可能性があるからです。

トークン内でグループを使用する代わりに、アプリ ロールを使用して含めることができます。 開発者は、アプリと API に対して[アプリ ロール](../develop/howto-add-app-roles-in-azure-ad-apps.md)を定義できます。顧客は、ポータルまたは API を使用して自分のディレクトリからそれらを管理できます。 IT 担当者は、ロールをさまざまなユーザーやグループに割り当てて、どのユーザーがどのコンテンツや機能にアクセスできるかを制御できます。 アプリケーションや API に対してトークンが発行されると、ユーザーに割り当てられたロールが、トークン内のロール要求で使用可能になります。 トークンでこの情報を直接取得することにより、追加の API 呼び出しを行わずに済みます。

最後に、IT 管理者は、テナント内の特定の情報に基づいて要求を追加することもできます。 たとえば、企業は、企業固有のユーザー ID を持つ拡張機能を使用できます。

いずれの場合も、ディレクトリからトークンに直接情報を追加することで効率的になり、アプリが持つ依存関係の数を減らすことでアプリの回復性を向上させることができます。 一方、トークンを取得できないことによる回復性の問題は対処されていません。 オプション要求の追加は、アプリケーションの主要なシナリオについてのみ行う必要があります。 アプリが管理機能のためだけに情報を必要とする場合は、アプリケーションで、必要なときにのみその情報を取得することをお勧めします。

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph には、組織内の生産性、ID、およびセキュリティのパターンを記述する Microsoft 365 データにアクセスするための統合 API エンドポイントが用意されています。 Microsoft Graph を使用するアプリケーションでは、認可に関する決定を行うために Microsoft 365 全体の情報のいずれかが使用される可能性があります。

すべての Microsoft 365 にアクセスするために、アプリで必要なトークンは 1 つだけです。 これは、複数のトークンを必要とする Microsoft Exchange や Microsoft SharePoint などの Microsoft 365 コンポーネントに固有の古い API を使用する場合に比べて回復性が高くなります。

Microsoft Graph API を使用するときは、[Microsoft Graph SDK](/graph/sdks/sdks-overview) を使用することをお勧めします。 Microsoft Graph SDK は、Microsoft Graph にアクセスする高品質かつ効率的で、回復性を備えたアプリケーションの構築を簡素化するように設計されています。

認可の決定については、開発者は、一部の Microsoft Graph 呼び出しの代わりに、トークンに含まれている要求をどのような場合に使用するかを検討する必要があります。 前述のように、開発者はトークン内でグループ、アプリ ロール、およびオプションの要求を要求できます。 回復性の観点からは、Microsoft Graph を認可に使用すると、Microsoft ID (Microsoft Graph にアクセスするためのトークンを取得するため) と Microsoft Graph 自体に依存する追加のネットワーク呼び出しが必要になります。 ただし、アプリケーションがデータ レイヤーとして Microsoft Graph に既に依存している場合は、認可のために Graph に依存しても、追加のリスクを負うことにはなりません。

## <a name="use-broker-authentication-on-mobile-devices"></a>モバイル デバイスでブローカー認証を使用する

モバイル デバイスでは、Microsoft Authenticator のような認証ブローカーを使用すると、回復性が向上します。 ブローカーによって、システム ブラウザーや埋め込み WebView などの他のオプションの場合よりも多くの利点が得られます。 認証ブローカーは、[プライマリ更新トークン](../devices/concept-primary-refresh-token.md) (PRT) を利用できます。これにはユーザーとデバイスに関する要求が含まれおり、デバイスから他のアプリケーションにアクセスするための認証トークンを取得するために使用できます。 アプリケーションへのアクセスを要求するために PRT が使用されると、そのデバイスと MFA の要求は Azure AD によって信頼されます。 これにより、デバイスを再度認証するための追加手順が回避されて、回復性が向上します。 ユーザーは同じデバイス上で複数の MFA プロンプトによってチャレンジされないため、外部サービスへの依存関係を減らすことで回復性を高め、ユーザー エクスペリエンスを向上させることができます。

![アプリケーションによって Microsoft ID への呼び出しが行われても、その呼び出しは、アプリケーションが実行されているデバイス上のトークン キャッシュとトークン ストアおよび認証ブローカーを通過します](media/resilience-client-app/authentication-broker.png)

ブローカー認証は、MSAL によって自動的にサポートされます。 ブローカー認証の使用の詳細については、次のページを参照してください。

- [macOS と iOS で SSO を構成する](../develop/single-sign-on-macos-ios.md#sso-through-authentication-broker-on-ios)
- [方法: Android で MSAL を使用してクロスアプリ SSO を有効にする](../develop/msal-android-single-sign-on.md)

## <a name="adopt-continuous-access-evaluation"></a>継続的アクセス評価を採用する

[継続的アクセス評価 (CAE)](../conditional-access/concept-continuous-access-evaluation.md) は、有効期間が長いトークンを使用してアプリケーションのセキュリティと回復性を高めることができる最新の開発です。 CAE は、OpenID Foundation の Shared Signals と Events Working Groupで開発されている新たな業界標準です。 CAE では、短いトークンの有効期間に依存するのではなく、[重大なイベント](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation)と[ポリシーの評価](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview)に基づいてアクセス トークンを取り消すことができます。 一部のリソース API の場合、リスクとポリシーがリアルタイムで評価されるため、CAE によってトークンの有効期間を最大 28 時間まで大幅に延ばすことができます。 リソース API とアプリケーションで CAE が採用されると、Microsoft ID は、取り消し可能で、長時間有効なアクセス トークンを発行できるようになります。 これらの有効期間が長いトークンは、MSAL によって事前に更新されます。

CAE は初期段階にありますが、アプリケーションで使用されているリソース (API) で CAE が採用されている場合は、[CAE を活用するクライアント アプリケーションを今すぐ開発](../develop/app-resilience-continuous-access-evaluation.md)できます。 CAE を採用するリソースが増えるにつれて、アプリケーションでも、それらのリソースに対する CAE 対応トークンを取得できるようになります。 Microsoft Graph API と [Microsoft Graph SDK](/graph/sdks/sdks-overview) では、2021 年の早期に CAE 機能のプレビューが行われます。 CAE に関する Microsoft Graph のパブリック プレビューへの参加をご希望の場合は、こちらからその旨をお知らせください: [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview)。

リソース API を開発する場合は、[Shared Signals と Events WG](https://openid.net/wg/sse/) に参加されることをお勧めします。 Microsoft は、Microsoft ID とリソース プロバイダー間のセキュリティ イベントの共有を可能にするために、こちらのグループと連携しています。

## <a name="next-steps"></a>次のステップ

- [継続的アクセス評価が有効になった API をアプリケーションで使用する方法](../develop/app-resilience-continuous-access-evaluation.md)
- [デーモン アプリケーションで回復性を強化する](resilience-daemon-app.md)
- [ID およびアクセス管理インフラストラクチャで回復性を強化する](resilience-in-infrastructure.md)
- [CIAM システムで回復性を強化する](resilience-b2c.md)
