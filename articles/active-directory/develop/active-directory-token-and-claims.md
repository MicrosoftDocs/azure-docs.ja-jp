---
title: "Azure AD でサポートされているさまざまなトークンと要求の種類について | Microsoft Docs"
description: "Azure Active Directory (AAD) によって発行された SAML 2.0 トークンおよび JSON Web トークン (JWT) のクレームを、理解および評価するためのガイド"
documentationcenter: na
author: bryanla
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 83bb2090d3a2fbd4fabdcd660c72590557cfcafc
ms.openlocfilehash: 46702abb229ba0a6512f336cb0aa4e4a75b51771
ms.lasthandoff: 02/18/2017


---
# <a name="azure-ad-token-reference"></a>Azure AD のトークン リファレンス
Azure Active Directory (Azure AD) は、各認証フローを処理する際に、複数の種類のセキュリティ トークンを出力します。 このドキュメントでは、各トークンの種類の形式、セキュリティ特性、内容について説明します。

## <a name="types-of-tokens"></a>トークンの種類
Azure AD は [OAuth 2.0 承認プロトコル](active-directory-protocols-oauth-code.md)をサポートしており、access_token と refresh_token の両方を利用します。  また、[OpenID Connect](active-directory-protocols-openid-connect-code.md) による認証とサインインもサポートしており、これによって&3; 種類目のトークンである id_token が導入されます。  これらの各トークンは、「ベアラー トークン」として表されます。

ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。 この意味で、"ベアラー" はトークンを提示できる任意の利用者を表します。 ベアラー トークンを受信するには Azure AD による認証が必要となりますが、意図しない利用者による傍受を防ぐために、トークンをセキュリティで保護する対策を講じる必要があります。 ベアラー トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがないため、トランスポート層セキュリティ (HTTPS) などのセキュリティで保護されたチャネルで転送する必要があります。 ベアラー トークンが暗号化されずに転送された場合、中間者攻撃によってトークンが取得され、保護されたリソースに不正アクセスされる可能性があります。 後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。 アプリケーションでは、常に安全な方法でベアラー トークンを転送および保存してください。 ベアラー トークンのセキュリティに関する考慮事項の詳細については、 [RFC 6750 セクション 5](http://tools.ietf.org/html/rfc6750)をご覧ください。

Azure AD によって発行されるトークンの多くは、JSON Web トークン (JWT) として実装されます。  JWT は、2 つのパーティ間で情報を転送する、コンパクトで URL の安全な手段です。  JWT に含まれる情報は「要求」と呼ばれ、トークンのベアラーとサブジェクトに関する情報のアサーションです。  JWT の要求は、伝送用にエンコードおよびシリアル化された JSON オブジェクトです。  Azure AD によって発行される JWT は署名されますが、暗号化されないため、デバッグの目的で JWT の内容を簡単に検査できます。  そのためには、 [jwt.calebb.net](http://jwt.calebb.net)などの複数のツールを利用できます。 JWT の詳細については、 [JWT の仕様](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)を参照してください。

## <a name="idtokens"></a>Id_tokens
id_token は、アプリが [OpenID Connect](active-directory-protocols-openid-connect-code.md) を使用して認証を実行すると受け取るサインイン セキュリティ トークンの形式です。  [JWT](#types-of-tokens) として表され、ユーザーがアプリに署名するために使用できる要求が含まれます。  必要に応じて id_token で要求を使用できます。一般には、アカウント情報の表示や、アプリ内でのアクセス制御の決定に使用されます。

id_token は署名されますが、この時点では暗号化されません。  アプリは、id_token を受け取ったら、[署名を検証](#validating-tokens)してトークンの信頼性を確認し、要求を検証してトークンの有効性を確認する必要があります。  アプリで検証する要求はシナリオの要件によって異なりますが、すべてのシナリオでアプリが実行する必要がある [共通の要求検証](#validating-tokens) がいくつかあります。

id_token の要求、およびサンプル id_token については、次のセクションを参照してください。  id_token 内のクレームは特定の順序では返されないことに注意してください。  さらに、随時、新しいクレームが id_token に導入される可能性があります。アプリは、新しいクレームが導入されても問題ないようにする必要があります。  次の一覧の要求は、この記事の執筆時点で、アプリで解釈できることが保証されているものです。  必要な場合は、[OpenID Connect の仕様](http://openid.net/specs/openid-connect-core-1_0.html)でさらに詳細な情報を参照できます。

#### <a name="sample-idtoken"></a>id_token のサンプル
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> 試しに、サンプル id_token を [calebb.net](http://jwt.calebb.net) に貼り付けて、その中の要求を調べてみてください。
> 
> 

#### <a name="claims-in-idtokens"></a>id_token 内の要求
> [!div class="mx-codeBreakAll"]
| JWT の要求 | 名前 | Description |
| --- | --- | --- |
| `appid` |アプリケーション ID |トークンを使用してリソースにアクセスするアプリケーションを識別します。 アプリケーションとして識別することもできますが、アプリケーションを使用しているユーザーとして識別することもできます。 アプリケーション ID は通常、アプリケーション オブジェクトを表しますが、Azure AD 内のサービス プリンシパル オブジェクトを表すこともできます。 <br><br> **JWT 値の例**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud` |対象となる読者 |トークンの対象となる受信者。 トークンを受信するアプリケーションは、対象ユーザーの値が正しいことを検証し、異なる対象ユーザー向けのトークンをすべて拒否する必要があります。 <br><br> **SAML 値の例**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **JWT 値の例**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |アプリケーションの認証コンテキスト クラスの参照 |クライアントが認証された方法を示します。 パブリック クライアントの場合、値は 0 です。 クライアント ID とクライアント シークレットが使用されている場合、値は 1 です。 <br><br> **JWT 値の例**: <br> `"appidacr": "0"` |
| `acr` |認証コンテキスト クラスの参照 |アプリケーションの認証コンテキスト クラスの参照要求のクライアントとは異なり、サブジェクトが認証された方法を示します。 値 「0」 は、エンドユーザーの認証が ISO/IEC 29115 の要件を満たしていないことを示します。 <br><br> **JWT 値の例**: <br> `"acr": "0"` |
| 認証のインスタント |認証が行われた日時を記録します。 <br><br> **SAML 値の例**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |認証方法 |トークンのサブジェクトが認証された方法を示します。 <br><br> **SAML 値の例**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **JWT 値の例**: `“amr”: ["pwd"]` |
| `given_name` |名 |Azure AD ユーザー オブジェクトに設定されたユーザーの名を示します。 <br><br> **SAML 値の例**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **JWT 値の例**: <br> `"given_name": "Frank"` |
| `groups` |グループ |サブジェクトのグループ メンバーシップを表すオブジェクト ID です。 これらの値は一意 (「オブジェクト ID」を参照) であり、アクセスの管理 (リソースへのアクセスを承認するなど) に安全に使用できます。 グループ クレームに含まれるグループは、アプリケーションごとに、アプリケーション マニフェストの ”groupMembershipClaims” プロパティを介して構成されます。 値が null の場合はすべてのグループが除外され、値が ”SecurityGroup” の場合は Active Directory セキュリティ グループのメンバーシップのみが含まれ、値が ”All” の場合はセキュリティ グループと Office 365 配布リストの両方が含まれます。 <br><br> **SAML 値の例**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **JWT 値の例**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` |ID プロバイダー |トークンのサブジェクトを認証した ID プロバイダーを記録します。 この値は、ユーザー アカウントが発行者とは異なるテナントにある場合を除いて、発行者クレームの値と同じです。 <br><br> **SAML 値の例**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **JWT 値の例**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |トークンが発行された時刻が格納されます。 このクレームは、トークンの鮮度を測定するためによく使用されます。 <br><br> **SAML 値の例**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **JWT 値の例**: <br> `"iat": 1390234181` |
| `iss` |発行者 |トークンを構築して返す Security Token Service (STS) を識別します。 Azure AD が返すトークンでは、発行者は sts.windows.net です。 発行者クレーム値の GUID は、Azure AD ディレクトリのテナント ID です。 テナント ID は、変更不可で信頼性の高いディレクトリの識別子です。 <br><br> **SAML 値の例**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **JWT 値の例**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |姓 |Azure AD ユーザー オブジェクトで定義されたユーザーの姓や名字を示します。 <br><br> **SAML 値の例**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **JWT 値の例**: <br> `"family_name": "Miller"` |
| `unique_name` |名前 |トークンのサブジェクトを識別する、人が判読できる値を提供します。 この値は、テナント内で一意であるとは限らず、表示目的でのみ使用されます。 <br><br> **SAML 値の例**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **JWT 値の例**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |オブジェクト ID |Azure AD 内のオブジェクトの一意の識別子が含まれています。 この値は変更不可で、再割り当ても再利用もできません。 オブジェクト ID を使用すると、Azure AD へのクエリ内のオブジェクトを識別できます。 <br><br> **SAML 値の例**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **JWT 値の例**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |ロール |グループ メンバーシップを通じて直接的および間接的にサブジェクトに付与されており、ロールベースのアクセス制御を適用するために使用できるすべてのアプリケーション ロールを表します。 アプリケーション ロールは、アプリケーションごとに、アプリケーション マニフェストの `appRoles` プロパティを介して定義されます。 各アプリケーション ロールの `value` プロパティは、ロールの要求内に表示される値です。 <br><br> **SAML 値の例**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **JWT 値の例**: <br> `“roles”: ["Admin", … ]` |
| `scp` |スコープ |クライアント アプリケーションに付与される偽装アクセス許可を示します。 既定のアクセス許可は `user_impersonation`です。 保護されたリソースの所有者は、別の値を Azure AD に登録できます。 <br><br> **JWT 値の例**: <br> `"scp": "user_impersonation"` |
| `sub` |[件名] |トークンが情報をアサートするプリンシパルを示します (アプリケーションのユーザーなど)。 この値は変更不可で、再割り当ても再利用もできません。したがってこの値を使用すると、安全に承認チェックができます。 サブジェクトは、Azure AD が発行するトークン内に常に存在するため、汎用性のある承認システムでこの値を使用することをお勧めします。 <br> `SubjectConfirmation` は要求ではありません。 これは、トークンのサブジェクトの検証方法を示します。 `Bearer` は、トークンを所有していることでサブジェクトが確認されることを示します。 <br><br> **SAML 値の例**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **JWT 値の例**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |テナント ID |トークンを発行したディレクトリ テナントを識別する、変更不可で、再利用できない識別子です。 この値を使用すると、マルチ テナント アプリケーションのテナント固有のディレクトリ リソースにアクセスできます。 たとえば、この値を使用すると、Graph API への呼び出しでテナントを識別できます。 <br><br> **SAML 値の例**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **JWT 値の例**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`、`exp` |トークンの有効期間 |トークンが有効である期間を定義します。 トークンを検証するサービスは、現在の日付がトークンの有効期間内にあることを確認し、有効期限内にない場合は、トークンを拒否する必要があります。 サービスでは、Azure AD とサービスの間のクロック タイムの違い (「時間のずれ」) を考慮して、トークンの有効期間の範囲を最大&5; 分まで延長する場合があります。 <br><br> **SAML 値の例**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **JWT 値の例**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |ユーザー プリンシパル名 |ユーザー プリンシパルのユーザー名が格納されます。<br><br> **JWT 値の例**: <br> `"upn": frankm@contoso.com` |
| `ver` |バージョン |トークンのバージョン番号が格納されます。 <br><br> **JWT 値の例**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>アクセス トークン
認証が成功すると、Azure AD はアクセス トークンを返します。アクセス トークンを使用すると、保護されたリソースにアクセスできます。 アクセス トークンは Base64 でエンコードされた JSON Web トークン (JWT) であり、デコーダーを使用して実行することでその内容を検査できます。

アプリがアクセス トークンのみを "*使用して*" API へのアクセスを取得する場合は、アクセス トークンを完全に不透明として処理できます (そうする必要があります)。アクセス トークンは、アプリが HTTP 要求内のリソースに渡すことができる単なる文字列です。

アクセス トークンを要求すると、Azure AD はアプリで使用できるようにアクセス トークンに関するメタデータも返します。  この情報には、アクセス トークンの有効期限や有効な範囲が含まれます。  これにより、アプリはアクセス トークン自体を解析しなくても、アクセス トークンのインテリジェントなキャッシュを実行できます。

アプリが Azure AD によって保護される APIであり、HTTP 要求内のアクセス トークンを予期している場合は、受信するトークンの検証と検査を実行する必要があります。 アプリでは、アクセス トークンを使用してリソースにアクセスする前にアクセス トークンの検証を実行する必要があります。 検証の詳細については、「[トークンの検証](#validating-tokens)」をご覧ください。  
これを .NET で実行する方法の詳細については、「[Azure AD からのベアラー トークンの使用による Web API の保護](active-directory-devquickstarts-webapi-dotnet.md)」を参照してください。

## <a name="refresh-tokens"></a>更新トークン

更新トークンは、OAuth 2.0 のフローで新しいアクセス トークンを取得するためにアプリで使用できるセキュリティ トークンです。  ユーザーが介入しなくても、アプリはユーザーに代わってリソースへの長期的なアクセスを実現できます。

更新トークンはマルチリソースです。  つまり、あるリソースに対するトークン要求の間に受け取った更新トークンを、まったく異なるリソースに対するアクセス トークンに使用できます。 このために、要求内の `resource` パラメーターを対象のリソースに設定します。

更新トークンは、アプリに対して完全に非透過的です。 有効期間は長いですが、アプリを作成するときに更新トークンが一定期間残っているものと期待することはできません。  更新トークンは、いつでもさまざまな理由で無効になる可能性があります。  アプリで更新トークンが有効かどうかを把握するための唯一の方法は、Azure AD トークン エンドポイントに対してトークン要求を行って更新トークンを利用することです。

新しいアクセス トークンに対して更新トークンを利用すると、トークン応答で新しい更新トークンが返されます。  新しく発行された更新トークンを保存し、要求で使用したものと置き換える必要があります。  これにより、可能な限り長く更新トークンが有効であることが保証されます。

## <a name="validating-tokens"></a>トークンの検証

id_token または access_token を検証するには、アプリはトークンの署名とクレームの両方を検証する必要があります。 アクセス トークンを検証するには、発行者、対象ユーザー、および署名トークンをアプリで検証する必要もあります。 これらの検証は、OpenID 探索ドキュメント内の値に対して行ってください。 たとえば、テナントに依存しないバージョンのドキュメントは [https://login.windows.net/common/.well-known/openid-configuration](https://login.windows.net/common/.well-known/openid-configuration) にあります。 Azure AD ミドルウェアにはアクセス トークンを検証するための機能が組み込まれており、選択した言語の[サンプル](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-code-samples)を参照できます。 JWT トークンを明示的に検証する方法の詳細については、[手動による JWT の検証のサンプル](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation)を参照してください。  

トークンの検証を簡単に処理する方法を示すライブラリとコード サンプルが提供されています。以下の情報は、基になるプロセスを理解したい開発者だけを対象としたものです。  JWT の検証に使用できるサード パーティ製オープン ソース ライブラリも複数あります。ほとんどすべてのプラットフォームと言語に対して少なくとも&1; つのライブラリがあります。 Azure AD 認証ライブラリとコード サンプルの詳細については、「[Azure Active Directory 認証ライブラリ](active-directory-authentication-libraries.md)」を参照してください。

#### <a name="validating-the-signature"></a>署名の検証

JWT には&3; つのセグメントがあり、 `.` 文字で区切られています。  1 番目のセグメントは**ヘッダー**、2 番目は**本文**、3 番目は**署名**と呼ばれます。  署名セグメントを使用してトークンの信頼性を検証し、アプリで信頼できることを確認できます。

Azure AD によって発行されるトークンは、RSA 256 などの業界標準の非対称暗号アルゴリズムを使用して署名されます。 JWT のヘッダーには、トークンの署名に使用されたキーと暗号方法に関する情報が含まれます。

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

`alg` 要求はトークンへの署名に使用されたアルゴリズムを示し、`x5t` 要求はトークンへの署名に使用された特定の公開キーを示します。

いつでも、Azure AD は公開/秘密キー ペアの特定セットのいずれかを使用して、id_token に署名できます。 Azure AD は定期的に使用可能なキー セットをローテーションするので、このキー変更を自動的に処理するようにアプリを作成する必要があります。  Azure AD によって使用される公開キーの更新を確認する適切な頻度は、24 時間間隔です。

署名の検証に必要な署名キー データは、次の場所にある OpenID Connect メタデータのドキュメントを使用して入手できます。

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> ブラウザーでこの URL をお試してみてください!
> 
> 

このメタデータ ドキュメントは、OpenID Connect 認証の実行に必要なさまざまなエンドポイントの場所など、役に立つ情報を含む JSON オブジェクトです。  

また、トークンの署名に使用される公開キーのセットの場所を示す `jwks_uri`も含まれます。  `jwks_uri` にある JSON ドキュメントには、特定の時点で使用されているすべての公開キー情報が含まれます。  アプリでは、 `kid` 要求を JWT ヘッダーで使用して、特定のトークンの署名に使用されたこのドキュメント内の公開キーを選択できます。  その後、正しい公開キーと指定されたアルゴリズムを使用して、署名の検証を実行できます。

署名の検証の実行は、このドキュメントの対象範囲外です。必要な場合は、役に立つオープン ソース ライブラリが数多く提供されています。

#### <a name="validating-the-claims"></a>要求を検証する

トークン (ユーザ―のサインイン時の id_token 、または HTTP 要求内のべアラー トークンとしてのアクセス トークンのいずれか) を受信したアプリは、トークン内のクレームをチェックする必要があります。  これらには次が含まれますが、これらに限定されるものではありません。

* **受信者**クレーム - トークンがそのアプリに与えられるものであることを検証します。
* **期間の開始時刻**クレームと**期限切れ日時**クレーム - トークンが期限切れでないことを検証します。
* **発行者** クレーム - トークンが Azure AD によってそのアプリに対して発行されたことを検証します。
* **Nonce** - トークン リプレイ攻撃を緩和するために検証します。
* その他にも用途はあります。

アプリが ID トークンに対して実行する必要があるクレーム検証の完全な一覧については、「[OpenID Connect specification (OpenID Connect の仕様)](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)」を参照してください。 これらの要求に対して予期される値の詳細については、前の [id_token](#id-tokens) のセクションを参照してください。

## <a name="sample-tokens"></a>トークンのサンプル

このセクションでは、Azure AD が返す SAML トークンと JWT トークンのサンプルを示します。 これらのサンプルでは、コンテキスト内のクレームを確認できます。

### <a name="saml-token"></a>SAML トークン

以下に示すのは、一般的な SAML トークンのサンプルです。

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>JWT トークン - ユーザーの権限借用
以下に示すのは、承認コード付与フローで使用される一般的な JSON Web トークン (JWT) のサンプルです。
要求に加えて、トークンには **ver** と **appidacr** で表されるバージョン番号と認証コンテキスト クラス参照 (クライアントが認証された方法を示す) が含まれます。 パブリック クライアントの場合、値は 0 です。 クライアント ID またはクライアント シークレットが使用されている場合、値は 1 です。

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>関連コンテンツ
* Azure AD Graph API を使用したトークンの有効期間ポリシーの管理の詳細については、Azure AD Graph の[ポリシー操作](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)および[ポリシー エンティティ](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)を参照してください。
* PowerShell コマンドレットを使用したポリシー管理の詳細およびサンプルについては、「[Configurable token lifetimes in Azure AD](../active-directory-configurable-token-lifetimes.md) (Azure AD で構成可能なトークンの有効期間)」を参照してください。 

