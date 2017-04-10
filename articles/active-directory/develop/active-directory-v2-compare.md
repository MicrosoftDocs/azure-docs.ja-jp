---
title: "Azure AD v2.0 エンドポイントの変更点 | Microsoft Docs"
description: "元の Azure AD と v2.0 エンドポイントの比較。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 47dce83cb4e3e5df92e91f1ca9195326634d6c8b
ms.openlocfilehash: 9f00013c4eb6c32707489d5f78a5e95b7419bcd2


---
# <a name="whats-different-about-the-v20-endpoint"></a>v2.0 エンドポイントの変更点
Azure Active Directory を使い慣れている場合、または以前にアプリを Azure AD に統合した場合は、v2.0 エンドポイントで予想外の違いを体験する可能性があります。  このドキュメントでは、それらの違いについて説明します。

> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。  v2.0 エンドポイントを使用する必要があるかどうかを判断するには、 [v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。
>
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Microsoft アカウントと Azure AD アカウント
v2.0 エンドポイントを使用すると、開発者は、1 つの認証エンドポイントを使用して Microsoft アカウントと Azure AD アカウントの両方からのサインインを受け付けるアプリを作成できます。  これにより、まったくアカウントに依存しないアプリを作成できます。ユーザーがサインインにどのアカウントを使用するかを考慮する必要がありません。  もちろん、特定のセッションで使用されているアカウントの種類をアプリで確認することは*できます*が、そうする必要はありません。

たとえば、アプリで [Microsoft Graph](https://graph.microsoft.io)を呼び出す場合、エンタープライズ環境のユーザーは、SharePoint サイトや Directory データなどの追加の機能とデータを使用できます。  ただし、 [ユーザーのメールを読む](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message)などの多くの操作について、Microsoft アカウントと Azure AD アカウントの両方に対して完全に同じコードを作成できます。  

Microsoft アカウントと Azure AD アカウントのアプリを&1; つの単純なプロセスで統合できるようになりました。  1 組のエンドポイント、1 つのライブラリ、1 回のアプリ登録によって、コンシューマー向けおよびエンタープライズ向けの両方の環境にアクセスできます。  v2.0 エンドポイントの詳細については、 [概要](active-directory-appmodel-v2-overview.md)のページを参照してください。

## <a name="new-app-registration-portal"></a>新しいアプリ登録ポータル
v2.0 エンドポイントは、新しい場所 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) にのみ登録できます。  このポータルでは、アプリケーション ID の取得、アプリのサインイン ページの外観のカスタマイズなどを行うことができます。  ポータルにアクセスするために必要なのは、Microsoft の強化されたアカウント (個人アカウントまたは職場/学校アカウントのいずれか) だけです。  

このアプリ登録ポータルには、多くの機能を継続的に追加していく予定です。  このポータルは、Microsoft アプリに関連したあらゆる情報を一元管理できる新たな場所になります。

## <a name="one-app-id-for-all-platforms"></a>1 つのアプリ ID ですべての製品に対応
元の Azure Active Directory サービスでは、1 つのプロジェクトに対して異なる複数のアプリを登録している場合があります。  その場合、ネイティブ クライアントと Web アプリで別々にアプリ登録する必要がありました。

![古いアプリケーションの登録 UI](../../media/active-directory-v2-flows/old_app_registration.PNG)

たとえば、Web サイトと iOS アプリの両方を構築する場合は、2 つの異なるアプリケーション ID を使用して、それぞれを別々に登録する必要がありました。  Web サイトとバックエンド Web API を使用していた場合、Azure AD に別々のアプリとして登録していました。  また、iOS アプリと Android アプリを使用していた場合は、2 つの異なるアプリを登録していました。  

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

v2.0 では、各プロジェクトに対して&1; つのアプリケーション ID を使用して&1; 回のアプリ登録を行うだけでかまいません。  それぞれのプロジェクトに複数の "プラットフォーム" を追加し、追加した各プラットフォームに対して適切なデータを提供できます。  もちろんアプリは要件に応じて必要な数だけ作成できますが、大半の場合、必要なアプリケーション ID は&1; つだけです。

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

マイクロソフトが目指しているのは、アプリの管理と開発のエクスペリエンスをいっそう簡素化し、より高度に統合されたビューで作業中の各プロジェクトの状況を把握できるようにすることです。

## <a name="scopes-not-resources"></a>リソースではなくスコープ
元の Azure AD サービスの場合、アプリは**リソース**として、またはトークンの受信者として動作できます。  リソースには、リソースで識別できる多数の**スコープ**または **oAuth2Permissions** を定義できます。それによりクライアント アプリは、そのリソースに特定のスコープのセットのトークンを要求できます。  リソースの例として、Azure AD Graph API があります。

* リソース識別子、または `AppID URI`:`https://graph.windows.net/`
* スコープ、または `OAuth2Permissions`:`Directory.Read`、`Directory.Write` などです。  

このすべてが v2.0 エンドポイントに当てはまります。  アプリはリソースとして動作し、スコープを定義することができ、URI によって識別できます。  また、クライアント アプリは、スコープへのアクセスを要求できます。  ただし、クライアントがアクセス許可を要求する方法が変更されました。  以前は、Azure AD への OAuth 2.0 承認要求は、次のようになっていました。

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

ここで、 **resource** パラメーターは、クライアント アプリが認証を求めている対象のリソースを示します。  Azure AD は、Azure Portal での静的な構成に基づいて、アプリによって要求されたアクセス許可を計算し、適切なトークンを発行していました。  現在は、同じ OAuth 2.0 承認要求が次のようになっています。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

ここで、**scope** パラメーターは、アプリが承認を要求している対象のリソースとアクセス許可を示します。 要求されているリソースはまだ要求内に多く存在し、scope パラメーターの各値に含まれています。  このように scope パラメーターを使用すると、v2.0 エンドポイントの OAuth 2.0 仕様への準拠を高め、一般的な業界の慣行に近づけることができます。  また、アプリで[増分同意](#incremental-and-dynamic-consent)を実行できます。次のセクションでこれについて説明します。

## <a name="incremental-and-dynamic-consent"></a>増分および動的な同意
一般公開の Azure AD サービスに登録されたアプリは、作成時に Azure Portal で、必要な OAuth 2.0 のアクセス許可を指定する必要がありました。

![アクセス許可の登録 UI](../../media/active-directory-v2-flows/app_reg_permissions.PNG)

アプリが要求したアクセス許可は **静的に**構成されていました。  これにより Azure Portal にアプリの構成を存在させることができ、コードを適切に維持できる一方、開発者にとっては問題もいくつかあります。

* アプリの作成時に、そのアプリで必要になる可能性のあるすべてのアクセス許可がわかっている必要があります。  後からアクセス許可を追加することは困難なプロセスでした。
* また、アプリでアクセスする可能性のあるすべてのリソースが事前にわかっている必要があります。  任意の数のリソースにアクセスするアプリを作成することは、困難でした。
* アプリは、ユーザーの初回サインインの時点で、そのアプリで必要になる可能性があるすべてのアクセス許可を要求する必要があります。  アクセス許可のリストは非常に長くなる場合があり、エンドユーザーが初回サインイン時にアプリのアクセスを承認する阻害要因となっていました。

v2.0 エンドポイントでは、アプリの通常の使用状況で、アプリに必要なアクセス許可を実行時に **動的に**指定できます。  これを行うには、任意の時点でアプリに必要なスコープを承認要求の `scope` パラメーターで指定できます。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

この要求は、アプリで Azure AD ユーザーのディレクトリ データの読み取りおよび書き込みのためのアクセス許可を求めています。  ユーザーが過去にこの特定のアプリでこれらのアクセス許可に同意した場合、資格情報を入力してアプリにサインインできます。  ユーザーがこれらのアクセス許可のいずれにも同意していない場合、v2.0 エンドポイントはこれらのアクセス許可に対する同意をユーザーに求めます。  詳細については、 [アクセス許可、同意、スコープ](active-directory-v2-scopes.md)の説明を参照してください。

アプリで `scope` パラメーターを使用してアクセス許可を動的に要求できるため、ユーザーの操作を完全に制御できます。  必要に応じて、同意の操作を初期段階に組み込み、1 回の初期認証要求ですべてのアクセス許可を求めます。  または、アプリで多数のアクセス許可が必要な場合は、時間の経過と共に、ユーザーがアプリの特定の機能を使用するときにアクセス許可を集めることができます。

## <a name="well-known-scopes"></a>既知のスコープ
#### <a name="offline-access"></a>オフライン アクセス
v2.0 エンドポイントでは、アプリのよく知られた新しいアクセス許可を使用することが必要な場合があります ( `offline_access` スコープ)。  すべてのアプリは、ユーザーがアプリを積極的に利用しない可能性がある場合でも、長期間ユーザーに代わってリソースにアクセスするために必要な、このアクセス許可を要求する必要があります。  `offline_access` スコープは、コンテンツ ダイアログで "データにオフラインでアクセスします" と表示されます。  `offline_access` アクセス許可を要求すると、Web アプリで v2.0 エンドポイントから OAuth 2.0 の refresh_tokens を受け取ることができます。  refresh_tokens は、長期間維持され、アクセスの期間を延長するために新しい OAuth 2.0 の access_tokens と交換することができます。  

アプリが `offline_access` スコープを要求しない場合、refresh_tokens を受け取ることはありません。  つまり、OAuth 2.0 承認コード フローの authorization_code を使用すると、`/token` エンドポイントから access_token だけが取得されます。  その access_token は、短時間 (通常は&1; 時間) 有効ですが、最終的には期限が切れます。  その時点で、アプリはユーザーを `/authorize` エンドポイントにリダイレクトして、新しい authorization_code を取得する必要があります。  このリダイレクト中に、アプリの種類によっては、ユーザーが資格情報を再入力したり、アクセス許可に再同意したりする必要がある場合もあります。

OAuth 2.0、refresh_tokens、および access_tokens の詳細については、[v2.0 プロトコルのリファレンス](active-directory-v2-protocols.md)を参照してください。

#### <a name="openid-profile-and-email"></a>OpenID、プロファイルと電子メール
元の Azure Active Directory サービスでは、結果の id_token のユーザーに関する情報は、最も基本的な OpenID Connect サインイン フローで多数提供されていました。  id_token 内の要求には、ユーザー名、推奨ユーザー名、電子メール アドレス、オブジェクト ID などを含めることができます。

今は、アプリで `openid` のスコープでアクセスできる情報が制限されるようになりました。  ‘openid’ スコープでは、ユーザーのサインインの許可と、ユーザーのアプリ固有の ID の受信のみをアプリに許可します。  アプリ内のユーザーの個人を特定できる情報 (PII) を取得するには、アプリからユーザーに追加のアクセス許可を要求する必要があります。  それを実行できる&2; つの新しいスコープ、`email` と `profile` スコープが導入されました。

`email` スコープは非常に明解です。id_token の `email` 要求を使用すると、アプリでユーザーのプライマリ電子メール アドレスにアクセスできます。  `profile` スコープでは、アプリに名前、推奨ユーザー名、オブジェクト ID など、ユーザーに関するその他のすべての基本的な情報へのアクセスを許可します。

これにより、最小限の公開でアプリをコーディングできます。アプリがそのジョブを実行するために必要な情報セットのみをユーザーに要求できます。  これらのスコープの詳細については、[v2.0 スコープのリファレンス](active-directory-v2-scopes.md)を参照してください。

## <a name="token-claims"></a>トークン要求
v2.0 エンドポイントによって発行されたトークンでの要求は、一般公開の Azure AD エンドポイントによって発行されるトークンと同一にはなりません。新しいサービスに移行するアプリでは、id_tokens または access_tokens 内に特定の要求の存在を想定しないでください。   v2.0 エンドポイントによって発行されたトークンは OAuth 2.0 および OpenID Connect 仕様に準拠していますが、一般公開の Azure AD サービスとは異なるセマンティクスに従っている可能性があります。

v2.0 トークンによって発行される特定の要求の詳細については、 [v2.0 トークン リファレンス](active-directory-v2-tokens.md)を参照してください。

## <a name="limitations"></a>制限事項
v2.0 エンドポイントを使用する場合に注意する必要があるいくつかの制限があります。  これらの制限事項が実際のシナリオに当てはまるかどうかについては、「 [v2.0 の制限事項に関するドキュメント](active-directory-v2-limitations.md) 」を参照してください。



<!--HONumber=Jan17_HO4-->


