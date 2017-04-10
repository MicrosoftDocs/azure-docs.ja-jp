---
title: "Azure Active Directory v2.0 のスコープ、アクセス許可、および同意 | Microsoft Docs"
description: "スコープ、アクセス許可、同意など、Azure AD v2.0 エンドポイントでの承認の説明。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: e00ceb8dd87e00bbdd05146e107c72e6182eb474


---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Azure Active Directory v2.0 エンドポイントでのスコープ、アクセス許可、および同意
Azure Active Directory (Azure AD) と統合されるアプリでは、アプリがどのようにデータにアクセスできるかをユーザーが制御できるようにする承認モデルが使用されます。 この承認モデルの v2.0 実装が更新され、アプリが Azure AD とやり取りする方法が変わりました。 この記事では、スコープ、アクセス許可、同意など、この承認モデルの基本的な概念について説明します。

> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。 v2.0 エンドポイントを使用するかどうかを判断するには、[v2.0 の制限](active-directory-v2-limitations.md)に関する記事を参照してください。
>
>

## <a name="scopes-and-permissions"></a>スコープとアクセス許可
Azure AD では、[OAuth 2.0](active-directory-v2-protocols.md) 承認プロトコルを実装しています。 OAuth 2.0 は、ユーザーに代わってサードパーティのアプリが Web でホストされるリソースにアクセスできる方法です。 Azure AD と統合される、Web でホストされるすべてのリソースは、リソース識別子、つまり*アプリケーション ID URI* を持ちます。 Microsoft の Web でホストされるリソースには、次のようなものがあります。

* Office 365 統合メール API: `https://outlook.office.com`
* Azure AD Graph API: `https://graph.windows.net`
* Microsoft Graph: `https://graph.microsoft.com`

