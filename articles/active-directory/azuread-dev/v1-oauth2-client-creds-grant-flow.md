---
title: OAuth2.0 を使用した Azure AD のサービス間の認証 | Microsoft Docs
description: この記事では、HTTP メッセージを使用して、OAuth2.0 クライアント資格情報付与フローを使用するサービス間の認証を実装する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 977dfea28c5c0dc3f34ada0c138556d70c979e04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85551706"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>クライアント資格情報を使用したサービス間の呼び出し (共有シークレットまたは証明書)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth 2.0 クライアント資格情報付与フローでは、Web サービス ("*Confidential クライアント*") が別の Web サービスを呼び出すときに、ユーザーを偽装する代わりに、独自の資格情報を使用して認証することができます。 このシナリオでは、クライアントは通常、中間層の Web サービス、デーモン サービス、または Web サイトです。 高いレベルの保証では、Azure AD により、呼び出し元サービスが、資格情報として (共有シークレットではなく) 証明書を使用することもできます。

## <a name="client-credentials-grant-flow-diagram"></a>クライアント資格情報付与フローの図
次の図に、Azure Active Directory (Azure AD) でのクライアント資格情報付与フローのしくみを示します。

![OAuth2.0 Client Credentials Grant Flow](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. クライアント アプリケーションが Azure AD トークン発行エンドポイントに対して認証処理を行い、アクセス トークンを要求します。
2. Azure AD トークン発行エンドポイントがアクセス トークンを発行します。
3. アクセス トークンを使用して、セキュリティで保護されたリソースに対する認証処理が行われます。
4. セキュリティで保護されたリソースからクライアント アプリケーションにデータが返されます。

## <a name="register-the-services-in-azure-ad"></a>Azure AD にサービスを登録する
Azure Active Directory (Azure AD) に呼び出し元のサービスと受信側のサービスの両方を登録します。 詳しい説明については、「[Azure Active Directory とアプリケーションの統合](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)」を参照してください。

## <a name="request-an-access-token"></a>アクセス トークンを要求する
アクセス トークンを要求するには、テナントに固有の Azure AD エンドポイントへの HTTP POST を使用します。

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>サービス間のアクセス トークン要求
クライアント アプリケーションのセキュリティ保護に共有シークレットまたは証明書のどちらを使うかに応じて、2 つのケースがあります。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>最初のケース:共有シークレットを使ったアクセス トークン要求
共有シークレットを使用する場合、サービス間のアクセス トークン要求には、次のパラメーターが含まれてます。

| パラメーター | Type | 説明 |
| --- | --- | --- |
| grant_type |required |要求されている付与の種類を指定します。 クライアント資格情報付与フローでは、値は **client_credentials** である必要があります。 |
| client_id |required |呼び出し元の Web サービスの Azure AD クライアント ID を指定します。 呼び出し元アプリケーションのクライアント ID を調べるには、[Azure Portal](https://portal.azure.com) で **[Azure Active Directory]** をクリックし、 **[アプリの登録]** をクリックしてからアプリケーションをクリックします。 client_id は "*アプリケーション ID*" です |
| client_secret |required |呼び出し元の Web サービスまたは デーモン アプリケーションに対して Azure AD に登録されているキーを入力します。 キーを作成するには、Azure Portal で **[Azure Active Directory]** をクリックし、 **[アプリの登録]** をクリックしてからアプリケーションをクリックします。次に、 **[設定]** 、 **[キー]** の順にクリックして、キーを追加します。  このシークレットは、それを提供するときに URL エンコードします。 |
| resource |required |受信側の Web サービスのアプリケーション ID URI を入力します。 アプリケーション ID URI を調べるには、Azure Portal で **[Azure Active Directory]** 、 **[アプリの登録]** の順にクリックして、サービス アプリケーションをクリックします。次に、 **[設定]** 、 **[プロパティ]** の順にクリックします |

#### <a name="example"></a>例
次の HTTP POST では、`https://service.contoso.com/` Web サービス用の[アクセス トークン](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)を要求します。 `client_id` は、アクセス トークンを要求する Web サービスを識別します。

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>2 番目のケース:証明書を使ったアクセス トークン要求
証明書を含むサービス間のアクセス トークン要求には、次のパラメーターが含まれています。

| パラメーター | Type | 説明 |
| --- | --- | --- |
| grant_type |required |要求された応答のタイプを指定します。 クライアント資格情報付与フローでは、値は **client_credentials** である必要があります。 |
| client_id |required |呼び出し元の Web サービスの Azure AD クライアント ID を指定します。 呼び出し元アプリケーションのクライアント ID を調べるには、[Azure Portal](https://portal.azure.com) で **[Azure Active Directory]** をクリックし、 **[アプリの登録]** をクリックしてからアプリケーションをクリックします。 client_id は "*アプリケーション ID*" です |
| client_assertion_type |required |値は `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` である必要があります |
| client_assertion |required | 作成する必要があるアサーション (JSON Web トークン) です。このアサーションは、アプリケーションの資格情報として登録した証明書で署名する必要があります。 証明書の登録方法とアサーションの形式の詳細については、[証明書資格情報](../develop/active-directory-certificate-credentials.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)に関する記事を参照してください。|
| resource | required |受信側の Web サービスのアプリケーション ID URI を入力します。 アプリケーション ID URI を調べるには、Azure Portal で **[Azure Active Directory]** 、 **[アプリの登録]** の順にクリックして、サービス アプリケーションをクリックします。次に、 **[設定]** 、 **[プロパティ]** の順にクリックします |

パラメーターは、共有シークレットによる要求のパラメーターとほぼ同じであることに注意してください。唯一異なるのは、client_secret パラメーターが、client_assertion_type と client_assertion の 2 つのパラメーターに置き換えられている点です。

#### <a name="example"></a>例
次の HTTP POST は、証明書を使用して `https://service.contoso.com/` Web サービスのアクセス トークンを要求します。 `client_id` は、アクセス トークンを要求する Web サービスを識別します。

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>サービス間のアクセス トークン応答

成功応答には、JSON OAuth 2.0 応答と共に次のパラメーターが含まれています。

| パラメーター | 説明 |
| --- | --- |
| access_token |要求されたアクセス トークン。 呼び出し元の Web サービスは、このトークンを使用して受信側の Web サービスに対する認証処理を行うことができます。 |
| token_type |トークン タイプ値を指定します。 Azure AD でサポートされるのは **Bearer** タイプのみです。 ベアラー トークンの詳細については、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。 |
| expires_in |アクセス トークンの有効期間 (秒)。 |
| expires_on |アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。 |
| not_before |アクセス トークンが使用可能になる日時。 日時は 1970-01-01T0:0:0Z UTC から、トークンの有効期間が切れるまでの秒数として表されます。|
| resource |受信側の Web サービスのアプリケーション ID URI。 |

#### <a name="example-of-response"></a>応答の例
次の例に、Web サービスへのアクセス トークン要求に対する成功応答を示します。

```
{
"access_token":"eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```
## <a name="use-the-access-token-to-access-the-secured-resource"></a>アクセス トークンを使用して、セキュリティ保護されたリソースにアクセスする

サービスは上で取得されたアクセス トークンを使用し、そのトークンを `Authorization` ヘッダー内に設定して、ダウンストリーム Web API に認証済み要求を発行できます。

### <a name="example"></a>例

```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiO ... 0X2tnSQLEANnSPHY0gKcgw
```

## <a name="see-also"></a>関連項目
* [Azure AD での OAuth 2.0](v1-protocols-oauth-code.md)
* [共有シークレットによるサービス間呼び出しの C# のサンプル](https://github.com/Azure-Samples/active-directory-dotnet-daemon)と[証明書によるサービス間呼び出しの C# のサンプル](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
