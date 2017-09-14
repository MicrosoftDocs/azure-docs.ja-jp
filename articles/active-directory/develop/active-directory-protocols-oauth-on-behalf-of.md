---
title: "OAuth2.0 On-Behalf-Of のドラフト仕様に基づく Azure AD サービス間認証 | Microsoft Docs"
description: "この記事では、HTTP メッセージを使用して、OAuth2.0 On-Behalf-Of フローを使用するサービス間の認証を実装する方法について説明します。"
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: nacanuma
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 0bb74816f216f0965c3ec780c4895cf7e488c3cf
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017


---
# On-Behalf-Of フローでの委任ユーザー ID を使ったサービス間の呼び出し
OAuth 2.0 の On-Behalf-Of フローは、アプリケーションがサービス/Web API を呼び出し、それがさらに別のサービス/Web API を呼び出す必要のあるユース ケースを提供します。 その考え方は、委任されたユーザー ID とアクセス許可を要求チェーン経由で伝達するというものです。 中間層サービスがダウンストリーム サービスに認証済み要求を発行するには、そのサービスは Azure Active Directory (Azure AD) からのアクセス トークンをユーザーに代わってセキュリティ保護する必要があります。

## On-Behalf-Of フローの図
[OAuth 2.0 認証コード付与フロー](active-directory-protocols-oauth-code.md)を使用するアプリケーションで、ユーザーが認証されているとします。 この時点で、そのアプリケーションには、中間層 Web API (API A) にアクセスするためのユーザーの要求と同意を含むアクセス トークン (トークン A) があります。 ここで、API A はダウンストリーム Web API (API B) に認証済み要求を発行する必要があります。

以降の手順は On-Behalf-Of フローを構成するものであり、次の図を使用して説明します。

