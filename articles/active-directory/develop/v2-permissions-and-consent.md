---
title: Microsoft ID プラットフォームのスコープ、アクセス許可、および同意 | Microsoft Docs
description: スコープ、アクセス許可、同意など、Microsoft ID プラットフォーム エンドポイントでの承認の説明。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 032cc0edaa140d82124a7369232cb82bf6c00c10
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702706"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Microsoft ID プラットフォーム エンドポイントでのアクセス許可と同意

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft ID プラットフォームと統合するアプリケーションは、データにアクセスする方法をユーザーと管理者が制御できるようにする承認モデルに従います。 この承認モデルの実装が Microsoft ID プラットフォーム エンドポイントで更新されて、アプリが Microsoft ID プラットフォームとやり取りする方法が変わりました。 この記事では、スコープ、アクセス許可、同意など、この承認モデルの基本的な概念について説明します。

> [!NOTE]
> Microsoft ID プラットフォーム エンドポイントでは、すべてのシナリオや機能がサポートされているわけではありません。 Microsoft ID プラットフォームのエンドポイントを使用する必要があるかどうかを判断するには、[Microsoft ID プラットフォームの制限事項](active-directory-v2-limitations.md)に関する記事を参照してください。

## <a name="scopes-and-permissions"></a>スコープとアクセス許可

Microsoft ID プラットフォームでは、[OAuth 2.0](active-directory-v2-protocols.md) 承認プロトコルが実装されています。 OAuth 2.0 は、ユーザーに代わってサードパーティのアプリが Web でホストされるリソースにアクセスできる方法です。 Microsoft ID プラットフォームと統合される、Web でホストされるすべてのリソースは、リソース識別子つまり "*アプリケーション ID URI*" を持っています。 Microsoft の Web でホストされるリソースには、次のようなものがあります。

* Microsoft Graph: `https://graph.microsoft.com`
* Office 365 メール API: `https://outlook.office.com`
* Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> Azure AD Graph、Office 365 メール API などではなく、Microsoft Graph を使用することを強くお勧めします。

