---
title: OAuth2.0 On-Behalf-Of ドラフト仕様を使用する Azure Active Directory サービス間認証 | Microsoft Docs
description: この記事では、HTTP メッセージを使用して、OAuth2.0 On-Behalf-Of フローを使用するサービス間の認証を実装する方法について説明します。
services: active-directory
documentationcenter: .net
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: hirsin, nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51fd5c8f406ea54c7fc8e81c674e41b30d7ad406
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482414"
---
# <a name="service-to-service-calls-that-use-delegated-user-identity-in-the-on-behalf-of-flow"></a>On-Behalf-Of フローでの委任ユーザー ID を使用するサービス間の呼び出し

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

OAuth 2.0 On-Behalf-Of (OBO) フローにより、サービスまたは Web API を起動するアプリケーションが、別のサービスまたは Web API にユーザー認証を渡すことができるようになります。 OBO フローは、委任されたユーザー ID とアクセス許可を要求チェーン経由で伝達します。 中間層サービスがダウンストリーム サービスに認証済み要求を発行するには、そのサービスは Azure Active Directory (Azure AD) からのアクセス トークンをユーザーに代わってセキュリティ保護する必要があります。

> [!IMPORTANT]
> 2018 年 5 月の時点で、`id_token` を On-Behalf-Of フローで使用することはできません。  シングルページ アプリ (SPA) では、中間層の機密クライアントにアクセス トークンを渡して、OBO フローを実行する必要があります。 On-Behalf-Of 呼び出しを実行できるクライアントの詳細については、[制限事項](#client-limitations)に関する記述を参照してください。

## <a name="on-behalf-of-flow-diagram"></a>On-Behalf-Of フローの図

OBO フローは、[OAuth 2.0 認証コード付与フロー](v1-protocols-oauth-code.md)を使用するアプリケーションでユーザーが認証された後に開始されます。 その時点で、アプリケーションは中間層の Web API (API A) に、API A にアクセスするためのユーザーの要求と同意を含むアクセス トークン (トークン A) を送信します。次に、API A はダウンストリームの Web API (API B) に認証済み要求を行います。

On-Behalf-Of フローは次の手順で構成されます。![OAuth2.0 の On-Behalf-Of フローの手順](./media/v1-oauth2-on-behalf-of-flow/active-directory-protocols-oauth-on-behalf-of-flow.png)

1. クライアント アプリケーションは、トークン A を使用して API A に要求を発行します。
1. API A が Azure AD トークン発行エンドポイントに対して認証処理を行い、API B にアクセスするためのトークンを要求します。
1. Azure AD トークン発行エンドポイントはトークン A を使用して API A の資格情報を検証し、API B (トークン B) へのアクセス トークンを発行します。
1. API B への要求には、Authorization ヘッダー内にトークン B が含まれています。
1. API B は、セキュリティで保護されたリソースからデータを返します。

>[!NOTE]
>ダウンストリーム サービスのトークンを要求するために使用されるアクセス トークンの受信者要求は、OBO 要求を行うサービスの ID である必要があります。 このトークンは、Azure Active Directory のグローバル署名キーで署名されている必要があります (ポータルの**アプリの登録**を介して登録されたアプリケーションでは既定)。

## <a name="register-the-application-and-service-in-azure-ad"></a>Azure AD でのアプリケーションとサービスの登録

Azure AD で、中間層サービスとクライアント アプリケーションの両方を登録します。

### <a name="register-the-middle-tier-service"></a>中間層サービスの登録

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 上部のバーにある自分のアカウントを選択し、 **[ディレクトリ]** の一覧からアプリケーションの Active Directory テナントを選択します。
1. 左側のウィンドウで **[その他のサービス]** を選択し、 **[Azure Active Directory]** を選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションのフレンドリ名を入力し、アプリケーションの種類を選択します。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
1. リダイレクト URI をベース URL に設定します。
1. **[登録]** を選択して、アプリケーションを作成します。
1. Azure portal を終了する前に、クライアント シークレットを生成します。
1. Azure portal でアプリケーションを選択して、 **[証明書とシークレット]** を選択します。
1. **[New client secret]\(新しいクライアント シークレット\)** を選択し、有効期間が 1 年または 2 年のシークレットを追加します。
1. このページを保存すると、Azure portal にシークレット値が表示されます。 シークレット値をコピーして安全な場所に保存します。

> [!IMPORTANT]
> 実装でアプリケーション設定を構成するには、このシークレットが必要です。 このシークレット値は二度と表示されず、他の方法で取得することはできません。 Azure portal で表示されたら、すぐに記録してください。

### <a name="register-the-client-application"></a>クライアント アプリケーションの登録

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 上部のバーにある自分のアカウントを選択し、 **[ディレクトリ]** の一覧からアプリケーションの Active Directory テナントを選択します。
1. 左側のウィンドウで **[その他のサービス]** を選択し、 **[Azure Active Directory]** を選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションのフレンドリ名を入力し、アプリケーションの種類を選択します。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
1. リダイレクト URI をベース URL に設定します。
1. **[登録]** を選択して、アプリケーションを作成します。
1. アプリケーション用にアクセス許可を構成します。 **[API のアクセス許可]** で、 **[アクセス許可の追加]** を選択し、 **[自分の API]** を選択します。
1. テキスト フィールドに中間層サービスの名前を入力します。
1. **[アクセス許可の選択]** を選択し、 **[<service name> にアクセス]** を選択します。

### <a name="configure-known-client-applications"></a>既知のクライアント アプリケーションの構成

このシナリオでは、中間層サービスは、ユーザーの操作なしで、ダウンストリーム API にアクセスするためのユーザーの同意を得る必要があります。 認証中の同意ステップの一部として、ダウンストリームの API へのアクセスを許可するためのオプションをあらかじめ表示する必要があります。

次の手順に従って、Azure AD でのクライアント アプリの登録を中間層サービスの登録に明示的にバインドします。 この操作により、クライアントと中間層の両方で必要な同意が、1 つのダイアログにマージされます。

1. 中間層サービスの登録に移動し、 **[マニフェスト]** を選択してマニフェスト エディターを開きます。
1. `knownClientApplications` 配列プロパティを探し、要素としてクライアント アプリケーションのクライアント ID を追加します。
1. **[保存]** を選択してマニフェストを保存します。

## <a name="service-to-service-access-token-request"></a>サービス間のアクセス トークン要求

アクセス トークンを要求するには、次のパラメーターを使用して、テナントに固有の Azure AD エンドポイントへの HTTP POST を作成します。

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```

クライアント アプリケーションは、共有シークレットまたは証明書によってセキュリティ保護されます。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>最初のケース:共有シークレットを使ったアクセス トークン要求

共有シークレットを使用する場合、サービス間のアクセス トークン要求には、次のパラメーターが含まれてます。

| パラメーター |  | 説明 |
| --- | --- | --- |
| grant_type |必須 | トークン要求の種類。 OBO 要求は JSON Web トークン (JWT) を使用するため、その値は **urn:ietf:params:oauth:grant-type:jwt-bearer** である必要があります。 |
| assertion |必須 | 要求で使用されるアクセス トークンの値。 |
| client_id |必須 | Azure AD での登録時に呼び出し元のサービスに割り当てられるアプリ ID。 Azure portal でアプリ ID を調べるには、 **[Active Directory]** 、目的のディレクトリ、アプリケーション名の順に選択します。 |
| client_secret |必須 | 呼び出し元のサービスに対して Azure AD に登録されているキー。 この値は登録時にメモしているはずです。 |
| resource |必須 | 受信側のサービスのアプリ ID URI (セキュリティ保護されたリソース)。 Azure portal でアプリ ID URI を調べるには、 **[Active Directory]** を選択し、目的のディレクトリを選びます。 アプリケーション名を選択し、 **[すべての設定]** 、 **[プロパティ]** の順に選択します。 |
| requested_token_use |必須 | 要求の処理方法を指定します。 On-Behalf-Of フローでは、値は **on_behalf_of** である必要があります。 |
| scope |必須 | トークン要求のスコープのスペース区切りリスト。 OpenID Connect の場合、スコープの **openid** を指定する必要があります。|

#### <a name="example"></a>例

次の HTTP POST は、 https://graph.windows.net Web API のアクセス トークンを要求します。 `client_id` は、アクセス トークンを要求するサービスを識別します。

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

### <a name="second-case-access-token-request-with-a-certificate"></a>2 番目のケース:証明書を使ったアクセス トークン要求

証明書を含むサービス間のアクセス トークン要求には、次のパラメーターが含まれています。

| パラメーター |  | 説明 |
| --- | --- | --- |
| grant_type |必須 | トークン要求の種類。 OBO 要求は JWT アクセス トークンを使用するため、その値は **urn:ietf:params:oauth:grant-type:jwt-bearer** である必要があります。 |
| assertion |必須 | 要求で使用されるトークンの値。 |
| client_id |必須 | Azure AD での登録時に呼び出し元のサービスに割り当てられるアプリ ID。 Azure portal でアプリ ID を調べるには、 **[Active Directory]** 、目的のディレクトリ、アプリケーション名の順に選択します。 |
| client_assertion_type |必須 |値は `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` である必要があります |
| client_assertion |必須 | アプリケーションの資格情報として登録した証明書で作成し署名する JSON Web トークンです。 アサーションの形式と証明書の登録方法との詳細については、[証明書資格情報](active-directory-certificate-credentials.md)に関する記事を参照してください。|
| resource |必須 | 受信側のサービスのアプリ ID URI (セキュリティ保護されたリソース)。 Azure portal でアプリ ID URI を調べるには、 **[Active Directory]** を選択し、目的のディレクトリを選びます。 アプリケーション名を選択し、 **[すべての設定]** 、 **[プロパティ]** の順に選択します。 |
| requested_token_use |必須 | 要求の処理方法を指定します。 On-Behalf-Of フローでは、値は **on_behalf_of** である必要があります。 |
| scope |必須 | トークン要求のスコープのスペース区切りリスト。 OpenID Connect の場合、スコープの **openid** を指定する必要があります。|

これらのパラメーターは、`client_secret parameter` が 2 つのパラメーター (`client_assertion_type` と `client_assertion`) に置き換えられる点を除けば、共有シークレットによる要求とほぼ同じです。

#### <a name="example"></a>例

次の HTTP POST は、証明書を使用して https://graph.windows.net Web API のアクセス トークンを要求します。 `client_id` は、アクセス トークンを要求するサービスを識別します。

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

## <a name="service-to-service-access-token-response"></a>サービス間のアクセス トークン応答

成功応答は、次のパラメーターを含む JSON OAuth 2.0 応答です。

| パラメーター | 説明 |
| --- | --- |
| token_type |トークン タイプ値を指定します。 Azure AD でサポートされるのは **Bearer**タイプのみです。 ベアラー トークンの詳細については、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。 |
| scope |トークンで付与されるアクセスのスコープ。 |
| expires_in |アクセス トークンが有効な時間の長さ (秒単位)。 |
| expires_on |アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。 |
| resource |受信側のサービスのアプリ ID URI (セキュリティ保護されたリソース)。 |
| access_token |要求されたアクセス トークン。 呼び出し元のサービスは、このトークンを使用して受信側のサービスへの認証を行うことができます。 |
| id_token |要求された ID トークン。 呼び出し元のサービスは、このトークンを使用してユーザー ID を確認し、そのユーザーとのセッションを開始することができます。 |
| refresh_token |要求されたアクセス トークンの更新トークン。 呼び出し元のサービスは、現在のアクセス トークンの期限が切れた後に、このトークンを使用して別のアクセス トークンを要求できます。 |

### <a name="success-response-example"></a>成功応答の例

次の例に、 https://graph.windows.net Web API へのアクセス トークン要求に対する成功応答を示します。

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

### <a name="error-response-example"></a>エラー応答の例

Azure AD トークン エンドポイントは、条件付きアクセス ポリシー (多要素認証など) で設定されたダウンストリーム API のアクセス トークンを取得しようとすると、エラー応答を返します。 クライアント アプリケーションが条件付きアクセス ポリシーを満たすためのユーザー操作を提供できるように、中間層サービスはこのエラーをクライアント アプリケーションに示す必要があります。

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

## <a name="use-the-access-token-to-access-the-secured-resource"></a>アクセス トークンを使用して、セキュリティ保護されたリソースにアクセスする

中間層サービスは上で取得されたアクセス トークンを使用し、そのトークンを `Authorization` ヘッダー内に設定して、ダウンストリーム Web API に認証済み要求を発行できます。

### <a name="example"></a>例

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="saml-assertions-obtained-with-an-oauth20-obo-flow"></a>OAuth2.0 OBO フローにより取得した SAML アサーション

一部の OAuth ベースの Web サービスは、非対話型フローで SAML アサーションを受け入れるその他の Web サービス API にアクセスする必要があります。 Azure Active Directory は、SAML ベースの Web サービスをターゲット リソースとして使用する On-Behalf-Of フローに応答して SAML アサーションを提供できます。

>[!NOTE]
>これは、OAuth2 ベースのアプリケーションが SAML トークンを使用する Web サービス API エンドポイントにアクセスできる、OAuth 2.0 の On-Behalf-Of フローの非標準の拡張機能です。

> [!TIP]
> フロントエンド Web アプリケーションから SAML で保護された Web サービスを呼び出す場合、API を呼び出すだけで、ユーザーの既存のセッションで通常の対話型認証フローを開始できます。 サービス間の呼び出しでユーザー コンテキストを提供するために SAML トークンが必要なときのみ OBO フローを使用する必要があります。

### <a name="obtain-a-saml-token-by-using-an-obo-request-with-a-shared-secret"></a>共有シークレットを持つ OBO 要求を使用して SAML トークンを取得する

サービス間の SAML アサーション要求には、次のパラメーターが含まれています。

| パラメーター |  | 説明 |
| --- | --- | --- |
| grant_type |必須 | トークン要求の種類。 JWT を使用する要求の場合、値は **urn:ietf:params:oauth:grant-type:jwt-bearer** である必要があります。 |
| assertion |必須 | 要求で使用されるアクセス トークンの値。|
| client_id |必須 | Azure AD での登録時に呼び出し元のサービスに割り当てられるアプリ ID。 Azure portal でアプリ ID を調べるには、 **[Active Directory]** 、目的のディレクトリ、アプリケーション名の順に選択します。 |
| client_secret |必須 | 呼び出し元のサービスに対して Azure AD に登録されているキー。 この値は登録時にメモしているはずです。 |
| resource |必須 | 受信側のサービスのアプリ ID URI (セキュリティ保護されたリソース)。 これは SAML トークンの対象となるリソースです。 Azure portal でアプリ ID URI を調べるには、 **[Active Directory]** を選択し、目的のディレクトリを選びます。 アプリケーション名を選択し、 **[すべての設定]** 、 **[プロパティ]** の順に選択します。 |
| requested_token_use |必須 | 要求の処理方法を指定します。 On-Behalf-Of フローでは、値は **on_behalf_of** である必要があります。 |
| requested_token_type | 必須 | 要求するトークンの種類を指定します。 アクセス先のリソースの要件に応じて、値は **urn:ietf:params:oauth:token-type:saml2** または **urn:ietf:params:oauth:token-type:saml1** のいずれかです。 |

応答には、UTF8 および Base64url でエンコードされた SAML トークンが含まれています。

- **OBO 呼び出しから提供される SAML アサーションの SubjectConfirmationData**:ターゲット アプリケーションで **SubjectConfirmationData** の受信者の値が必要な場合、その値はリソース アプリケーション構成内の非ワイルドカードの応答 URL である必要があります。
- **SubjectConfirmationData ノード**:このノードは SAML 応答の一部ではないため、**InResponseTo** 属性を含めることはできません。 SAML トークンを受け取るアプリケーションは、**InResponseTo** 属性なしで SAML アサーションを受け入れることができる必要があります。

- **同意**:OAuth フローでユーザー データを含む SAML トークンを受信するためには、同意が付与されている必要があります。 アクセス許可および管理者の同意を得る方法については、「[Azure Active Directory v1.0 エンドポイントでのアクセス許可と同意](https://docs.microsoft.com/azure/active-directory/develop/v1-permissions-and-consent)」を参照してください。

### <a name="response-with-saml-assertion"></a>SAML アサーションの応答

| パラメーター | 説明 |
| --- | --- |
| token_type |トークン タイプ値を指定します。 Azure AD でサポートされるのは **Bearer**タイプのみです。 ベアラー トークンの詳細については、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。 |
| scope |トークンで付与されるアクセスのスコープ。 |
| expires_in |アクセス トークンが有効な時間の長さ (秒単位)。 |
| expires_on |アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。 |
| resource |受信側のサービスのアプリ ID URI (セキュリティ保護されたリソース)。 |
| access_token |SAML アサーションを返すパラメーター。 |
| refresh_token |更新トークン。 呼び出し元のサービスは、現在の SAML アサーションの期限が切れた後に、このトークンを使用して別のアクセス トークンを要求できます。 |

- token_type:Bearer
- expires_in:3296
- ext_expires_in:0
- expires_on:1529627844
- resource: `https://api.contoso.com`
- access_token:\<SAML assertion\>
- issued_token_type: urn:ietf:params:oauth:token-type:saml2
- refresh_token:\<更新トークン\>

## <a name="client-limitations"></a>クライアントの制限事項

ワイルドカード応答 URL を持つパブリック クライアントは、OBO フローで `id_token` を使用することはできません。 ただし、パブリック クライアントが登録済みのワイルドカード リダイレクト URI を持っている場合でも、機密クライアントは引き続き、暗黙的な付与フローを通じて取得した**アクセス** トークンを利用することができます。

## <a name="next-steps"></a>次の手順

OAuth 2.0 プロトコルと、クライアント資格情報を使用したサービス間認証を実行する別の方法について詳しく学びます。

* [OAuth 2.0 クライアント資格情報付与を使用した Azure AD のサービス間の認証](v1-oauth2-client-creds-grant-flow.md)
* [Azure AD での OAuth 2.0](v1-protocols-oauth-code.md)