![OAuth2.0 の On-Behalf-Of フロー](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. クライアント アプリケーションは、トークン A を使用して API A に要求を発行します。
2. API A が Azure AD トークン発行エンドポイントに対して認証処理を行い、API B にアクセスするためのトークンを要求します。
3. Azure AD トークン発行エンドポイントはトークン A を使用して API A の資格情報を検証し、API B (トークン B) へのアクセス トークンを発行します。
4. API B への要求の承認ヘッダー内にトークン B が設定されます。
5. セキュリティで保護されたリソースからのデータが API B によって返されます。

## Azure AD でのアプリケーションとサービスの登録
Azure AD で、クライアント アプリケーションと中間層サービスの両方を登録します。
### 中間層サービスの登録
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 上部のバーにある自分のアカウントをクリックし、**[ディレクトリ]** の一覧から、アプリケーションを登録する Active Directory テナントを選択します。
3. 左側のナビゲーションで **[More Services (その他のサービス)]**をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックし、**[新しいアプリケーションの登録]** を選択します。
5. アプリケーションのフレンドリ名を入力し、アプリケーションの種類を選択します。 アプリケーションの種類に基づいて、サインオン URL またはリダイレクト URL をベース URL に設定します。 **[作成]** をクリックして、アプリケーションを作成します。
6. 引き続き Azure Portal でアプリケーションを選択して、**[設定]** をクリックします。 [設定] メニューから **[キー]** を選択し、キーを追加します。キーの期間として 1 年または 2 年のいずれかを選択します。 このページを保存すると、キーの値が表示されるので、その値をコピーして安全な場所に保存します。このキーは、後ほど実装でアプリケーション設定を構成するときに必要になります。このキーの値は二度と表示されません。また、他のいかなる方法でも取得できません。したがって、Azure Portal で表示されたらすぐに記録してください。

### クライアント アプリケーションの登録
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 上部のバーにある自分のアカウントをクリックし、**[ディレクトリ]** の一覧から、アプリケーションを登録する Active Directory テナントを選択します。
3. 左側のナビゲーションで **[More Services (その他のサービス)]**をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックし、**[新しいアプリケーションの登録]** を選択します。
5. アプリケーションのフレンドリ名を入力し、アプリケーションの種類を選択します。 アプリケーションの種類に基づいて、サインオン URL またはリダイレクト URL をベース URL に設定します。 **[作成]** をクリックして、アプリケーションを作成します。
6. アプリケーションのアクセス許可を構成します。[設定] メニューで **[必要なアクセス許可]** セクションを選択して、**[追加]**、**[API を選択します]** の順にクリックし、中間層サービスの名前をテキストボックスに入力します。 **[アクセス許可の選択]** をクリックし、[*サービス名*にアクセス] を選択します。

### 既知のクライアント アプリケーションの構成
このシナリオでは、中間層サービスに、ダウンストリーム API にアクセスするユーザーの同意を得るためのユーザー操作はありません。 そのため、認証中の同意ステップの一部として、ダウンストリームの API へのアクセスを許可するためのオプションをあらかじめ指定する必要があります。
それには、次の手順に従って、Azure AD でのクライアント アプリの登録を中間層サービスの登録と明示的にバインドすることにより、クライアントと中間層の両方に必要な同意を 1 つのダイアログに統合します。
1. 中間層サービスの登録に移動し、**[マニフェスト]** をクリックしてマニフェスト エディターを開きます。
2. マニフェストで `knownClientApplications` 配列プロパティを探し、要素としてクライアント アプリケーションのクライアント ID を追加します。
3. 保存ボタンをクリックしてマニフェストを保存します。

## サービス間のアクセス トークン要求
アクセス トークンを要求するには、次のパラメーターを使用して、テナントに固有の Azure AD エンドポイントへの HTTP POST を作成します。

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
クライアント アプリケーションのセキュリティ保護に共有シークレットまたは証明書のどちらを使うかに応じて、2 つのケースがあります。

### 最初のケース: 共有シークレットを使ったアクセス トークン要求
共有シークレットを使用する場合、サービス間のアクセス トークン要求には、次のパラメーターが含まれてます。

| パラメーター |  | Description |
| --- | --- | --- |
| grant_type |必須 | トークン要求の種類。 JWT を使用する要求の場合、値は **urn:ietf:params:oauth:grant-type:jwt-bearer** である必要があります。 |
| assertion |必須 | 要求で使用されるトークンの値。 |
| client_id |必須 | Azure AD での登録時に呼び出し元のサービスに割り当てられるアプリケーション ID。 Azure 管理ポータルでアプリ ID を調べるには、**[Active Directory]**、目的のディレクトリ、アプリケーション名の順にクリックします。 |
| client_secret |必須 | 呼び出し元のサービスに対して Azure AD に登録されているキー。 この値は登録時にメモしているはずです。 |
| resource |必須 | 受信側のサービスのアプリ ID URI (セキュリティ保護されたリソース)。 アプリ ID URI を調べるには、Azure 管理ポータルで、**[Active Directory]**、目的のディレクトリ、アプリケーション名、**[すべての設定]**、**[プロパティ]** の順にクリックします。 |
| requested_token_use |必須 | 要求の処理方法を指定します。 On-Behalf-Of フローでは、値は **on_behalf_of** である必要があります。 |
| scope |必須 | トークン要求のスコープのスペース区切りリスト。 OpenID Connect の場合、スコープの **openid** を指定する必要があります。|

#### 例
次の HTTP POST は、https://graph.windows.net Web API のアクセス トークンを要求します。 `client_id` は、アクセス トークンを要求するサービスを識別します。

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### 2 番目のケース: 証明書を使ったアクセス トークン要求
証明書を含むサービス間のアクセス トークン要求には、次のパラメーターが含まれています。

| パラメーター |  | Description |
| --- | --- | --- |
| grant_type |必須 | トークン要求の種類。 JWT を使用する要求の場合、値は **urn:ietf:params:oauth:grant-type:jwt-bearer** である必要があります。 |
| assertion |必須 | 要求で使用されるトークンの値。 |
| client_id |必須 | Azure AD での登録時に呼び出し元のサービスに割り当てられるアプリケーション ID。 Azure 管理ポータルでアプリ ID を調べるには、**[Active Directory]**、目的のディレクトリ、アプリケーション名の順にクリックします。 |
| client_assertion_type |必須 |値は `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` である必要があります |
| client_assertion |必須 | 作成する必要があるアサーション (JSON Web Token) です。このアサーションは、アプリケーションの資格情報として登録した証明書で署名する必要があります。  証明書の登録方法とアサーションの形式の詳細については、[証明書資格情報](active-directory-certificate-credentials.md)に関する記事を参照してください。|
| resource |必須 | 受信側のサービスのアプリ ID URI (セキュリティ保護されたリソース)。 アプリ ID URI を調べるには、Azure 管理ポータルで、**[Active Directory]**、目的のディレクトリ、アプリケーション名、**[すべての設定]**、**[プロパティ]** の順にクリックします。 |
| requested_token_use |必須 | 要求の処理方法を指定します。 On-Behalf-Of フローでは、値は **on_behalf_of** である必要があります。 |
| scope |必須 | トークン要求のスコープのスペース区切りリスト。 OpenID Connect の場合、スコープの **openid** を指定する必要があります。|

パラメーターは、共有シークレットによる要求のパラメーターとほぼ同じであることに注意してください。唯一異なるのは、client_secret パラメーターが、client_assertion_type と client_assertion の 2 つのパラメーターに置き換えられている点です。

#### 例
次の HTTP POST は、証明書のある、https://graph.windows.net Web API のアクセス トークンを要求します。 `client_id` は、アクセス トークンを要求するサービスを識別します。

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## サービス間のアクセス トークン応答
成功応答は、次のパラメーターを含む JSON OAuth 2.0 応答です。

| パラメーターが含まれる必要があります。 | Description |
| --- | --- |
| token_type |トークン タイプ値を指定します。 Azure AD でサポートされるのは **Bearer**タイプのみです。 ベアラー トークンの詳細については、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) (OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750))](http://www.rfc-editor.org/rfc/rfc6750.txt)」を参照してください。 |
| scope |トークンで付与されるアクセスのスコープ。 |
| expires_in |アクセス トークンが有効な時間の長さ (秒単位)。 |
| expires_on |アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。 |
| resource |受信側のサービスのアプリ ID URI (セキュリティ保護されたリソース)。 |
| access_token |要求されたアクセス トークン。 呼び出し元のサービスは、このトークンを使用して受信側のサービスへの認証を行うことができます。 |
| id_token |要求された ID トークン。 呼び出し元のサービスは、これを使用してユーザー ID を確認し、そのユーザーとのセッションを開始することができます。 |
| refresh_token |要求されたアクセス トークンの更新トークン。 呼び出し元のサービスは、現在のアクセス トークンの期限が切れた後に、このトークンを使用して別のアクセス トークンを要求できます。 |

### 成功応答の例
次の例に、https://graph.windows.net Web API のアクセス トークン要求に対する成功応答を示します。

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### エラー応答の例
ダウンストリーム API に多要素認証などの条件付きアクセス ポリシーが設計されている場合は、ダウンストリーム API へのアクセス トークンを取得しようとすると、Azure AD トークン エンドポイントによってエラー応答が返されます。 クライアント アプリケーションが条件付きアクセス ポリシーを満たすためのユーザー操作を提供できるように、中間層サービスはこのエラーをクライアント アプリケーションに示す必要があります。

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## アクセス トークンを使用して、セキュリティ保護されたリソースにアクセスする
これで、中間層サービスは上で取得されたトークンを使用して、そのトークンを `Authorization` ヘッダー内に設定することによってダウンストリーム Web API に認証済み要求を発行できます。

### 例
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## 次のステップ
OAuth 2.0 プロトコルと、クライアント資格情報を使用したサービス間認証を実行する別の方法について詳しく学びます。
* [OAuth 2.0 クライアント資格情報付与を使用した Azure AD のサービス間の認証](active-directory-protocols-oauth-service-to-service.md)
* [Azure AD での OAuth 2.0](active-directory-protocols-oauth-code.md)

