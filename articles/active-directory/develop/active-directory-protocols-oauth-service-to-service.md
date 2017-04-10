---
title: "OAuth2.0 を使用した Azure AD のサービス間の認証 | Microsoft Docs"
description: "この記事では、HTTP メッセージを使用して、OAuth2.0 クライアント資格情報付与フローを使用するサービス間の認証を実装する方法について説明します。"
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: 9904ec79782b1790ecb21f31b078fea9936c8380
ms.openlocfilehash: c5164ad1f0fef17a86295db5e4caee21a9a19d33


---
# <a name="service-to-service-calls-using-client-credentials"></a>クライアント資格情報を使用したサービス間の呼び出し
OAuth 2.0 クライアント資格情報付与フローでは、Web サービス (" **機密性の高いクライアント**") が別の Web サービスを呼び出すときに、ユーザーを偽装する代わりに、独自の資格情報を使用して認証することが許可されます。 このシナリオでは、クライアントは通常、中間層の Web サービス、デーモン サービス、または Web サイトです。

## <a name="client-credentials-grant-flow-diagram"></a>クライアント資格情報付与フローの図
次の図に、Azure Active Directory (Azure AD) でのクライアント資格情報付与フローのしくみを示します。

![OAuth2.0 Client Credentials Grant Flow](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. クライアント アプリケーションが Azure AD トークン発行エンドポイントに対して認証処理を行い、アクセス トークンを要求します。
2. Azure AD トークン発行エンドポイントがアクセス トークンを発行します。
3. アクセス トークンを使用して、セキュリティで保護されたリソースに対する認証処理が行われます。
4. セキュリティで保護されたリソースから Web アプリケーションにデータが返されます。

## <a name="register-the-services-in-azure-ad"></a>Azure AD にサービスを登録する
Azure Active Directory (Azure AD) に呼び出し元のサービスと受信側のサービスの両方を登録します。 詳しい説明については、「[Azure Active Directory とアプリケーションの統合](active-directory-integrating-applications.md)」を参照してください。

## <a name="request-an-access-token"></a>アクセス トークンを要求する
アクセス トークンを要求するには、テナントに固有の Azure AD エンドポイントへの HTTP POST を使用します。

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>サービス間のアクセス トークン要求
サービス間のアクセス トークン要求には、次のパラメーターが含まれています。

| パラメーター |  | 説明 |
| --- | --- | --- |
| response_type |必須 |要求された応答のタイプを指定します。 クライアント資格情報付与フローでは、値は **client_credentials** である必要があります。 |
| client_id |必須 |呼び出し元の Web サービスの Azure AD クライアント ID を指定します。 呼び出し元アプリケーションのクライアント ID を調べるには、Azure 管理ポータルで、**[Active Directory]** をクリックし、目的のディレクトリとアプリケーションを順にクリックして、**[構成]** をクリックします。 |
| client_secret |必須 |呼び出し元の Web サービスに対して Azure AD に登録されているキーを入力します。 キーを作成するには、Azure 管理ポータルで、**[Active Directory]** をクリックし、目的のディレクトリとアプリケーションを順にクリックして、**[構成]** をクリックします。 |
| resource |必須 |受信側の Web サービスのアプリケーション ID URI を入力します。 アプリケーション ID URI を調べるには、Azure 管理ポータルで、**[Active Directory]** をクリックし、目的のディレクトリとアプリケーションを順にクリックして、**[構成]** をクリックします。 |

## <a name="example"></a>例
次の HTTP POST は、https://service.contoso.com/ Web サービスのアクセス トークンを要求します。 `client_id` は、アクセス トークンを要求する Web サービスを識別します。

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>サービス間のアクセス トークン応答
成功応答には、JSON OAuth 2.0 応答と共に次のパラメーターが含まれています。

| パラメーター | Description |
| --- | --- |
| access_token |要求されたアクセス トークン。 呼び出し元の Web サービスは、このトークンを使用して受信側の Web サービスに対する認証処理を行うことができます。 |
| access_type |トークン タイプ値を指定します。 Azure AD でサポートされるのは **Bearer**タイプのみです。 ベアラー トークンの詳細については、「 [OAuth2.0 Authorization Framework: Bearer Token Usage (OAuth2.0 承認フレームワーク: ベアラー トークンの使用について)](http://www.rfc-editor.org/rfc/rfc6750.txt)」(RFC 6750) を参照してください。 |
| expires_in |アクセス トークンの有効期間 (秒)。 |
| expires_on |アクセス トークンの有効期限が切れる日時。 日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 この値は、キャッシュされたトークンの有効期間を調べるために使用されます。 |
| resource |受信側の Web サービスのアプリケーション ID URI。 |

## <a name="example"></a>例
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
* [Azure AD での OAuth 2.0](active-directory-protocols-oauth-code.md)


<!--HONumber=Feb17_HO2-->


