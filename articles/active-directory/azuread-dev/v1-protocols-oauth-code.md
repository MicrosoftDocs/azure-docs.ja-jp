---
title: Azure AD での OAuth 2.0 承認コード フローについて
description: この記事では、Azure Active Directory と OAuth 2.0 を利用してテナントの Web アプリケーションと Web API へのアクセスを承認するために HTTP メッセージを使用する方法について説明します。
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 5f987ab15201e4c4dabf147ac468184881e9ed17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85551636"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>OAuth 2.0 コード付与フローを使用して Azure Active Directory Web アプリケーションへアクセスを承認する

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  呼び出す予定のリソースをサーバーに対して指定しないと、サーバーではそのリソースの条件付きアクセス ポリシーがトリガーされません。 このため、MFA をトリガーするには、URL にリソースを含める必要があります。 
>

Azure Active Directory (Azure AD) が OAuth 2.0 を使用することにより、ユーザーは Azure AD テナントの Web アプリケーションと Web API へのアクセスを承認することができます。 本ガイドでは、[オープンソース ライブラリ](active-directory-authentication-libraries.md)を利用せず、HTTP メッセージを送受信する方法について説明します。本ガイドは言語非依存です。

OAuth 2.0 承認コード フローは、 [OAuth 2.0 仕様のセクション 4.1](https://tools.ietf.org/html/rfc6749#section-4.1)で規定されています。 Web アプリやネイティブにインストールされるアプリを含め、ほとんどの種類のアプリで認証と承認を行う際にこのフローが使用されます。

## <a name="register-your-application-with-your-ad-tenant"></a>AD テナントへのアプリケーションの登録
まず、Azure Active Directory (Azure AD) テナントにアプリケーションを登録する必要があります。 これにより、アプリケーションのアプリケーション ID を取得でき、トークンを受信できるようになります。

1. [Azure portal](https://portal.azure.com) にサインインします。
   
1. ページ右上隅にあるアカウントを選択し、 **[ディレクトリの切り替え]** ナビゲーションを選択してから、適切なテナントを選択して、Azure AD テナントを選択します。 
   - アカウントの下の Azure AD テナントが 1 つのみの場合、または適切な Azure AD テナントを既に選択している場合は、この手順をスキップします。
   
1. Azure portal で、 **[Azure Active Directory]** を検索して選択します。
   
1. **[Azure Active Directory]** の左側のメニューで、 **[アプリの登録]** を選択し、 **[新しい登録]** を選択します。
   
1. 画面の指示に従い、新しいアプリケーションを作成します。 このチュートリアルでは、Web アプリケーションであるかパブリック クライアント (モバイルとデスクトップ) アプリケーションであるかは重要ではありませんが、Web アプリケーションまたはパブリック クライアント アプリケーションの具体的な例を確認するには、[クイック スタート](v1-overview.md)をご覧ください。
   
   - **[名前]** はアプリケーションの名前で、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
   - **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
   - **[リダイレクト URI]** を指定します。 Web アプリケーションの場合、これはユーザーのサインイン場所となるアプリのベース URL です。  たとえば、「 `http://localhost:12345` 」のように入力します。 パブリック クライアント (モバイルとデスクトップ) の場合、Azure AD はこれを使用してトークン応答を返します。 アプリケーション固有の値を入力します。  たとえば、「 `http://MyFirstAADApp` 」のように入力します。
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. 登録が完了すると、Azure AD により、アプリケーションに一意のクライアント ID (**アプリケーション ID**) が割り当てられます。 この値は次のセクションで必要になるので、アプリケーション ページからコピーします。
   
1. Azure portal でアプリケーションを見つけるには、 **[アプリの登録]** を選択し、 **[アプリケーションをすべて表示]** を選択します。

## <a name="oauth-20-authorization-flow"></a>OAuth 2.0 承認フロー

まとめると、アプリケーションの全体的な承認フローは次のようになります。

![OAuth Auth Code Flow](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>承認コードを要求する

承認コード フローは、クライアントがユーザーを `/authorize` エンドポイントにリダイレクトさせることから始まります。 この要求で、クライアントは、ユーザーから取得する必要のあるアクセス許可を指定します。 Azure Portal で **[アプリ登録] > [エンドポイント]** を選択して、テナントの OAuth 2.0 承認エンドポイントを取得できます。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| パラメーター | Type | 説明 |
| --- | --- | --- |
| tenant |required |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値はテナント ID です。たとえば、`8eaef023-2b34-4da1-9baa-8bc8c9d6a490`、`contoso.onmicrosoft.com` または `common` (テナント独立のトークンの場合) です |
| client_id |required |Azure AD への登録時にアプリに割り当てられたアプリケーション ID。 これは、Azure Portal で確認できます。 サービス サイドバーで **[Azure Active Directory]** をクリックして、 **[アプリの登録]** をクリックしてアプリケーションを選択します。 |
| response_type |required |承認コード フローでは `code` を指定する必要があります。 |
| redirect_uri |推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。 ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 ネイティブ アプリとモバイル アプリでは、`https://login.microsoftonline.com/common/oauth2/nativeclient` の既定値を使用します。 |
| response_mode |省略可能 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。 `query`、`fragment`、または `form_post` を指定できます。 `query` はリダイレクト URI でクエリ文字列パラメーターとしてコードを提供します。 暗黙的フローを使って ID トークンを要求する場合、[OpenID 仕様](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)で規定された `query` を使用することはできません。コードのみを要求する場合は、`query`、`fragment`、`form_post` のいずれかを使用できます。 `form_post` は、リダイレクト URI に対するコードを含んだ POST を実行します。 コード フローの既定値は `query` です。  |
| state |推奨 |要求に含まれ、トークンの応答としても返される値。 [クロスサイト リクエスト フォージェリ攻撃を防ぐ](https://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。 この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| resource | 推奨 |対象となる Web API のアプリケーション ID/URI (セキュリティで保護されたリソース)。 アプリケーション ID/URI を調べるには、Azure Portal で **[Azure Active Directory]** 、 **[アプリの登録]** の順にクリックして、アプリケーションの **[設定]** ページを開きます。その後、 **[プロパティ]** をクリックします。 `https://graph.microsoft.com` のような外部リソースである場合もあります。 認証またはトークン要求でこれが必要です。 認証プロンプトの回数を少なくするには、認証要求に配置して、ユーザーから同意を受信できるようにします。 |
| scope | **ignored** | v1 Azure AD アプリでは、Azure Portal のアプリケーションの **[設定]** の **[必要なアクセス許可]** で、スコープを静的に構成する必要があります。 |
| prompt |省略可能 |ユーザーとの必要な対話の種類を指定します。<p> 有効な値は次のとおりです。 <p> *login*:再認証を求めるメッセージがユーザーに表示されます。 <p> *select_account*:アカウントの選択を求めるメッセージがユーザーに表示され、シングル サインオンが中断されます。 ユーザーでは、既存サインイン アカウントを選択して、記憶されているアカウントの資格情報を入力、またはまったく別のアカウントを一緒に、使用する可能性があります。 <p> *consent*:ユーザーの同意は得られていますが、更新する必要があります。 同意を求めるメッセージがユーザーに表示されます。 <p> *admin_consent*:組織内のすべてのユーザーを代表して同意するよう求めるメッセージが管理者に表示されます |
| login_hint |省略可能 |ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。 多くのアプリでは、`preferred_username` 要求を使用して以前のサインインからユーザー名を抽出しておき、再認証時にこのパラメーターを使用します。 |
| domain_hint |省略可能 |ユーザーがサインインで使用することになるテナントまたはドメインについてのヒントを指定します。 テナントの登録ドメインが domain_hint の値となります。 テナントがオンプレミスのディレクトリと連動している場合、AAD から、指定されたテナントのフェデレーション サーバーにリダイレクトされます。 |
| code_challenge_method | 推奨    | `code_challenge` パラメーターの `code_verifier` をエンコードするために使用されるメソッド。 `plain` か `S256` のいずれかを指定できます。 除外されていると、`code_challenge` が含まれている場合、`code_challenge` はプレーンテキストであると見なされます。 Azure AAD v1.0 は、`plain` と `S256` の両方をサポートします。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 |
| code_challenge        | 推奨    | ネイティブ クライアントまたはパブリック クライアントからの PKCE (Proof Key for Code Exchange) を使用して、承認コード付与をセキュリティ保護するために使用されます。 `code_challenge_method` が含まれている場合は必須です。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。 |

> [!NOTE]
> ユーザーが組織に所属している場合は、組織の管理者がユーザーに代わって同意または却下するか、あるいはユーザーに同意を許可することができます。 そのユーザーは、管理者が許可した場合のみ承認を行うことができます。
>
>

この時点でユーザーは、資格情報を入力し、Azure Portal のアプリからのアクセス許可に同意するよう要求されます。 ユーザーが認証を行い、同意の許可を与えると、Azure AD は要求で指定されたアプリの `redirect_uri` アドレスにコードとともに応答を返します。

### <a name="successful-response"></a>成功応答
正常な応答は次のようになります。

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| パラメーター | 説明 |
| --- | --- |
| admin_consent |管理者が同意要求プロンプトに同意した場合、値は True です。 |
| code |アプリケーションが要求した承認コード。 アプリケーションは承認コードを使用して、対象リソースのアクセス トークンを要求します。 |
| session_state |現在のユーザー セッションを識別する一意の値。 この値は GUID ですが、検査なしで渡される不透明な値として扱う必要があります。 |
| state |要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。 応答を使用する前に、要求と応答に含まれる state の値が同一であることをアプリケーション側で確認することをお勧めします。 クライアントに対する [クロスサイト リクエスト フォージェリ (CSRF) 攻撃](https://tools.ietf.org/html/rfc6749#section-10.12) を検出するのに役立ちます。 |

### <a name="error-response"></a>エラー応答
アプリケーション側でエラーを適切に処理できるよう、 `redirect_uri` にもエラー応答が送信される場合があります。

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| --- | --- |
| error |「 [OAuth 2.0 Authorization Framework (OAuth 2.0 承認フレームワーク)](https://tools.ietf.org/html/rfc6749)」のセクション 5.2 で定義されているエラー コード値。 次の表で、Azure AD が返すエラー コードについて説明します。 |
| error_description |エラーの詳しい説明。 このメッセージはエンドユーザー向けではありません。 |
| state |state 値は、ランダムに生成された再利用されない値で、クロスサイト リクエスト フォージェリ (CSRF) 攻撃を防ぐために、要求で送信され、応答で返されます。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>承認エンドポイント エラーのエラー コード
次の表で、エラー応答の `error` パラメーターで返される可能性のあるさまざまなエラー コードを説明します。

| エラー コード | 説明 | クライアント側の処理 |
| --- | --- | --- |
| invalid_request |必要なパラメーターが不足しているなどのプロトコル エラーです。 |要求を修正し再送信します。 これは、開発エラーであり、通常は初期テスト中に発生します。 |
| unauthorized_client |クライアント アプリケーションは、認証コードの要求を許可されていません。 |これは通常、クライアント アプリケーションが Azure AD に登録されていない、またはユーザーの Azure AD テナントに追加されていないときに発生します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| access_denied |リソースの所有者が同意を拒否しました。 |クライアント アプリケーションは、ユーザーが同意しないと続行できないことを、ユーザーに通知できます。 |
| unsupported_response_type |承認サーバーは要求に含まれる応答の種類をサポートしていません。 |要求を修正し再送信します。 これは、開発エラーであり、通常は初期テスト中に発生します。 |
| server_error |サーバーで予期しないエラーが発生しました。 |要求をやり直してください。 これらのエラーは一時的な状況によって発生します。 クライアント アプリケーションは、一時的なエラーのため応答が遅れることをユーザーに説明する場合があります。 |
| temporarily_unavailable |サーバーが一時的にビジー状態であるため、要求を処理できません。 |要求をやり直してください。 クライアント アプリケーションは、一時的な状況が原因で応答が遅れることをユーザーに説明する場合があります。 |
| invalid_resource |対象のリソースは、存在しない、Azure AD が見つけられない、または正しく構成されていないために無効です。 |これは、リソース (存在する場合) がテナントで構成されていないことを示します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>承認コードを使用してアクセス トークンを要求する
承認コードを取得しユーザーからアクセス許可を得たら、POST 要求を `/token` エンドポイントに送信することで、目的のリソースのアクセス トークンのためにコードを使うことができます。

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| パラメーター | Type | 説明 |
| --- | --- | --- |
| tenant |required |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。 使用できる値はテナント ID です。たとえば、`8eaef023-2b34-4da1-9baa-8bc8c9d6a490`、`contoso.onmicrosoft.com` または `common` (テナント独立のトークンの場合) です |
| client_id |required |Azure AD への登録時にアプリに割り当てられたアプリケーション ID。 これは、Azure Portal で確認できます。 アプリケーション ID は、アプリの登録の設定で表示されます。 |
| grant_type |required |承認コード フローでは `authorization_code` を指定する必要があります。 |
| code |required |前のセクションで取得した `authorization_code` 。 |
| redirect_uri |required | クライアント アプリケーションに登録されている `redirect_uri`。 |
| client_secret |Web アプリで必須、パブリック クライアントでは使用不可 |アプリのために Azure Portal の **[キー]** で作成した、アプリケーションのシークレット。 ネイティブ アプリ (パブリック クライアント) では使用できません。デバイスに client_secret を確実に保存することができないためです。 Web アプリや Web API (すべての機密クライアント) では `client_secret` をサーバー側で安全に保存する機能が備わっているため、これを指定する必要があります。 client_secret は、送信前に URL エンコードされる必要があります。 |
| resource | 推奨 |対象となる Web API のアプリケーション ID/URI (セキュリティで保護されたリソース)。 アプリケーション ID/URI を調べるには、Azure Portal で **[Azure Active Directory]** 、 **[アプリの登録]** の順にクリックして、アプリケーションの **[設定]** ページを開きます。その後、 **[プロパティ]** をクリックします。 `https://graph.microsoft.com` のような外部リソースである場合もあります。 認証またはトークン要求でこれが必要です。 認証プロンプトの回数を少なくするには、認証要求に配置して、ユーザーから同意を受信できるようにします。 認証要求とトークン要求の if と resource パラメーターは一致する必要があります。 | 
| code_verifier | 省略可能 | authorization_code を取得するために使用されたのと同じ code_verifier。 承認コード付与要求で PKCE が使用された場合は必須です。 詳細については、「[PKCE RFC](https://tools.ietf.org/html/rfc7636)」を参照してください。   |

アプリケーション ID/URI を調べるには、Azure Portal で **[Azure Active Directory]** 、 **[アプリの登録]** の順にクリックして、アプリケーションの **[設定]** ページを開きます。その後、 **[プロパティ]** をクリックします。

### <a name="successful-response"></a>成功応答
成功応答時には Azure AD から[アクセス トークン](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)が返されます。 クライアント アプリケーションからのネットワーク呼び出しとこれに関連する遅延時間を最小限に抑えるために、クライアント アプリケーションは OAuth 2.0 応答で指定されているトークンの有効期間中、アクセス トークンをキャッシュする必要があります。 トークンの有効期間を決定するには、`expires_in` または `expires_on` のいずれかのパラメーター値を使用します。

Web API リソースから `invalid_token` エラー コードが返された場合、リソースが、トークンの有効期限が切れていると判断した可能性があります。 クライアントとリソースのクロック時間が異なる (「時間のずれ」として知られている) 場合、トークンがクライアント キャッシュからクリアされる前に、リソースがトークンの期限が切れていると認識することがあります。 このような場合は、計算上の有効期間内である場合でも、キャッシュからトークンをクリアします。

正常な応答は次のようになります。

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| パラメーター | 説明 |
| --- | --- |
| access_token |要求されたアクセス トークン。  これは不透明な文字列であり、リソースが受け取ることになっているものによって異なります。クライアントが確認するためのものではありません。 アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。 |
| token_type |トークン タイプ値を指定します。 Azure AD でサポートされるのは Bearer タイプのみです。 ベアラー トークンの詳細については、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) (OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750))](https://www.rfc-editor.org/rfc/rfc6750.txt)」をご覧ください。 |
| expires_in |アクセス トークンの有効期間 (秒)。 |
| expires_on |アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。 |
| resource |Web API のアプリケーション ID/URI (セキュリティで保護されたリソース)。 |
| scope |クライアント アプリケーションに付与される偽装アクセス許可。 既定のアクセス許可は `user_impersonation`です。 保護されたリソースの所有者は、別の値を Azure AD に登録できます。 |
| refresh_token |OAuth 2.0 更新トークン。 現在のアクセス トークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のアクセス トークンを取得することができます。 更新トークンは有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 |
| id_token |[ID トークン](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)を表す符号なしの JSON Web トークン (JWT)。 このトークンのセグメントを base64Url でデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 |

JSON Web トークンに関する詳細については、[JWT の IETF ドラフト仕様](https://go.microsoft.com/fwlink/?LinkId=392344)に関するページを参照してください。   `id_tokens` について詳しくは、[v1.0 OpenID Connect のフロー](v1-protocols-openid-connect-code.md)に関するページをご覧ください。

### <a name="error-response"></a>エラー応答
クライアントがトークン発行エンドポイントを直接呼び出すため、トークン発行エンドポイントは HTTP エラー コードです。 HTTP 状態コードだけでなく、Azure AD トークン発行エンドポイントも、エラーを説明するオブジェクトを含む JSON ドキュメントを返します。

エラー応答の例は次のようになります。

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| パラメーター | 説明 |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| error_codes |診断に役立つ STS 固有のエラー コードの一覧。 |
| timestamp |エラーが発生した時刻。 |
| trace_id |診断に役立つ、要求の一意の識別子。 |
| correlation_id |コンポーネント間での診断に役立つ、要求の一意の識別子。 |

#### <a name="http-status-codes"></a>HTTP 状態コード
次の表に、トークン発行エンドポイントが返す HTTP 状態コードを示します。 場合によっては、エラー コードだけで十分に応答を理解することもできますが、エラーが発生した場合は付属の JSON ドキュメントを解析し、そのエラー コードを確認する必要があります。

| HTTP コード | 説明 |
| --- | --- |
| 400 |既定の HTTP コード。 ほとんどの場合に使用され、通常は、形式が正しくない要求が原因です。 要求を修正し再送信します。 |
| 401 |認証に失敗しました。 たとえば、要求に client_secret パラメーターがありません。 |
| 403 |承認に失敗しました。 たとえば、ユーザーにリソースにアクセスするためのアクセス許可がありません。 |
| 500 |サービスで内部エラーが発生しました。 要求をやり直してください。 |

#### <a name="error-codes-for-token-endpoint-errors"></a>トークン エンドポイント エラーのエラー コード
| エラー コード | 説明 | クライアント側の処理 |
| --- | --- | --- |
| invalid_request |必要なパラメーターが不足しているなどのプロトコル エラーです。 |要求を修正し再送信します。 |
| invalid_grant |承認コードが無効であるか、または有効期限切れです。 |`/authorize` エンドポイントに対して改めて要求を試行します。 |
| unauthorized_client |認証されたクライアントは、この承認付与の種類を使用する権限がありません。 |これは通常、クライアント アプリケーションが Azure AD に登録されていない、またはユーザーの Azure AD テナントに追加されていないときに発生します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| invalid_client |クライアント認証に失敗しました。 |クライアント資格情報が有効ではありません。 修正するには、アプリケーション管理者が資格情報を更新します。 |
| unsupported_grant_type |承認サーバーが承認付与の種類をサポートしていません。 |要求の付与の種類を変更します。 この種のエラーは、開発時にのみ発生し、初期テスト中に検出する必要があります。 |
| invalid_resource |対象のリソースは、存在しない、Azure AD が見つけられない、または正しく構成されていないために無効です。 |これは、リソース (存在する場合) がテナントで構成されていないことを示します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| interaction_required |要求にユーザーの介入が必要です。 たとえば、追加の認証手順が必要です。 | 同じリソースに対して、非対話型の要求ではなく、対話型の承認要求で再試行してください。 |
| temporarily_unavailable |サーバーが一時的にビジー状態であるため、要求を処理できません。 |要求をやり直してください。 クライアント アプリケーションは、一時的な状況が原因で応答が遅れることをユーザーに説明する場合があります。 |

## <a name="use-the-access-token-to-access-the-resource"></a>リソースにアクセスするためにアクセス トークンを使用します。
`access_token` を無事取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、Web API への要求に使用することができます。 [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) 仕様では、HTTP 要求でベアラー トークンを使用して、保護されたリソースにアクセスする方法について説明されています。

### <a name="sample-request"></a>要求のサンプル
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>エラー応答
RFC 6750 を実装するセキュリティで保護されたリソースは、HTTP 状態コードを発行します。 要求に認証資格情報が含まれていない、または要求にトークンがない場合は、応答に `WWW-Authenticate` ヘッダーが含まれます。 要求が失敗したときに、リソース サーバーは HTTP 状態コードとエラー コードを含む応答を返します。

次に、クライアント要求にベアラー トークンが含まれていないときの失敗応答の一例を示します。

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>エラーのパラメーター
| パラメーター | 説明 |
| --- | --- |
| authorization_uri |承認サーバーの URI (物理エンドポイント)。 この値は、探索エンドポイントからサーバーの詳細を取得するための、ルックアップ キーとしても使用します。 <p><p> クライアントは、承認サーバーが信頼されていることを検証する必要があります。 リソースが Azure AD によって保護されている場合は、URL が `https://login.microsoftonline.com` または Azure AD によってサポートされる別のホスト名で始まることを確認するだけで十分です。 テナント固有のリソースは、テナント固有の承認 URI を常に返すはずです。 |
| error |「 [OAuth 2.0 Authorization Framework (OAuth 2.0 承認フレームワーク)](https://tools.ietf.org/html/rfc6749)」のセクション 5.2 で定義されているエラー コード値。 |
| error_description |エラーの詳しい説明。 このメッセージはエンドユーザー向けではありません。 |
| resource_id |リソースの一意の識別子を返します。 クライアント アプリケーションは、リソースのトークンを要求するときに、この識別子を `resource` パラメーターの値として使用できます。 <p><p> クライアント アプリケーションがこの値を確認することは重要です。確認を行わない場合、悪意のあるサービスから **権限昇格** 攻撃を受ける可能性があります。 <p><p> 攻撃を防止するための推奨方法として、 `resource_id` と、アクセスしている Web API URL のベースが一致していることを確認します。 たとえば、`https://service.contoso.com/data` にアクセスしている場合、`resource_id` は `https://service.contoso.com/` になります。 クライアント アプリケーションは、ID を検証する信頼性の高い代替方法がない限り、ベース URL ではじまらない `resource_id` を拒否する必要があります。 |

#### <a name="bearer-scheme-error-codes"></a>ベアラー スキームのエラー コード
RFC 6750 仕様では、応答で WWW-Authenticate ヘッダーとベアラー スキームを使用するリソースのために、次のエラーが定義されています。

| HTTP 状態コード | エラー コード | 説明 | クライアント側の処理 |
| --- | --- | --- | --- |
| 400 |invalid_request |要求の形式が正しくありません。 たとえば、パラメーターがない、または同じパラメーターを 2 回使用している可能性があります。 |エラーを修正して、要求を再試行してください。 この種のエラーは、開発時にのみ発生し、初期テスト中に検出する必要があります。 |
| 401 |invalid_token |アクセス トークンがない、無効である、または取り消されました。 error_description パラメーターの値で追加の詳細情報が提供されます。 |承認サーバーから新しいトークンを要求します。 新しいトークンが失敗すると、予期しないエラーが発生しました。 ユーザーにエラー メッセージを送信し、ランダムな遅延後に再試行します。 |
| 403 |insufficient_scope |アクセス トークンに、リソースにアクセスするために必要な偽装アクセス許可が含まれていません。 |新しい承認要求を承認エンドポイントに送信します。 応答にスコープのパラメーターが含まれている場合は、リソースへの要求でそのスコープ値を使用します。 |
| 403 |insufficient_access |トークンのサブジェクトに、リソースにアクセスするために必要なアクセス許可がありません。 |ユーザーに別のアカウントの使用か、指定のリソースへのアクセス許可の要求を求めるメッセージを表示します。 |

## <a name="refreshing-the-access-tokens"></a>アクセス トークンの更新

アクセス トークンは有効期間が短く、期限が切れた後もリソースにアクセスし続けるためにはトークンを更新する必要があります。 `access_token` を更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。このとき、`code` の代わりに `refresh_token` を指定します。  更新トークンは、クライアントが既にアクセスを同意されているすべてのリソースについて有効です。そのため、`resource=https://graph.microsoft.com` に対する要求で発行された更新トークンを使用して、`resource=https://contoso.com/api` に対する新しいアクセス トークンを要求できます。 

更新トークンには、指定された有効期間はありません。 通常、更新トークンの有効期間は比較的長いです。 ただし、場合によっては、更新トークンの有効期限が切れる、失効する、または目的の操作のための十分な特権がないことがあります。 クライアント アプリケーションは、トークン発行エンドポイントから返されるエラーを予期して正しく処理する必要があります。

更新トークン エラーを含む応答を受信したときは、現在の更新トークンを破棄し、新しい認証コードまたはアクセス トークンを要求します。 特に、認証コード付与フロー内で更新トークンを使用しているときに `interaction_required` または `invalid_grant` エラー コードを含む応答を受信した場合は、更新トークンを破棄し、新しい認証コードを要求します。

更新トークンを使って新しいアクセス トークンを取得する **テナント固有** のエンドポイント (**共通** エンドポイントの利用も可能) への要求の例は次のようになります。

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>成功応答
正常なトークン応答は次のようになります。

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| パラメーター | 説明 |
| --- | --- |
| token_type |トークンの型。 サポートされている値は **bearer** のみです。 |
| expires_in |トークンの残りの有効期間を秒単位で表したもの。 標準的な値は 3600 (1 時間) です。 |
| expires_on |トークンの有効期限が切れる日付と時刻。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 |
| resource |アクセス トークンを使ってアクセスできる保護されたリソースを識別します。 |
| scope |ネイティブ クライアント アプリケーションに付与される偽装アクセス許可。 既定のアクセス許可は **user_impersonation** です。 ターゲット リソースの所有者は、代替の値を Azure AD に登録できます。 |
| access_token |要求された新しいアクセス トークン。 |
| refresh_token |新しい OAuth 2.0 の refresh_token で、応答中にアクセス トークンの有効期限が切れた時に、新しいアクセス トークンを要求するために使用します。 |

### <a name="error-response"></a>エラー応答
エラー応答の例は次のようになります。

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| パラメーター | 説明 |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| error_codes |診断に役立つ STS 固有のエラー コードの一覧。 |
| timestamp |エラーが発生した時刻。 |
| trace_id |診断に役立つ、要求の一意の識別子。 |
| correlation_id |コンポーネント間での診断に役立つ、要求の一意の識別子。 |

エラー コードとクライアントに推奨される対処法については、「[トークン エンドポイント エラーのエラー コード](#error-codes-for-token-endpoint-errors)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure AD v1.0 エンドポイントの詳細と、Web アプリケーションと Web API に認証と承認を追加する方法については、[サンプル アプリケーション](sample-v1-code.md)に関するページをご覧ください。
