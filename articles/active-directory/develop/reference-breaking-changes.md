---
title: Azure Active Directory 重大な変更に関するリファレンス
description: アプリケーションに影響を与える可能のある Azure AD プロトコルの変更について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 2/22/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c5e7f556f37a1d6d53e0a938490f1099a7be776a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647423"
---
# <a name="whats-new-for-authentication"></a>認証の新機能

> このページの更新に関する通知を受け取るには、この URL を RSS フィード リーダーに貼り付けてください。<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

認証システムは、セキュリティの向上と規格への準拠を確保するために、継続的に機能の変更と追加を行います。 常に最新の開発情報を把握していただけるよう、この記事では次の事項に関する情報を提供します。

- 最新の機能
- 既知の問題
- プロトコルの変更
- 非推奨の機能

> [!TIP]
> このページは頻繁に更新されるため、定期的にアクセスしてご確認ください。 特に明記されていない限り、これらの変更は新規に登録されたアプリケーションに対してのみ適用されます。

## <a name="upcoming-changes"></a>今後の変更

### <a name="conditional-access-will-only-trigger-for-explicitly-requested-scopes"></a>条件付きアクセスは、明示的に要求されたスコープに対してのみトリガーされる

**有効日**: 2021 年 3 月

**影響を受けるエンドポイント**: v2.0