Azure AD と統合されたサードパーティのリソースも同様です。 これらのリソースのいずれでも、機能をより小さいまとまりに分割するために使用できるアクセス許可のセットを定義できます。 たとえば、[Microsoft Graph](https://graph.microsoft.io) では、特に次のタスクを実行するアクセス許可が定義されています。

* ユーザーの予定表の読み取り
* ユーザーの予定表への書き込み
* ユーザーとしてのメールの送信

これらの種類のアクセス許可を定義することで、リソースは、データとそのデータを外部にどのように公開するかをきめ細かく制御できます。 サードパーティのアプリは、アプリのユーザーから、これらのアクセス許可を要求できます。 アプリのユーザーは、アプリがユーザーの代わりに操作できるようにするために、アクセス許可を承認する必要があります。 リソースの機能を細かいアクセス許可セットにまとめることによって、サードパーティのアプリは、機能を実行するために必要な特定のアクセス許可のみを要求するように構築できます。 アプリのユーザーは、アプリがどのようにデータを使用するのかを正確に知ることができるため、アプリが悪意のある動作をしていないことを確信できます。

Azure AD と OAuth では、これらの種類のアクセス許可は*スコープ*と呼ばれています。 または、*oAuth2Permissions* と呼ばれる場合もあります。 スコープは、Azure AD では文字列値として表されます。 Microsoft Graph の例では、各アクセス許可のスコープ値は次のとおりです:

* `Calendar.Read` を使用したユーザーの予定表の読み取り
* `Mail.ReadWrite` を使用したユーザーの予定表への書き込み
* `Mail.Send` を使用したユーザーとしてのメールの送信

アプリは、v2.0 エンドポイントへの要求でスコープを指定することによって、これらのアクセス許可を要求できます。

## <a name="openid-connect-scopes"></a>OpenID Connect のスコープ
OpenID Connect の v2.0 実装には、特定のリソースには適用されない、適切に定義されたスコープ `openid`、`email`、`profile`、`offline_access` があります。

### <a name="openid"></a>openid
アプリは、[OpenID Connect](active-directory-v2-protocols.md) を使用してサインインを行う場合、`openid` スコープを要求する必要があります。 `openid` スコープは、職場アカウントの同意ページでは "サインイン" アクセス許可として表示され、個人用 Microsoft アカウントの同意ページでは "プロフィールの表示と、Microsoft アカウントを使うアプリとサービスへの接続" アクセス許可として表示されます。 このアクセス許可により、`sub` 要求の形式でユーザーの一意の識別子をアプリが受け取ることができます。 アプリが UserInfo エンドポイントにアクセスすることも可能にします。 `openid` スコープは、v2.0 トークン エンドポイントで ID トークンを取得するために使用できます。このトークンは、アプリ内の異なるコンポーネント間の HTTP 呼び出しをセキュリティで保護するために使用できます。

### <a name="email"></a>email
`email` スコープは `openid` スコープやその他のスコープと共に使用できます。 これにより、アプリがユーザーのプライマリ電子メール アドレスに `email` 要求の形式でアクセスできます。 電子メール アドレスがユーザー アカウントと関連付けられている場合のみ (常にではありません)、`email` 要求はトークンに含まれます。 `email` スコープを使用する場合は、トークンに `email` 要求が存在しないケースにも対応できるようにアプリを準備する必要があります。

### <a name="profile"></a>[プロファイル]
`profile` スコープは `openid` スコープやその他のスコープと共に使用できます。 これにより、アプリはユーザーの多くの情報にアクセスできます。 アプリがアクセスできる情報には、ユーザーの名、姓、希望するユーザー名、オブジェクト ID などがありますが、これらに限定されません。 特定のユーザーに対して id_tokens パラメーターで使用できるプロファイル要求の完全な一覧については、「[v2.0 トークンのリファレンス](active-directory-v2-tokens.md)」を参照してください。

### <a name="offlineaccess"></a>offline_access
[ `offline_access`スコープ](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)を使用すると、アプリはユーザーの代わりに、長期間にわたってリソースにアクセスできます。 このスコープは、職場アカウントの同意ページには、"データへの常時アクセス" アクセス許可として表示されます。 個人用 Microsoft アカウントの同意ページには、"あなたの情報にいつでもアクセスする" アクセス許可として表示されます。 ユーザーが `offline_access` スコープを承認すると、アプリは v2.0 トークン エンドポイントから更新トークンを取得できます。 更新トークンの有効期間は長期です。 アプリは、古いアクセス トークンの有効期限が切れると、新しいアクセス トークンを取得できます。

アプリが `offline_access` スコープを要求しない場合、更新トークンを受け取ることはありません。 つまり、[OAuth 2.0 承認コード フロー](active-directory-v2-protocols.md)の承認コードを使用すると、`/token` エンドポイントからアクセス トークンだけが取得されます。 アクセス トークンの有効期間は短期です。 アクセス トークンは、通常、1 時間以内に期限切れとなります。 その時点で、アプリはユーザーを `/authorize` エンドポイントにリダイレクトして、新しい承認コードを取得する必要があります。 このリダイレクト中に、アプリの種類によっては、ユーザーが資格情報を再入力したり、アクセス許可に再同意したりする必要がある場合もあります。

更新トークンの取得方法と使用方法の詳細については、[v2.0 プロトコルのリファレンス](active-directory-v2-protocols.md)を参照してください。

## <a name="requesting-individual-user-consent"></a>個々のユーザーの同意を要求する
[OpenID Connect または OAuth 2.0](active-directory-v2-protocols.md) 承認要求では、アプリは `scope` クエリ パラメーターを使用して、必要なアクセス許可を要求できます。 たとえば、ユーザーがアプリにサインインするときに、アプリは次のような要求を送信します (読みやすくするために、改行を追加しています)。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` パラメーターは、アプリが要求しているスコープの、空白文字で区切られた一覧です。 各スコープは、リソースの識別子 (アプリケーション ID URI) にスコープ値を追加することによって示されます。 上の要求では、ユーザーの予定表を読み取り、ユーザーとしてメールを送信するためのアクセス許可をアプリが必要としています。

ユーザーが資格情報を入力すると、v2.0 エンドポイントが*ユーザーの同意*の一致するレコードを確認します。 要求されたアクセス許可にユーザーがまだ同意したことがない場合、v2.0 エンドポイントは要求されたアクセス許可を付与するようにユーザーに求めます。

![職場アカウントの同意](../../media/active-directory-v2-flows/work_account_consent.png)

ユーザーがアクセス許可を承認すると、同意が記録され、ユーザーが今後のアカウントのサインインで再度同意しなくても済みます。

## <a name="requesting-consent-for-an-entire-tenant"></a>テナント全体の同意を要求する
多くの場合、組織がアプリケーションのライセンスまたはサブスクリプションを購入していれば、従業員に対してアプリケーションを完全にプロビジョニングできます。 このプロセスの一環として、管理者は、従業員の代理としてアプリケーションへの同意を与えることができます。 管理者がテナント全体に対する同意を与えると、組織の従業員にはアプリケーションの同意ページは表示されません。

テナントのユーザー全員に同意を要求するために、アプリで管理者の同意エンドポイントを使用できます。

## <a name="admin-restricted-scopes"></a>管理者によって制限されるスコープ
Microsoft のエコシステムにおける高い権限には、*管理者によって制限されている*とマークされているものもあります。 これらの種類のスコープの例として、次のアクセス許可があります。

* `Directory.Read` を使用した組織のディレクトリ データの読み取り
* `Directory.ReadWrite` を使用した組織のディレクトリへのデータの書き込み
* `Groups.Read.All` を使用した組織のディレクトリでのセキュリティ グループの読み取り

コンシューマー ユーザーがこの種類のデータへのアプリケーション アクセスを許可している場合でも、組織のユーザーは会社の同一の機密データ セットへのアクセスを許可することが制限されます。 アプリケーションが組織のユーザーにアクセス許可を要求する場合は、そのユーザーにはアプリのアクセス許可に同意する権限がないという内容のエラー メッセージが表示されます。

アプリが組織の管理者に制限されるスコープにアクセスする必要がある場合は、次のように管理者の同意エンドポイントを使用して、会社の管理者から直接要求する必要があります。

管理者の同意エンドポイントを介して管理者がこれらのアクセス許可を付与すると、テナントのユーザー全員に対して同意が付与されます。

## <a name="using-the-admin-consent-endpoint"></a>管理者の同意エンドポイントを使用する
これから説明する手順に従うと、管理者に制限されているスコープを含むテナントのユーザー全員のアクセス許可をアプリで収集できます。 手順を実装するコード サンプルについては、[管理者に制限されているスコープのサンプル](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)に関するページを参照してください。

### <a name="request-the-permissions-in-the-app-registration-portal"></a>アプリケーション登録ポータルでアクセス許可を要求する
1. [アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)でアプリケーションに移動するか、アプリを作成していない場合は[アプリを作成](active-directory-v2-app-registration.md)します。
2. **Microsoft Graph のアクセス許可**セクションを検索し、アプリに必要なアクセス許可を追加します。
3. アプリの登録を**保存**します。

### <a name="recommended-sign-the-user-in-to-your-app"></a>推奨: アプリへのユーザーのサインイン
通常、管理者の同意エンドポイントを使用するアプリケーションを構築する場合は、アプリ側に管理者がアプリのアクセス許可を承認できるページやビューが必要です。 このページは、アプリのサインアップ フローやアプリの設定の一部にするか、専用の "接続" フローにすることができます。 多くの場合、職場または学校の Microsoft アカウントでユーザーがサインインした後にのみ、”接続" ビューが表示されます。

ユーザーをアプリにサインインさせると、必要なアクセス許可の承認をユーザーに依頼する前に、管理者が所属する組織を識別できます。 必須ではありませんが、組織のユーザーに向けたより直観的なエクスペリエンスの作成に役立ちます。 ユーザーのサインインを行うには、 [v2.0 プロトコル チュートリアル](active-directory-v2-protocols.md)に従ってください。

### <a name="request-the-permissions-from-a-directory-admin"></a>ディレクトリ管理者にアクセス許可を要求する
組織の管理者にアクセス許可を要求する準備ができたら、ユーザーを v2.0 *管理者の同意エンドポイント*にリダイレクトできます。

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| パラメーター | 条件 | 説明 |
| --- | --- | --- |
| テナント |必須 |アクセス許可を要求するディレクトリ テナント。 GUID またはフレンドリ名の形式で指定できます。 |
| client_id |必須 |[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)によってアプリに割り当てられたアプリケーション ID。 |
| redirect_uri |必須 |処理するアプリの応答の送信先となるリダイレクト URI。 アプリケーション登録ポータルで登録したリダイレクト URI のいずれかと完全に一致させる必要があります。 |
| state |推奨 |要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用します。 |

現在 Azure AD では、テナント管理者がサインインして、要求を完了する必要があります。 管理者はユーザーがアプリケーション登録ポータルで要求したすべてのアクセス許可への承認を求められます。

#### <a name="successful-response"></a>成功応答
管理者がアプリにアクセス許可を承認すると、成功応答は次のようになります。

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| パラメーター | Description |
| --- | --- | --- |
| テナント |アプリケーションが要求したアクセス許可を GUID 形式で付与するディレクトリ テナント。 |
| state |要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。 |
| admin_consent |**true** に設定されます。 |

#### <a name="error-response"></a>エラー応答
管理者がアプリにアクセス許可を承認しない場合、失敗した応答は次のようになります。

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| パラメーター | Description |
| --- | --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

管理者の同意エンドポイントから成功応答を受信した後で、アプリは要求したアクセス許可を獲得しています。 次に、必要なリソースのトークンを要求できます。

## <a name="using-permissions"></a>アクセス許可の使用
ユーザーがアプリのアクセス許可に同意したら、アプリは一定範囲でリソースにアクセスするためのアプリのアクセス許可を表すアクセス トークンを取得できます。 アクセス トークンは、1 つのリソースだけで使用できますが、アクセス トークンの内部には、そのリソースに関してアプリに付与されたすべてのアクセス許可がエンコードされます。 アクセス トークンを取得するために、アプリは v2.0 トークン エンドポイントに対して、次のような要求を作成できます。

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

リソースへの HTTP 要求では、取得したアクセス トークンを使用できます。 アクセス トークンはリソースに対して、特定のタスクを実行する適切なアクセス許可がアプリにあることを確実に示すことができます。  

OAuth 2.0 プロトコルとアクセス トークンの取得方法の詳細については、[v2.0 エンドポイント プロトコルのリファレンス](active-directory-v2-protocols.md)を参照してください。



<!--HONumber=Jan17_HO3-->


