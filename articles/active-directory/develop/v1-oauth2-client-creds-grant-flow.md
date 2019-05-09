---
title: OAuth2.0 を使用した Azure AD のサービス間の認証 | Microsoft Docs
description: この記事では、HTTP メッセージを使用して、OAuth2.0 クライアント資格情報付与フローを使用するサービス間の認証を実装する方法について説明します。
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74c79dbfb397a8e6d87de75b5468414f2b7adf2b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191590"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>クライアント資格情報を使用したサービス間の呼び出し (共有シークレットまたは証明書)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

OAuth 2.0 クライアント資格情報付与フローでは、Web サービス ("*Confidential クライアント*") が別の Web サービスを呼び出すときに、ユーザーを偽装する代わりに、独自の資格情報を使用して認証することができます。 このシナリオでは、クライアントは通常、中間層の Web サービス、デーモン サービス、または Web サイトです。 高いレベルの保証では、Azure AD により、呼び出し元サービスが、資格情報として (共有シークレットではなく) 証明書を使用することもできます。

## <a name="client-credentials-grant-flow-diagram"></a>クライアント資格情報付与フローの図
次の図に、Azure Active Directory (Azure AD) でのクライアント資格情報付与フローのしくみを示します。

![OAuth2.0 Client Credentials Grant Flow](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. クライアント アプリケーションが Azure AD トークン発行エンドポイントに対して認証処理を行い、アクセス トークンを要求します。
2. Azure AD トークン発行エンドポイントがアクセス トークンを発行します。
3. アクセス トークンを使用して、セキュリティで保護されたリソースに対する認証処理が行われます。
4. セキュリティで保護されたリソースからクライアント アプリケーションにデータが返されます。

## <a name="register-the-services-in-azure-ad"></a>Azure AD にサービスを登録する
Azure Active Directory (Azure AD) に呼び出し元のサービスと受信側のサービスの両方を登録します。 詳しい説明については、「[Azure Active Directory とアプリケーションの統合](quickstart-v1-integrate-apps-with-azure-ad.md)」を参照してください。

## <a name="request-an-access-token"></a>アクセス トークンを要求する
アクセス トークンを要求するには、テナントに固有の Azure AD エンドポイントへの HTTP POST を使用します。

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>サービス間のアクセス トークン要求
クライアント アプリケーションのセキュリティ保護に共有シークレットまたは証明書のどちらを使うかに応じて、2 つのケースがあります。

### <a name="first-case-access-token-request-with-a-shared-secret"></a>最初のケース:共有シークレットを使ったアクセス トークン要求
共有シークレットを使用する場合、サービス間のアクセス トークン要求には、次のパラメーターが含まれてます。

| パラメーター |  | 説明 |
| --- | --- | --- |
| grant_type |必須 |要求されている付与の種類を指定します。 クライアント資格情報付与フローでは、値は **client_credentials** である必要があります。 |
| client_id |必須 |呼び出し元の Web サービスの Azure AD クライアント ID を指定します。 呼び出し元アプリケーションのクライアント ID を調べるには、[Azure Portal](https://portal.azure.com) で **[Azure Active Directory]** をクリックし、**[アプリの登録]** をクリックしてからアプリケーションをクリックします。 client_id は "*アプリケーション ID*" です |
| client_secret |必須 |呼び出し元の Web サービスまたは デーモン アプリケーションに対して Azure AD に登録されているキーを入力します。 キーを作成するには、Azure Portal で **[Azure Active Directory]** をクリックし、**[アプリの登録]** をクリックしてからアプリケーションをクリックします。次に、**[設定]**、**[キー]** の順にクリックして、キーを追加します。  このシークレットは、それを提供するときに URL エンコードします。 |
| resource |必須 |受信側の Web サービスのアプリケーション ID URI を入力します。 アプリケーション ID URI を調べるには、Azure Portal で **[Azure Active Directory]**、**[アプリの登録]** の順にクリックして、サービス アプリケーションをクリックします。次に、**[設定]**、**[プロパティ]** の順にクリックします |

#### <a name="example"></a>例
次の HTTP POST では、 https://service.contoso.com/ Web サービス用の[アクセス トークン](access-tokens.md)を要求します。 `client_id` は、アクセス トークンを要求する Web サービスを識別します。

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>2 番目のケース:証明書を使ったアクセス トークン要求
証明書を含むサービス間のアクセス トークン要求には、次のパラメーターが含まれています。

| パラメーター |  | 説明 |
| --- | --- | --- |
| grant_type |必須 |要求された応答のタイプを指定します。 クライアント資格情報付与フローでは、値は **client_credentials** である必要があります。 |
| client_id |必須 |呼び出し元の Web サービスの Azure AD クライアント ID を指定します。 呼び出し元アプリケーションのクライアント ID を調べるには、[Azure Portal](https://portal.azure.com) で **[Azure Active Directory]** をクリックし、**[アプリの登録]** をクリックしてからアプリケーションをクリックします。 client_id は "*アプリケーション ID*" です |
| client_assertion_type |必須 |値は `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` である必要があります |
| client_assertion |必須 | 作成する必要があるアサーション (JSON Web トークン) です。このアサーションは、アプリケーションの資格情報として登録した証明書で署名する必要があります。 証明書の登録方法とアサーションの形式の詳細については、[証明書資格情報](active-directory-certificate-credentials.md)に関する記事を参照してください。|
| resource | 必須 |受信側の Web サービスのアプリケーション ID URI を入力します。 アプリケーション ID URI を調べるには、Azure Portal で **[Azure Active Directory]**、**[アプリの登録]** の順にクリックして、サービス アプリケーションをクリックします。次に、**[設定]**、**[プロパティ]** の順にクリックします |

パラメーターは、共有シークレットによる要求のパラメーターとほぼ同じであることに注意してください。唯一異なるのは、client_secret パラメーターが、client_assertion_type と client_assertion の 2 つのパラメーターに置き換えられている点です。

#### <a name="example"></a>例
次の HTTP POST は、証明書を使用して https://service.contoso.com/ Web サービスのアクセス トークンを要求します。 `client_id` は、アクセス トークンを要求する Web サービスを識別します。

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
| token_type |トークン タイプ値を指定します。 Azure AD でサポートされるのは **Bearer**タイプのみです。 ベアラー トークンの詳細については、「[OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)」(OAuth 2.0 承認フレームワーク: ベアラー トークンの使用法 (RFC 6750)) をご覧ください。 |
| expires_in |アクセス トークンの有効期間 (秒)。 |
| expires_on |アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。 |
| not_before |アクセス トークンが使用可能になる日時。 日時は 1970-01-01T0:0:0Z UTC から、トークンの有効期間が切れるまでの秒数として表されます。|
| resource |受信側の Web サービスのアプリケーション ID URI。 |

#### <a name="example-of-response"></a>応答の例
次の例に、Web サービスへのアクセス トークン要求に対する成功応答を示します。

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>関連項目
* [Azure AD での OAuth 2.0](v1-protocols-oauth-code.md)
* [共有シークレットによるサービス間呼び出しの C# のサンプル](https://github.com/Azure-Samples/active-directory-dotnet-daemon)と[証明書によるサービス間呼び出しの C# のサンプル](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
