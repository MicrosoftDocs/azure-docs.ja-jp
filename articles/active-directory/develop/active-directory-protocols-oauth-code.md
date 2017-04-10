---
title: "Azure AD での OAuth 2.0 承認コード フローについて | Microsoft Docs"
description: "この記事では、Azure Active Directory と OAuth 2.0 を利用してテナントの Web アプリケーションと Web API へのアクセスを承認するために HTTP メッセージを使用する方法について説明します。"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: de3412cb-5fde-4eca-903a-4e9c74db68f2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: 06d8cb3ce2fe4419a79a63b76d67cc476d205e08
ms.openlocfilehash: a3e21d5af43562afde927bb623b910c96ad48158


---
# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>OAuth 2.0 と Azure Active Directory を使用した Web アプリケーションへのアクセスの承認
Azure Active Directory (Azure AD) が OAuth 2.0 を使用することにより、ユーザーは Azure AD テナントの Web アプリケーションと Web API へのアクセスを承認することができます。 本ガイドでは、オープンソース ライブラリを利用せず、HTTP メッセージを送受信する方法について説明します。本ガイドは言語非依存です。

OAuth 2.0 承認コード フローは、 [OAuth 2.0 仕様のセクション 4.1](https://tools.ietf.org/html/rfc6749#section-4.1)で規定されています。 Web アプリやネイティブにインストールされるアプリを含め、ほとんどの種類のアプリで認証と承認を行う際にこのフローが使用されます。

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>OAuth 2.0 承認フロー
まとめると、アプリケーションの全体的な承認フローは次のようになります。

![OAuth Auth Code Flow](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>承認コードを要求する
承認コード フローは、クライアントがユーザーを `/authorize` エンドポイントにリダイレクトさせることから始まります。 この要求で、クライアントは、ユーザーから取得する必要のあるアクセス許可を指定します。 Azure クラシック ポータルのアプリケーションのページ下部のドロアーにある **[エンドポイントの表示]** ボタンから OAuth 2.0 エンドポイントを取得できます。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| パラメーター |  | Description |
| --- | --- | --- |
| テナント |必須 |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。  使用できる値はテナント ID です。たとえば、`8eaef023-2b34-4da1-9baa-8bc8c9d6a490` や `contoso.onmicrosoft.com`、またはテナントに依存しないトークンの場合は `common` です |
| client_id |必須 |Azure AD への登録時にアプリに割り当てられたアプリケーション ID。 これは、Azure Portal で確認できます。 **[Active Directory]** をクリックしてからディレクトリをクリックし、アプリケーションを選択して **[構成]** をクリックします。 |
| response_type |必須 |承認コード フローでは `code` を指定する必要があります。 |
| redirect_uri |推奨 |アプリ の redirect_uri。アプリは、この URI で認証応答を送受信することができます。  ポータルで登録したいずれかの redirect_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。  ネイティブ アプリとモバイル アプリでは、`urn:ietf:wg:oauth:2.0:oob` の既定値を使用します。 |
| response_mode |推奨 |結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。  `query` または `form_post` を指定できます。 |
| state |推奨 |要求に含まれ、トークンの応答としても返される値。 [クロスサイト リクエスト フォージェリ攻撃を防ぐ](http://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。  この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| resource |省略可能 |Web API のアプリケーション ID/URI (セキュリティで保護されたリソース)。 Azure Portal で Web API のアプリケーション ID/URI を調べるには、**[Active Directory]** をクリックし、目的のディレクトリとアプリケーションを順にクリックして、**[構成]** をクリックします。 |
| prompt |省略可能 |ユーザーとの必要な対話の種類を指定します。<p> 有効な値は次のとおりです。 <p> *login*: 再認証を求めるメッセージがユーザーに表示されます。 <p> *consent*: ユーザーの同意は得られていますが、更新する必要があります。 同意を求めるメッセージがユーザーに表示されます。 <p> *admin_consent*: 組織内のすべてのユーザーを代表して同意するよう求めるメッセージが管理者に表示されます |
| login_hint |省略可能 |ユーザー名が事前にわかっている場合、このパラメータを使用すると、ユーザーに代わってサインイン ページのユーザー名/電子メール アドレス フィールドに事前入力できます。  多くのアプリでは、`preferred_username` 要求を使用して以前のサインインからユーザー名を抽出しておき、再認証時にこのパラメーターを使用します。 |
| domain_hint |省略可能 |ユーザーがサインインで使用することになるテナントまたはドメインについてのヒントを指定します。 テナントの登録ドメインが domain_hint の値となります。 テナントがオンプレミスのディレクトリと連動している場合、AAD から、指定されたテナントのフェデレーション サーバーにリダイレクトされます。 |

> [!NOTE]
> ユーザーが組織に所属している場合は、組織の管理者がユーザーに代わって同意または却下するか、あるいはユーザーに同意を許可することができます。 そのユーザーは、管理者が許可した場合のみ承認を行うことができます。
>
>

この時点でユーザーは、資格情報を入力し、`scope` クエリ パラメーターで指定されたアクセス許可に同意するよう要求されます。 ユーザーが認証を行い、同意の許可を与えると、Azure AD は要求で指定されたアプリの `redirect_uri` アドレスに応答を返します。

### <a name="successful-response"></a>成功応答
正常な応答は次のようになります。

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
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

| パラメーター | Description |
| --- | --- |
| error |「 [OAuth 2.0 Authorization Framework (OAuth 2.0 承認フレームワーク)](http://tools.ietf.org/html/rfc6749)」のセクション 5.2 で定義されているエラー コード値。 次の表で、Azure AD が返すエラー コードについて説明します。 |
| error_description |エラーの詳しい説明。 このメッセージはエンドユーザー向けではありません。 |
| state |state 値は、ランダムに生成された再利用されない値で、クロスサイト リクエスト フォージェリ (CSRF) 攻撃を防ぐために、要求で送信され、応答で返されます。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>承認エンドポイント エラーのエラー コード
次の表で、エラー応答の `error` パラメーターで返される可能性のあるさまざまなエラー コードを説明します。

| エラー コード | Description | クライアント側の処理 |
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
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| パラメーター |  | Description |
| --- | --- | --- |
| テナント |必須 |要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。  使用できる値はテナント ID です。たとえば、`8eaef023-2b34-4da1-9baa-8bc8c9d6a490` や `contoso.onmicrosoft.com`、またはテナントに依存しないトークンの場合は `common` です |
| client_id |必須 |Azure AD への登録時にアプリに割り当てられたアプリケーション ID。 値は Azure クラシック ポータルにあります。 **[Active Directory]** をクリックしてからディレクトリをクリックし、アプリケーションを選択して **[構成]** をクリックします。 |
| grant_type |必須 |承認コード フローでは `authorization_code` を指定する必要があります。 |
| code |必須 |前のセクションで取得した `authorization_code` 。 |
| redirect_uri |必須 |`authorization_code` を取得するために使用したのと同じ `redirect_uri` 値。 |
| client_secret |Web アプリの場合は必須 |アプリ登録ポータルで作成した、アプリケーションのシークレット。  ネイティブ アプリでは使用しないでください。デバイスに client_secret を確実に保存することができません。  Web アプリや Web API では `client_secret` をサーバー側で安全に保存する機能が備わっており、指定する必要があります。 |
| resource |承認コード要求で指定された場合は必須、それ以外の場合は省略可 |Web API のアプリケーション ID/URI (セキュリティで保護されたリソース)。 |

アプリケーション ID URI を調べるには、Azure 管理ポータルで、**[Active Directory]** をクリックし、目的のディレクトリとアプリケーションを順にクリックして、**[構成]** をクリックします。

### <a name="successful-response"></a>成功応答
成功応答時には Azure AD からアクセス トークンが返されます。 クライアント アプリケーションからのネットワーク呼び出しとこれに関連する遅延時間を最小限に抑えるために、クライアント アプリケーションは OAuth 2.0 応答で指定されているトークンの有効期間中、アクセス トークンをキャッシュする必要があります。 トークンの有効期間を決定するには、`expires_in` または `expires_on` のいずれかのパラメーター値を使用します。

Web API リソースから `invalid_token` エラー コードが返された場合、リソースが、トークンの有効期限が切れていると判断した可能性があります。 クライアントとリソースのクロック時間が異なる (「時間のずれ」として知られている) 場合、トークンがクライアント キャッシュからクリアされる前に、リソースがトークンの期限が切れていると認識することがあります。 このような場合は、計算上の有効期間内である場合でも、キャッシュからトークンをクリアします。

正常な応答は次のようになります。

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| パラメーター | Description |
| --- | --- |
| access_token |要求されたアクセス トークン。 アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。 |
| token_type |トークン タイプ値を指定します。 Azure AD でサポートされるのは Bearer タイプのみです。 ベアラー トークンに関する詳細については、「 [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750) (OAuth2.0 承認フレームワーク: ベアラー トークンの使用について (RFC 6750))](http://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |アクセス トークンの有効期間 (秒)。 |
| expires_on |アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。 |
| resource |Web API のアプリケーション ID/URI (セキュリティで保護されたリソース)。 |
| scope |クライアント アプリケーションに付与される偽装アクセス許可。 既定のアクセス許可は `user_impersonation`です。 保護されたリソースの所有者は、別の値を Azure AD に登録できます。 |
| refresh_token |OAuth 2.0 更新トークン。 現在のアクセス トークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のアクセス トークンを取得することができます。  更新トークンは有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 |
| id_token |無署名の JSON Web トークン (JWT)。 このトークンのセグメントを base64Url でデコードすることによって、サインインしたユーザーに関する情報を要求することができます。 この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 |

### <a name="jwt-token-claims"></a>JWT トークン要求
`id_token` パラメーターの値にある JWT トークンは次の要求にデコードすることができます。

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

JSON Web トークンに関する詳細については、[JWT の IETF ドラフト仕様](http://go.microsoft.com/fwlink/?LinkId=392344)に関するページを参照してください。 トークンの種類と要求の詳細については、[サポートされているトークンと要求の種類](active-directory-token-and-claims.md)に関するページを参照してください。

`id_token` パラメーターには、以下の要求の種類があります。

| 要求の種類 | 説明 |
| --- | --- |
| aud |トークンの対象ユーザー。 クライアント アプリケーションにトークンが発行される場合、対象ユーザーは、クライアントの `client_id` です。 |
| exp |期限切れ日時。 トークンの有効期限が切れる日時。 トークンを有効にするには、現在の日付/時刻が `exp` の値以前である必要があります。 日時は UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) から、トークンが発行された日時までの秒数で表されます。 |
| family_name |ユーザーの姓。 アプリケーションでは、この値を表示できます。 |
| given_name |ユーザーの名。 アプリケーションでは、この値を表示できます。 |
| iat |発行日時。 JWT が発行された日時。 日時は UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) から、トークンが発行された日時までの秒数で表されます。 |
| iss |トークン発行者を識別します。 |
| nbf |期間の開始日時。 トークンが有効になる日時。 トークンを有効にするには、現在の日付/時刻が Nbf 値以降である必要があります。 日時は UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) から、トークンが発行された日時までの秒数で表されます。 |
| oid |Azure AD におけるユーザー オブジェクトのオブジェクト識別子 (ID)。 |
| sub |トークンのサブジェクト識別子。 これは、トークンが示すユーザーの永続的で不変の識別子です。 キャッシュ ロジックでは、この値を使用します。 |
| tid |トークンを発行した Azure AD テナントのテナント識別子 (ID) です。 |
| unique_name |ユーザーに表示される一意識別子。 これは、通常ユーザー プリンシパル名 (UPN) です。 |
| upn |ユーザーのユーザー プリンシパル名。 |
| ver |バージョン。 JWT トークンのバージョンで、通常は 1.0 です。 |

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
| パラメーター | Description |
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
| エラー コード | Description | クライアント側の処理 |
| --- | --- | --- |
| invalid_request |必要なパラメーターが不足しているなどのプロトコル エラーです。 |要求を修正し再送信します。 |
| invalid_grant |承認コードが無効であるか、または有効期限切れです。 |`/authorize` エンドポイントに対して改めて要求を試行します。 |
| unauthorized_client |認証されたクライアントは、この承認付与の種類を使用する権限がありません。 |これは通常、クライアント アプリケーションが Azure AD に登録されていない、またはユーザーの Azure AD テナントに追加されていないときに発生します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| invalid_client |クライアント認証に失敗しました。 |クライアント資格情報が有効ではありません。 修正するには、アプリケーション管理者が資格情報を更新します。 |
| unsupported_grant_type |承認サーバーが承認付与の種類をサポートしていません。 |要求の付与の種類を変更します。 この種のエラーは、開発時にのみ発生し、初期テスト中に検出する必要があります。 |
| invalid_resource |対象のリソースは、存在しない、Azure AD が見つけられない、または正しく構成されていないために無効です。 |これは、リソース (存在する場合) がテナントで構成されていないことを示します。 アプリケーションでは、アプリケーションのインストールと Azure AD への追加を求める指示をユーザーに表示できます。 |
| interaction_required |要求にユーザーの介入が必要です。 たとえば、追加の認証手順が必要です。 |同じリソースで要求を再試行します。 |
| temporarily_unavailable |サーバーが一時的にビジー状態であるため、要求を処理できません。 |要求をやり直してください。 クライアント アプリケーションは、一時的な状況が原因で応答が遅れることをユーザーに説明する場合があります。 |

## <a name="use-the-access-token-to-access-the-resource"></a>リソースにアクセスするためにアクセス トークンを使用します。
`access_token` を無事取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、Web API への要求に使用することができます。 [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) 仕様では、HTTP 要求でベアラー トークンを使用して、保護されたリソースにアクセスする方法について説明されています。

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
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>エラーのパラメーター
| パラメーター | 説明 |
| --- | --- |
| authorization_uri |承認サーバーの URI (物理エンドポイント)。 この値は、探索エンドポイントからサーバーの詳細を取得するための、ルックアップ キーとしても使用します。 <p><p> クライアントは、承認サーバーが信頼されていることを検証する必要があります。 リソースが Azure AD によって保護されている場合は、URL が https://login.windows.net または Azure AD がサポートする別のホスト名で始まることを確認するだけで十分です。 テナント固有のリソースは、テナント固有の承認 URI を常に返すはずです。 |
| error |「 [OAuth 2.0 Authorization Framework (OAuth 2.0 承認フレームワーク)](http://tools.ietf.org/html/rfc6749)」のセクション 5.2 で定義されているエラー コード値。 |
| error_description |エラーの詳しい説明。 このメッセージはエンドユーザー向けではありません。 |
| resource_id |リソースの一意の識別子を返します。 クライアント アプリケーションは、リソースのトークンを要求するときに、この識別子を `resource` パラメーターの値として使用できます。 <p><p> クライアント アプリケーションがこの値を確認することは重要です。確認を行わない場合、悪意のあるサービスから**権限昇格**攻撃を受ける可能性があります。 <p><p> 攻撃を防止するための推奨方法として、`resource_id` と、アクセスしている Web API URL のベースが一致していることを確認します。 たとえば、https://service.contoso.com/data にアクセスしている場合、`resource_id` は htttps://service.contoso.com/ になります。 クライアント アプリケーションは、ID を検証する信頼性の高い代替方法がない限り、ベース URL ではじまらない `resource_id` を拒否する必要があります。 |

#### <a name="bearer-scheme-error-codes"></a>ベアラー スキームのエラー コード
RFC 6750 仕様では、応答で WWW-Authenticate ヘッダーとベアラー スキームを使用するリソースのために、次のエラーが定義されています。

| HTTP 状態コード | エラー コード | Description | クライアント側の処理 |
| --- | --- | --- | --- |
| 400 |invalid_request |要求の形式が正しくありません。 たとえば、パラメーターがない、または同じパラメーターを&2; 回使用している可能性があります。 |エラーを修正して、要求を再試行してください。 この種のエラーは、開発時にのみ発生し、初期テスト中に検出する必要があります。 |
| 401 |invalid_token |アクセス トークンがない、無効である、または取り消されました。 error_description パラメーターの値で追加の詳細情報が提供されます。 |承認サーバーから新しいトークンを要求します。 新しいトークンが失敗すると、予期しないエラーが発生しました。 ユーザーにエラー メッセージを送信し、ランダムな遅延後に再試行します。 |
| 403 |insufficient_scope |アクセス トークンに、リソースにアクセスするために必要な偽装アクセス許可が含まれていません。 |新しい承認要求を承認エンドポイントに送信します。 応答にスコープのパラメーターが含まれている場合は、リソースへの要求でそのスコープ値を使用します。 |
| 403 |insufficient_access |トークンのサブジェクトに、リソースにアクセスするために必要なアクセス許可がありません。 |ユーザーに別のアカウントの使用か、指定のリソースへのアクセス許可の要求を求めるメッセージを表示します。 |

## <a name="refreshing-the-access-tokens"></a>アクセス トークンの更新
アクセス トークンは有効期間が短く、期限が切れた後もリソースにアクセスし続けるためにはトークンを更新する必要があります。 `access_token` を更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。このとき、`code` の代わりに `refresh_token` を指定します。

更新トークンには、指定された有効期間はありません。 通常、更新トークンの有効期間は比較的長いです。 ただし、場合によっては、更新トークンの有効期限が切れる、失効する、または目的の操作のための十分な特権がないことがあります。 クライアント アプリケーションは、トークン発行エンドポイントから返されるエラーを予期して正しく処理する必要があります。

更新トークン エラーを含む応答を受信したときは、現在の更新トークンを破棄し、新しい認証コードまたはアクセス トークンを要求します。 特に、認証コード付与フロー内で更新トークンを使用しているときに `interaction_required` または `invalid_grant` エラー コードを含む応答を受信した場合は、更新トークンを破棄し、新しい認証コードを要求します。

更新トークンを使って新しいアクセス トークンを取得する**テナント固有**のエンドポイント (**共通**エンドポイントの利用も可能) への要求の例は次のようになります。

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
| パラメーター | Description |
| --- | --- |
| access_token |要求された新しいアクセス トークン。 |
| expires_in |トークンの残りの有効期間を秒単位で表したもの。 標準的な値は 3600 (1 時間) です。 |
| expires_on |トークンの有効期限が切れる日付と時刻。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 |
| refresh_token |新しい OAuth 2.0 の refresh_token で、応答中にアクセス トークンの有効期限が切れた時に、新しいアクセス トークンを要求するために使用します。 |
| resource |アクセス トークンを使ってアクセスできる保護されたリソースを識別します。 |
| scope |ネイティブ クライアント アプリケーションに付与される偽装アクセス許可。 既定のアクセス許可は **user_impersonation** です。 ターゲット リソースの所有者は、代替の値を Azure AD に登録できます。 |
| token_type |トークンのタイプ。 サポートされている値は **bearer**のみです。 |

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

### <a name="error-response"></a>エラー応答
エラー応答の例は次のようになります。

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| パラメーター | Description |
| --- | --- |
| error |発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error_description |認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| error_codes |診断に役立つ STS 固有のエラー コードの一覧。 |
| timestamp |エラーが発生した時刻。 |
| trace_id |診断に役立つ、要求の一意の識別子。 |
| correlation_id |コンポーネント間での診断に役立つ、要求の一意の識別子。 |

エラー コードとクライアントに推奨される対処法については、「[トークン エンドポイント エラーのエラー コード](#error-codes-for-token-endpoint-errors)」を参照してください。


<!--HONumber=Feb17_HO2-->