**影響を受けるプロトコル**:[動的な同意](v2-permissions-and-consent.md#requesting-individual-user-consent)が使用されているすべてのフロー

現在、動的な同意が使用されているアプリケーションでは、名前を指定して `scope` パラメーターで要求されなかった場合でも、同意があるすべてのアクセス許可が付与されます。  これにより、たとえば、`user.read` のみが要求されているが、`files.read` に対する同意も含まれているアプリでは、`files.read` アクセス許可のために割り当てられている条件付きアクセスが強制的に渡される場合があります。 

不要な条件付きアクセスのプロンプト数を減らすため、Azure AD では、要求されていないスコープがアプリケーションに提供される方法を変更し、明示的に要求されたスコープのみによって条件付きアクセスがトリガーされるようにします。 この変更によって、Azure AD の以前の動作 (つまり、要求されていなくてもすべてのアクセス許可を提供する) に依存しているアプリでは、要求するトークンにアクセス許可が不足することになり、問題が発生する可能性があります。

今後、アプリでは、混合したアクセス許可 (要求されたものと、同意はあるが、条件付きアクセスのプロンプトが必要ないもの) が含まれたアクセス トークンを受け取ります。  アクセス トークンのスコープは、トークン応答の `scope` パラメーターに反映されます。 

**使用例**

アプリには、`user.read`、`files.readwrite`、および `tasks.read` に対する同意があります。 `files.readwrite` には、条件付きアクセス ポリシーが適用されていますが、他の 2 つには適用されていません。 アプリで `scope=user.read` に対するトークン要求が行われ、現在サインインしているユーザーが条件付きアクセス　ポリシーを渡していない場合、結果として得られるトークンは `user.read` と `tasks.read` のアクセス許可を対象としたものになります。 `tasks.read` が含まれている理由は、それに対する同意がアプリにあり、かつ条件付きアクセス ポリシーを適用する必要がないからです。 

次に、アプリで `scope=files.readwrite` が要求されると、テナントによって要求される条件付きアクセスがトリガーされ、条件付きアクセス ポリシーを満たすことができる対話型認証プロンプトの表示がアプリに対して強制されます。  返されるトークンには、3 つすべてのスコープが含まれます。 

次に、アプリがその 3 つのスコープのいずれか (たとえば、`scope=tasks.read`) に対して最後の要求を行うと、Azure AD では、ユーザーが `files.readwrite` に必要な条件付きアクセス ポリシーを既に完了していることを確認し、3 つすべてのアクセス許可が含まれたトークンを再び発行します。 


## <a name="may-2020"></a>2020 年 5 月

### <a name="azure-government-endpoints-are-changing"></a>Azure Government エンドポイントの変更

**発効日**:5 月 5 日 (2020 年 6 月終了) 

**影響を受けるエンドポイント**:All

**影響を受けるプロトコル**:すべてのフロー

2018 年 6 月 1 日、Azure Government に対する Azure Active Directory (AAD) の公式な機関が、`https://login-us.microsoftonline.com` から `https://login.microsoftonline.us` に変更されました。 この変更は、Azure Government AAD でもサービスが提供される Microsoft 365 GCC High および DoD にも適用されます。 米国政府テナント内でアプリケーションを所有している場合は、`.us` エンドポイントでユーザーをサインインさせるようにアプリケーションを更新する必要があります。  

5 月 5 日以降、Azure AD でエンドポイントの変更の適用が開始され、政府ユーザーはパブリック エンドポイント (`microsoftonline.com`) を使用して米国政府テナントでホストされているアプリにサインインできなくなります。  影響を受けるアプリでは、`AADSTS900439` - `USGClientNotSupportedOnPublicEndpoint` エラーが表示されるようになります。 このエラーは、アプリがパブリック クラウド エンドポイントで米国政府ユーザーのサインインを試みていることを示します。 アプリがパブリック クラウド テナント内にあり、米国政府ユーザーのサポートを意図している場合は、[それらのユーザーを明示的にサポートするようにアプリを更新する](./authentication-national-cloud.md)必要があります。 これには、米国政府機関向けクラウドで新しいアプリの登録を作成することが必要になる場合があります。 

この変更の適用は、米国政府のクラウドからアプリケーションにサインインするユーザーの頻度に基づいて、段階的なロールアウトを使用して行われます。米国政府ユーザーがサインインする頻度の少ないアプリは最初に適用され、米国政府ユーザーが頻繁に使用するアプリは最後に適用されます。 2020 年 6 月には、すべてのアプリで適用が完了するものと思われます。 

詳細については、[この移行に関する Azure Government ブログ記事](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)を参照してください。 

## <a name="march-2020"></a>2020 年 3 月

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>ユーザーのパスワードは、256 文字に制限されます。

**発効日**:2020 年 3 月 13 日

**影響を受けるエンドポイント**:All

**影響を受けるプロトコル**:すべてのユーザー フローです。

256 文字を超えるパスワードを使用して (ADFS などのフェデレーション IDP ではなく) Azure AD に直接接続するユーザーは、2020 年 3 月 13 日以降はサインインできなくなり、パスワードをリセットするように求められます。  管理者は、ユーザーのパスワード リセットを支援するように要求される場合があります。

サインイン ログのエラーは、AADSTS 50052: InvalidPasswordExceedsMaxLength

メッセージ: `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

修復:

パスワードが許可されている最大長を超えているため、ユーザーはログインできません。 パスワードをリセットするには、管理者に連絡する必要があります。 テナントで SSPR が有効になっている場合は、[パスワードを忘れた場合] のリンクを使用してパスワードをリセットできます。



## <a name="february-2020"></a>2020 年 2 月

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>ログイン エンドポイントからのすべての HTTP リダイレクトに空のフラグメントが追加されます。

**発効日**:2020 年 2 月 8 日

**影響を受けるエンドポイント**:v1.0 と v2.0 の両方

**影響を受けるプロトコル**:response_type=query を使用する OAuth および OIDC フロー。これには [承認コード フロー](v2-oauth2-auth-code-flow.md) (場合による) と [暗黙のフロー](v2-oauth2-implicit-grant-flow.md)が含まれます。

login.microsoftonline.com から HTTP リダイレクト経由で認証応答がアプリケーションに送信されると、サービスによって空のフラグメントが応答 URL に追加します。  これにより、ブラウザーで認証要求内の既存のフラグメントがすべて消去され、リダイレクト攻撃のクラスを防止できます。  アプリはこの動作に依存しないようにしてください。


## <a name="august-2019"></a>2019 年 8 月

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>POST フォームのセマンティクスがより厳密に適用され、スペースおよび引用符は無視されます

**発効日**:2019 年 9 月 2 日

**影響を受けるエンドポイント**:v1.0 と v2.0 の両方

**影響を受けるプロトコル**:POST が使用されるすべての場所 ([クライアント資格情報](./v2-oauth2-client-creds-grant-flow.md)、[承認コードの利用](./v2-oauth2-auth-code-flow.md)、[ROPC](./v2-oauth-ropc.md)、[OBO](./v2-oauth2-on-behalf-of-flow.md)、および [更新トークンの利用](./v2-oauth2-auth-code-flow.md#refresh-the-access-token))

9/2 の週から、POST メソッドを使用する認証要求は、より厳格な HTTP 標準を使用して検証されます。  具体的には、スペースと二重引用符 (") が要求フォームの値から削除されなくなります。 これらの変更によって、既存のクライアントが中断されることはなく、Azure AD に送信された要求は毎回確実に処理されます。 今後 (上記参照)、重複するパラメーターを拒否し、要求内の BOM を無視することをさらに計画しています。

例:

現在、`?e=    "f"&g=h` は `?e=f&g=h` と同じように解析されるため、`e` == `f` となります。  この変更により、これは `e` == `    "f"` と解析されるようになりました。これは有効な引数である可能性が低く、要求は失敗します。


## <a name="july-2019"></a>2019 年 7 月

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>シングルテナント アプリケーションのアプリ専用トークンは、クライアント アプリがリソース テナントに存在する場合にのみ発行される

**発効日**:2019 年 7 月 26 日

**影響を受けるエンドポイント**:[v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) と [v2.0](./v2-oauth2-client-creds-grant-flow.md) の両方

**影響を受けるプロトコル**:[クライアント資格情報 (アプリ専用トークン)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

(クライアント資格情報の付与による) アプリ専用トークンの発行方法を変更するセキュリティの変更が、7 月 26 日に行われました。 以前は、テナント内の存在またはそのアプリケーションに対して同意されているロールに関係なく、アプリケーションではトークンを取得して他のアプリを呼び出すことができました。  この動作が更新され、シングルテナント (既定) に設定されているリソース (Web API と呼ばれることもあります) の場合、クライアント アプリケーションはリソース テナント内に存在する必要があります。  クライアントと API の間の既存の同意は依然として必須ではなく、アプリでは、`roles` 要求が存在し、API に必要な値が含まれていることを確認するために、独自の承認チェックを実行する必要があることに注意してください。

現在、このシナリオのエラー メッセージは次のようになっています。

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

この問題を解決するには、管理者の同意エクスペリエンスを使ってテナントにクライアント アプリケーション サービス プリンシパルを作成するか、手動で作成します。  この要件により、テナントによってアプリケーションにテナント内で動作するアクセス許可が付与されていることが確認されます。

#### <a name="example-request"></a>要求の例

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` この例では、リソース テナント (機関) は contoso.com、リソース アプリは Contoso テナントに対する `gateway.contoso.com/api` という名前のシングルテナント アプリ、クライアント アプリは `14c88eee-b3e2-4bb0-9233-f5e3053b3a28` です。  クライアント アプリが Contoso.com 内にサービス プリンシパルを持っている場合は、この要求を続行できます。  そうでない場合、要求は上記のエラーで失敗します。

ただし、Contoso ゲートウェイ アプリがマルチテナント アプリケーションの場合は、クライアント アプリのサービス プリンシパルが Contoso.com 内にあるかどうかに関係なく、要求は続行されます。

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>リダイレクト URI にクエリ文字列パラメーターを含めることができるようになった

**発効日**:2019 年 7 月 22 日

**影響を受けるエンドポイント**:v1.0 と v2.0 の両方

**影響を受けるプロトコル**:すべてのフロー

[RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2) に従って、Azure AD アプリケーションでは、OAuth 2.0 要求に対する静的クエリ パラメーター (`https://contoso.com/oauth2?idp=microsoft` など) でリダイレクト (応答) URI を登録して使用できるようになりました。  動的リダイレクト URI は、セキュリティ上のリスクがあり、認証要求全体で状態情報を保持するために使用できないため、引き続き許可されません。そのためには、`state` パラメーターを使用します。

静的クエリ パラメーターは、リダイレクト URI の他の部分と同様に、リダイレクト URI の文字列照合の対象になります。URI でデコードされたリダイレクト URI に一致する文字列が登録されていない場合、要求は拒否されます。  アプリの登録で URI が見つかった場合は、静的クエリ パラメーターを含む文字列全体が、ユーザーをリダイレクトするために使われます。

現時点 (2019 年 7 月末) では、Azure portal のアプリ登録 UX では、クエリ パラメーターが引き続きブロックされることに注意してください。  ただし、アプリケーション マニフェストを手動で編集して、クエリ パラメーターを追加し、アプリでこれをテストすることができます。


## <a name="march-2019"></a>2019 年 3 月

### <a name="looping-clients-will-be-interrupted"></a>クライアントのループ処理が中断されます

**発効日**:2019 年 3 月 25 日

**影響を受けるエンドポイント**:v1.0 と v2.0 の両方

**影響を受けるプロトコル**:すべてのフロー

クライアント アプリケーションが誤動作し、短期間に数百の同じログイン要求を発行する場合があります。  これらの要求は成功する場合もしない場合もありますが、そのすべてがユーザー エクスペリエンスの低下や IDP のワークロードの増加につながるため、すべてのユーザーの待ち時間が長くなり、IDP の可用性が低下します。  これらのアプリケーションは通常の使用の範囲外で動作しており、正しく動作するように更新する必要があります。

重複した要求を複数回発行するクライアントには `invalid_grant` エラー: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` が送信されます。

ほとんどのクライアントは、動作を変更してこのエラーを回避する必要はありません。  正しく構成されていない (トークンのキャッシュを使用していない、または既にプロンプト ループを示している) クライアントのみがこのエラーの影響を受けます。  クライアントは、次の要因に対して (Cookie 経由で) ローカルにインスタンスごとに追跡されます。

* ユーザー ヒント (存在する場合)

* 要求されているスコープまたはリソース

* クライアント ID

* リダイレクト URI

* 応答の種類とモード

短期間 (5 分) に複数の (15 を超える) 要求を発行しているアプリは、ループ処理していることを示す `invalid_grant` エラーを受信します。  要求されているトークンには十分に長い有効期間 (最小 10 分、既定では 60 分) があるため、この期間にわたって繰り返される要求は必要ありません。

すべてのアプリが、確認なしでトークンを要求するのではなく、対話型プロンプトを示すことによって `invalid_grant` を処理する必要があります。  このエラーを回避するために、クライアントは、受信したトークンを正しくキャッシュしていることを確認する必要があります。


## <a name="october-2018"></a>2018 年 10 月

### <a name="authorization-codes-can-no-longer-be-reused"></a>認証コードを再利用できなくなりました

**発効日**:2018 年 11 月 15 日

**影響を受けるエンドポイント**:v1.0 と v2.0 の両方

**影響を受けるプロトコル**:[コード フロー](v2-oauth2-auth-code-flow.md)

2018 年 11 月 15 日以降、Azure AD では、以前使用されていた、アプリの認証コードの受け入れが停止されます。 このセキュリティの変更により、Azure AD と OAuth の仕様が一致するようになります。この変更は、v1 と v2 両方のエンドポイントに適用されます。

お使いのアプリで承認コードを再利用して複数のリソースに対するトークンを取得している場合は、コードを使用して更新トークンを取得した後、その更新トークンを使用して他のリソース用のトークンを追加取得することお勧めします。 承認コードは 1 回しか使用できませんが、更新トークンは複数のリソースで複数回使用できます。 OAuth コード フローの使用時に新しいアプリで認証コードを再利用しようとすると、invalid_grant エラーが発生します。

更新トークンについて詳しくは、「[アクセス トークンの更新](v2-oauth2-auth-code-flow.md#refresh-the-access-token)」をご覧ください。  ADAL または MSAL を使用する場合、これはライブラリによって処理され、'AcquireTokenByAuthorizationCodeAsync' の 2 つ目のインスタンスを 'AcquireTokenSilentAsync' に置き換えます。

## <a name="may-2018"></a>2018 年 5 月

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID トークンは OBO フローに使用できません

**日付**:2018 年 5 月 1 日

**影響を受けるエンドポイント**:v1.0 と v2.0 の両方

**影響を受けるプロトコル**:暗黙的フローと [On-Behalf-Of フロー](v2-oauth2-on-behalf-of-flow.md)

2018 年 5 月 1 日以降、id_tokens は新しいアプリケーションの OBO フローでアサーションとして使用できません。 代わりに、アクセス トークンを使用して、同じアプリケーションのクライアントと中間層の間でも、API のセキュリティを確保する必要があります。 2018 年 5 月 1 日より前に登録されたアプリは、引き続き動作し、id_tokens をアクセス トークンに交換することができますが、このパターンはベスト プラクティスとは見なされません。

この変更を回避するには、次の操作を行います。

1. 1 つ以上のスコープを使用して、アプリケーション用の Web API を作成します。 この明示的なエントリ ポイントにより、きめ細かな制御とセキュリティが可能になります。
1. アプリのマニフェスト、[Azure portal](https://portal.azure.com)、または[アプリ登録ポータル](https://apps.dev.microsoft.com)で、アプリが暗黙のフローを介してアクセス トークンを発行できることを確認します。 これは `oauth2AllowImplicitFlow` キーによって制御されます。
1. クライアント アプリケーションで `response_type=id_token` を使用して id_token を要求した場合、上記で作成した Web API に対してもアクセス トークン (`response_type=token`) を要求します。 したがって、v2.0 エンドポイントを使用する場合、`scope` パラメータは `api://GUID/SCOPE` と同様のものになります。 v1.0 エンドポイントでは、`resource` パラメータを Web API のアプリ URI にする必要があります。
1. このアクセストークンを、id_token の代わりに、中間層に渡します。