Microsoft ID プラットフォームと統合されたサードパーティのリソースも同様です。 これらのリソースのいずれでも、機能をより小さいまとまりに分割するために使用できるアクセス許可のセットを定義できます。 たとえば、[Microsoft Graph](https://graph.microsoft.com) では、特に次のタスクを実行するアクセス許可が定義されています。

* ユーザーの予定表の読み取り
* ユーザーの予定表への書き込み
* ユーザーとしてのメールの送信

これらの種類のアクセス許可を定義することで、リソースでは、データと、API 機能を公開する方法を、きめ細かく制御できます。 サード パーティのアプリでは、ユーザーや管理者にこれらのアクセス許可を要求でき、ユーザーや管理者が承認してからでなければ、アプリはデータにアクセスしたり、ユーザーの代理として動作したりできません。 リソースの機能を細かいアクセス許可セットにまとめることによって、サードパーティのアプリは、機能を実行するために必要な特定のアクセス許可のみを要求するように構築できます。 ユーザーや管理者は、アプリがアクセスできるデータを正確に知ることができ、アプリが悪意のある動作をしていないことを確信できます。 開発者は常に最小限の特権の概念に従って、アプリケーションが機能するために必要なアクセス許可のみを要求する必要があります。

OAuth 2.0 では、これらの種類のアクセス許可は "*スコープ*" と呼ばれます。 "*アクセス許可*" と呼ばれることもよくあります。 アクセス許可は、Microsoft ID プラットフォームでは文字列値として表現されます。 Microsoft Graph の例では、各アクセス許可の文字列値は次のようになります。

* `Calendars.Read` を使用したユーザーの予定表の読み取り
* `Calendars.ReadWrite` を使用したユーザーの予定表への書き込み
* `Mail.Send` を使用したユーザーとしてのメールの送信

アプリでは、通常、Microsoft ID プラットフォーム承認エンドポイントへの要求でスコープを指定することにより、これらのアクセス許可を要求します。 ただし、特定の高い特権アクセス許可は、管理者が同意することによってのみ付与することができ、[管理者同意エンドポイント](v2-permissions-and-consent.md#admin-restricted-permissions)を使用して要求/付与できます。 詳細については、後の説明を参照してください。

## <a name="permission-types"></a>アクセス許可の種類

Microsoft ID プラットフォームでは、**委任されたアクセス許可**と**アプリケーションのアクセス許可**という 2 種類のアクセス許可がサポートされています。

* **委任されたアクセス許可**は、サインインしているユーザーが存在するアプリで使用されます。 これらのアプリでは、ユーザーまたは管理者がアプリから要求されたアクセス許可に同意すると、ターゲット リソースの呼び出し時にサインイン ユーザーとして動作するためのアクセス許可がアプリに委任されます。 一部の委任されたアクセス許可には管理者以外のユーザーでも同意できますが、一部の高い特権のアクセス許可では[管理者の同意](v2-permissions-and-consent.md#admin-restricted-permissions)が必要です。 委任されたアクセス許可に同意できる管理者ロールについては、「[Azure AD での管理者ロールのアクセス許可](../users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

* **アプリケーションのアクセス許可**は、サインインしているユーザーが存在しない状態で実行されるアプリ (バックグラウンド サービスまたはデーモンとして実行されるアプリなど) で使用されます。  アプリケーションのアクセス許可には、[管理者だけが同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)できます。

"_有効なアクセス許可_" は、アプリがターゲット リソースに要求を行うときに付与されるアクセス許可です。 アプリに付与される委任されたアクセス許可とアプリケーションのアクセス許可の違い、およびターゲット リソースへの呼び出しを行うときの有効なアクセス許可について理解しておくことが重要です。

- 委任されたアクセス許可の場合、アプリの有効な "_アクセス許可_" は、(同意によって) アプリに付与されている委任されたアクセス許可と現在サインインしているユーザーの特権の共通部分の最小特権になります。 サインインしているユーザーよりも多くの特権をアプリに付与することはできません。 組織内では、サインインしているユーザーの特権は、ポリシーによって決定することも、1 つ以上の管理者ロールのメンバーシップによって決定することもできます。 委任されたアクセス許可に同意できる管理者ロールについては、「[Azure AD での管理者ロールのアクセス許可](../users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

   たとえば、委任されたアクセス許可として _User.ReadWrite.All_ がアプリに付与されているものとします。 通常、このアクセス許可は、組織内のすべてのユーザーのプロファイルを読み取り、更新する権限をアプリに付与します。 サインインしているユーザーが全体管理者の場合、アプリは組織内のすべてのユーザーのプロファイルを更新できます。 ただし、サインインしているユーザーが管理者ロールに属していない場合、アプリが更新できるのは、サインインしているユーザーのプロファイルだけになります。 アプリにはユーザーの代理で動作するためのアクセス許可が付与されていますが、そのユーザーに組織内の他のユーザーのプロファイルを更新する権限がないため、アプリがこれを実行することはできません。
  
- アプリケーションのアクセス許可の場合、アプリの "_有効なアクセス許可_" は、そのアクセス許可が暗示する完全なレベルの権限になります。 たとえば、アプリケーションのアクセス許可として _User.ReadWrite.All_ が付与されているアプリは、組織内のすべてのユーザーのプロファイルを更新できます。 

## <a name="openid-connect-scopes"></a>OpenID Connect のスコープ

OpenID Connect の Microsoft ID プラットフォーム実装には、特定のリソースには適用されない、適切に定義されたスコープ `openid`、`email`、`profile`、`offline_access` があります。 `address` と `phone` の OpenID Connect スコープはサポートされていません。

### <a name="openid"></a>openid

アプリは、[OpenID Connect](active-directory-v2-protocols.md) を使用してサインインを行う場合、`openid` スコープを要求する必要があります。 `openid` スコープは、職場アカウントの同意ページでは "サインイン" アクセス許可として表示され、個人用 Microsoft アカウントの同意ページでは "プロフィールの表示と、Microsoft アカウントを使うアプリとサービスへの接続" アクセス許可として表示されます。 このアクセス許可により、`sub` 要求の形式でユーザーの一意の識別子をアプリが受け取ることができます。 アプリが UserInfo エンドポイントにアクセスすることも可能にします。 `openid` スコープは、Microsoft ID プラットフォーム トークン エンドポイントで ID トークンを取得するために使用できます。このトークンは、アプリが認証目的で使用できます。

### <a name="email"></a>email

`email` スコープは `openid` スコープやその他のスコープと共に使用できます。 これにより、アプリがユーザーのプライマリ電子メール アドレスに `email` 要求の形式でアクセスできます。 電子メール アドレスがユーザー アカウントと関連付けられている場合のみ (常にではありません)、`email` 要求はトークンに含まれます。 `email` スコープを使用する場合は、トークンに `email` 要求が存在しないケースにも対応できるようにアプリを準備する必要があります。

### <a name="profile"></a>profile

`profile` スコープは `openid` スコープやその他のスコープと共に使用できます。 これにより、アプリはユーザーの多くの情報にアクセスできます。 アプリがアクセスできる情報には、ユーザーの名、姓、希望するユーザー名、オブジェクト ID などがありますが、これらに限定されるものではありません。 特定のユーザーに対して id_tokens パラメーターで使用できるプロファイル要求の完全な一覧については、[`id_tokens` のリファレンス](id-tokens.md)をご覧ください。

### <a name="offlineaccess"></a>offline_access

[ `offline_access`スコープ](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)を使用すると、アプリはユーザーの代わりに、長期間にわたってリソースにアクセスできます。 同意ページで、このスコープは、"アクセス権を与えたデータへのアクセスを管理する" アクセス許可として表示されます。 ユーザーが `offline_access` スコープを承認すると、アプリは Microsoft ID プラットフォーム トークン エンドポイントから更新トークンを取得できます。 更新トークンの有効期間は長期です。 アプリは、古いアクセス トークンの有効期限が切れると、新しいアクセス トークンを取得できます。

アプリは、`offline_access` スコープを明示的に要求しない場合、更新トークンを受け取ることはありません。 つまり、[OAuth 2.0 承認コード フロー](active-directory-v2-protocols.md)の承認コードを使用すると、`/token` エンドポイントからアクセス トークンだけが取得されます。 アクセス トークンの有効期間は短期です。 アクセス トークンは、通常、1 時間以内に期限切れとなります。 その時点で、アプリはユーザーを `/authorize` エンドポイントにリダイレクトして、新しい承認コードを取得する必要があります。 このリダイレクト中に、アプリの種類によっては、ユーザーが資格情報を再入力したり、アクセス許可に再同意したりする必要がある場合もあります。 `offline_access` スコープはサーバーによって自動的に要求されますが、クライアントは更新トークンを受け取るためにスコープを要求する必要があります。

更新トークンの取得方法と使用方法の詳細については、[Microsoft ID プラットフォーム プロトコルのリファレンス](active-directory-v2-protocols.md)を参照してください。

## <a name="requesting-individual-user-consent"></a>個々のユーザーの同意を要求する

[OpenID Connect または OAuth 2.0](active-directory-v2-protocols.md) 承認要求では、アプリは `scope` クエリ パラメーターを使用して、必要なアクセス許可を要求できます。 たとえば、ユーザーがアプリにサインインするときに、アプリは次のような要求を送信します (読みやすくするために、改行を追加しています)。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` パラメーターは、アプリが要求している委任されたアクセス許可の、空白文字で区切られた一覧です。 各アクセス許可は、リソースの識別子 (アプリケーション ID URI) にアクセス許可の値を追加することによって示されます。 上の要求では、ユーザーの予定表を読み取り、ユーザーとしてメールを送信するためのアクセス許可をアプリが必要としています。

ユーザーが資格情報を入力すると、Microsoft ID プラットフォーム エンドポイントは、*ユーザーの同意*の一致するレコードがないか確認します。 ユーザーが過去に要求されたどのアクセス許可にも同意しておらず、管理者も組織全体の代わりにそれらのアクセス許可に同意していない場合、Microsoft ID プラットフォーム エンドポイントは要求されたアクセス許可の付与をユーザーに求めます。

> [!NOTE]
> この時点で、`offline_access` ("アクセス権を与えたデータへのアクセスを管理する") と `user.read` ("サインインとプロファイルの読み取り") のアクセス許可が、アプリケーションへの初期同意に自動的に組み込まれます。  一般に、これらのアクセス許可は、適切なアプリの機能に必要です。`offline_access` は、ネイティブおよび Web のアプリに不可欠の更新トークンへのアクセスをアプリに提供し、`user.read` は、クライアントまたはアプリが長期間にわたってユーザーを適切に識別し、基本的なユーザー情報にアクセスできるようにする `sub` 要求へのアクセスを提供します。  

![作業アカウントの同意を示すスクリーンショットの例](./media/v2-permissions-and-consent/work_account_consent.png)

ユーザーがアクセス許可の要求を承認すると、同意が記録されるので、ユーザーはそれ以降にアカウントにサインインするときに再度同意する必要はありません。

## <a name="requesting-consent-for-an-entire-tenant"></a>テナント全体の同意を要求する

多くの場合、組織がアプリケーションのライセンスまたはサブスクリプションを購入している場合は、組織のすべてのメンバーが使用できるようにアプリケーションを事前にセットアップすることが望まれます。 このプロセスの一環として、管理者は、テナントのユーザーの代理としてアプリケーションへの同意を与えることができます。 管理者がテナント全体に対する同意を与えると、組織のユーザーにはアプリケーションの同意ページは表示されません。

テナントのユーザー全員に対して委任されたアクセス許可の同意を要求するには、アプリで管理者の同意エンドポイントを使用できます。

さらに、アプリケーションでは、管理者の同意エンドポイントを使用してアプリケーションのアクセス許可を要求する必要があります。

## <a name="admin-restricted-permissions"></a>管理者によって制限されるアクセス許可

Microsoft のエコシステムにおける高い権限には、*管理者によって制限されている*とマークされているものもあります。 これらの種類のアクセス許可の例として、次のアクセス許可があります。

* `User.Read.All` を使用してすべてのユーザーの完全なプロファイルを読み取る
* `Directory.ReadWrite.All` を使用した組織のディレクトリへのデータの書き込み
* `Groups.Read.All` を使用して組織のディレクトリ内の全グループを読み取る

コンシューマー ユーザーがこの種類のデータへのアプリケーション アクセスを許可している場合でも、組織のユーザーは会社の同一の機密データ セットへのアクセスを許可することが制限されます。 アプリケーションが組織のユーザーにこれらのアクセス許可のいずれかへのアクセスを要求すると、ユーザーは、アプリのアクセス許可に同意する権限がないという内容のエラー メッセージを受け取ります。

アプリが組織の管理者に制限されるスコープにアクセスする必要がある場合は、次のように管理者の同意エンドポイントを使用して、会社の管理者から直接要求する必要があります。

アプリケーションが高い特権の委任されたアクセス許可を要求していて、管理者の同意エンドポイントを介して管理者がこれらのアクセス許可を付与すると、テナントのユーザー全員に対して同意が付与されます。

アプリケーションがアプリケーションのアクセス許可を要求していて、管理者が管理者の同意エンドポイントを介してこれらのアクセス許可を付与する場合、この許可は特定のユーザーに代わっては行われません。 代わりに、クライアント アプリケーションはアクセス許可を "*直接*" 付与されます。 これらの種類のアクセス許可は、バックグラウンドで実行されるデーモン サービスと他の非対話型アプリケーションでのみ使用されます。

## <a name="using-the-admin-consent-endpoint"></a>管理者の同意エンドポイントを使用する

会社の管理者がアプリケーションを使用していて、承認エンドポイントに送られた場合、Microsoft ID プラットフォームは、ユーザーのロールを検出し、アプリケーションで要求されたアクセス許可に対してテナント全体の代わりに同意するかどうかを管理者に確認します。 ただし、テナント全体に代わって管理者がアクセス許可を付与することを事前に要求する必要がある場合に使用できる、専用の管理者の同意エンドポイントもあります。 アプリケーションのアクセス許可を要求する場合も、このエンドポイントを使用する必要があります (これは、承認エンドポイントでは要求できません)。

これから説明する手順に従うと、管理者に制限されているスコープを含むテナントのユーザー全員のアクセス許可をアプリで要求できます。 これは、高い特権の操作であり、シナリオに必要な場合にのみ行う必要があります。

手順を実装するコード サンプルについては、[管理者に制限されているスコープのサンプル](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)に関するページを参照してください。

### <a name="request-the-permissions-in-the-app-registration-portal"></a>アプリケーション登録ポータルでアクセス許可を要求する

管理者の同意ではスコープ パラメーターは受け付けられないので、要求されるアクセス許可はすべて、アプリケーションの登録で静的に定義されている必要があります。 一般に、特定のアプリケーションに対して静的に定義するアクセス許可を、動的/増分的に将来要求するアクセス許可のスーパーセットにすることが、最善の方法です。

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>アプリケーションに対して静的に要求されるアクセス許可のリストを構成するには

1. [Azure portal - アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)エクスペリエンスでアプリケーションに移動するか、まだ[アプリを作成](quickstart-register-app.md)していない場合は作成します。
2. **[API のアクセス許可]** セクションを見つけ、API のアクセス許可内で [アクセス許可の追加] をクリックします。
3. 使用可能な API の一覧から **[Microsoft Graph]** を選択し、アプリに必要なアクセス許可を追加します。
3. アプリの登録を**保存**します。

### <a name="recommended-sign-the-user-into-your-app"></a>推奨:ユーザーをアプリにサインインさせる

通常、管理者の同意エンドポイントを使用するアプリケーションを構築する場合は、アプリ側に管理者がアプリのアクセス許可を承認できるページやビューが必要です。 このページは、アプリのサインアップ フローやアプリの設定の一部にするか、専用の "接続" フローにすることができます。 多くの場合、職場または学校の Microsoft アカウントでユーザーがサインインした後にのみ、”接続" ビューが表示されます。

ユーザーをアプリにサインインさせるとき、必要なアクセス許可の承認をユーザーに依頼する前に、管理者が所属する組織を識別できます。 必須ではありませんが、組織のユーザーに向けたより直観的なエクスペリエンスの作成に役立ちます。 ユーザーをサインインさせるには、[Microsoft ID プラットフォーム プロトコルのチュートリアル](active-directory-v2-protocols.md)に従ってください。

### <a name="request-the-permissions-from-a-directory-admin"></a>ディレクトリ管理者にアクセス許可を要求する

組織の管理者にアクセス許可を要求する準備ができたら、Microsoft ID プラットフォームの*管理者の同意エンドポイント*にユーザーをリダイレクトできます。

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
| `tenant` | 必須 | アクセス許可を要求するディレクトリ テナント。 GUID またはフレンドリ名の形式で指定できます。または、例で示すように `common` で総称的に参照できます。 |
| `client_id` | 必須 | [Azure portal の [アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) エクスペリエンスでアプリに割り当てられた**アプリケーション (クライアント) ID**。 |
| `redirect_uri` | 必須 |処理するアプリの応答の送信先となるリダイレクト URI。 アプリケーション登録ポータルで登録したリダイレクト URI のいずれかと完全に一致させる必要があります。 |
| `state` | 推奨 | 要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用します。 |

現在 Azure AD では、テナント管理者がサインインして、要求を完了する必要があります。 管理者はユーザーがアプリケーション登録ポータルで要求したすべてのアクセス許可への承認を求められます。

#### <a name="successful-response"></a>成功応答

管理者がアプリにアクセス許可を承認すると、成功応答は次のようになります。

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| パラメーター | 説明 |
| --- | --- |
| `tenant` | アプリケーションが要求したアクセス許可を GUID 形式で付与するディレクトリ テナント。 |
| `state` | 要求に含まれ、かつトークンの応答として返される値。 任意のコンテンツの文字列を指定することができます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。 |
| `admin_consent` | `True`に設定されます。 |

#### <a name="error-response"></a>エラー応答

管理者がアプリにアクセス許可を承認しない場合、失敗した応答は次のようになります。

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| パラメーター | 説明 |
| --- | --- |
| `error` | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| `error_description` | エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

管理者の同意エンドポイントから成功応答を受信した後で、アプリは要求したアクセス許可を獲得しています。 次に、必要なリソースのトークンを要求できます。

## <a name="using-permissions"></a>アクセス許可の使用

ユーザーがアプリのアクセス許可に同意したら、アプリは一定範囲でリソースにアクセスするためのアプリのアクセス許可を表すアクセス トークンを取得できます。 アクセス トークンは、1 つのリソースだけで使用できますが、アクセス トークンの内部には、そのリソースに関してアプリに付与されたすべてのアクセス許可がエンコードされます。 アクセス トークンを取得するために、アプリは Microsoft ID プラットフォーム トークン エンドポイントに対して、次のような要求を行うことができます。

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

OAuth 2.0 プロトコルとアクセス トークンの取得方法の詳細については、[Microsoft ID プラットフォーム エンドポイント プロトコルのリファレンス](active-directory-v2-protocols.md)を参照してください。

## <a name="the-default-scope"></a>/.default スコープ

`/.default` スコープを使用すると、v1.0 エンドポイントから Microsoft ID プラットフォーム エンドポイントにアプリを移行するのに役立ちます。 これは、アプリケーション登録で構成されたアクセス許可の静的一覧を参照するすべてのアプリケーションの組み込みスコープです。 `https://graph.microsoft.com/.default` の `scope` 値は、v1.0 エンドポイント `resource=https://graph.microsoft.com` と機能的に同じです。つまり、これは、アプリケーションが Azure portal で登録した Microsoft Graph のスコープでトークンを要求します。

/.default スコープは、どの OAuth 2.0 フローでも使用できますが、[On-Behalf-Of フロー](v2-oauth2-on-behalf-of-flow.md)と[クライアントの資格情報フロー](v2-oauth2-client-creds-grant-flow.md)では必須です。  

> [!NOTE]
> クライアントは、静的 (`/.default`) と動的の同意を 1 つの要求に結合することはできません。 そうした場合、スコープの種類の結合が原因で `scope=https://graph.microsoft.com/.default+mail.read` はエラーになります。

### <a name="default-and-consent"></a>/.default と同意

`/.default` スコープは、`prompt=consent` に対しても v1.0 エンドポイントの動作をトリガーします。 リソースに関係なく、アプリケーションによって登録されたすべてのアクセス許可に対して同意が要求されます。 要求の一部として含まれている場合、`/.default` スコープは、要求されたリソースのスコープを含むトークンを返します。

### <a name="default-when-the-user-has-already-given-consent"></a>ユーザーが既に同意している場合の /.default

`/.default` は、機能的には `resource` 中心の v1.0 エンドポイントの動作と同じであるため、v1.0 エンドポイントの同意の動作も行います。 つまり、`/.default` は、ユーザーによってクライアントとリソース間のアクセス許可が付与されていない場合にのみ、同意プロンプトをトリガーします。 そのような同意が存在する場合は、ユーザーがそのリソースに対して付与したすべてのスコープを含むトークンが返されます。 ただし、アクセス許可が付与されていない場合や `prompt=consent` パラメーターが指定されている場合は、クライアント アプリケーションによって登録されたすべてのスコープに対して同意プロンプトが表示されます。

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>例 1:ユーザーまたはテナント管理者がアクセス許可を付与している

ユーザー (またはテナント管理者) が、Microsoft Graph のアクセス許可 `mail.read` と `user.read` をクライアントに付与しています。 クライアントが `scope=https://graph.microsoft.com/.default` の要求を行うと、Microsoft Graph に対するクライアント アプリケーションの登録済みアクセス許可の内容に関係なく、同意プロンプトは表示されません。 スコープ `mail.read` と `user.read` を含むトークンが返されます。

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>例 2:ユーザーがクライアントとリソース間のアクセス許可を付与していない

クライアントと Microsoft Graph 間のユーザーの同意は存在しません。 クライアントは `user.read` と `contacts.read` のアクセス許可、および Azure Key Vault スコープ `https://vault.azure.net/user_impersonation` を登録しています。 クライアントが `scope=https://graph.microsoft.com/.default` のトークンを要求すると、`user.read`、`contacts.read`、および Key Vault `user_impersonation` のスコープの同意画面がユーザーに表示されます。 返されるトークンには、`user.read` と `contacts.read` のスコープだけが含まれます。

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>例 3:ユーザーは同意済みで、クライアントが追加のスコープを要求する

ユーザーは、クライアントの `mail.read` に既に同意しています。 クライアントは、登録で `contacts.read` スコープを登録しています。 クライアントが `scope=https://graph.microsoft.com/.default` を使用してトークンを要求し、`prompt=consent` を介して同意を要求すると、アプリケーションによって登録された唯一かつすべてのアクセス許可の同意画面がユーザーに表示されます。 `contacts.read` は同意画面に表示されますが、`mail.read` は表示されません。 返されたトークンは Microsoft Graph 用であり、`mail.read` と `contacts.read` が含まれます。

### <a name="using-the-default-scope-with-the-client"></a>クライアントで /.default スコープを使用する

クライアントがそれ自体の `/.default` スコープを要求するという `/.default` スコープの特殊なケースがあります。 このシナリオを以下の例で説明します。

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

これにより、登録されているすべてのアクセス許可の同意画面が生成され (同意と `/.default` の前述の説明に基づいて適用される場合)、アクセス トークンではなく、id_token が返されます。  この動作は、ADAL から MSAL に移行する特定のレガシー クライアントのために存在するものであり、Microsoft ID プラットフォーム エンドポイントを対象とする新しいクライアントでは使用しないでください。  

## <a name="troubleshooting-permissions-and-consent"></a>アクセス許可と同意のトラブルシューティング

アプリケーションの所有者やユーザーによる同意プロセスの間に予期しないエラーが発生する場合は、こちらの記事「[アプリケーションに同意すると、予期しないエラーが発生する](../manage-apps/application-sign-in-unexpected-user-consent-error.md)」のトラブルシューティング手順をご覧ください。
