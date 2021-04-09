---
title: Azure App Configuration REST API - ヘッダー
description: Azure App Configuration REST API で使用されるヘッダーのリファレンス ページ
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932576"
---
# <a name="headers"></a>ヘッダー

この記事では、Azure App Configuration REST API で使用されるヘッダーのリファレンス ページへのリンクを示します。

## <a name="request-headers"></a>要求ヘッダー

次の表では、Azure App Configuration で使用される一般的な要求ヘッダーについて説明します。

| ヘッダー | 説明 | 例 |
|--|--|--|
| **承認** | サービスへの要求を[認証](./rest-api-authentication-index.md)するために使用されます。 詳しくは[セクション 14.8](https://tools.ietf.org/html/rfc2616#section-14.8) を参照してください | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **受容** | クライアントで受け入れられる HTTP 応答のメディアの種類をサーバーに通知します。 詳しくは[セクション 14.1](https://tools.ietf.org/html/rfc2616#section-14.1) を参照してください | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Accept-Datetime** | サーバーに対して、そのコンテンツを以前の状態の表現として返すように要求します。 このヘッダーの値は、その状態の要求された日時です。 詳しくは [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) を参照してください | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Content-Type** | HTTP 要求本文内のコンテンツのメディアの種類が格納されています。 詳しくは[セクション 14.17](https://tools.ietf.org/html/rfc2616#section-14.17) を参照してください | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Date** | HTTP 要求が発行された日時。 このヘッダーは [HMAC 認証](./rest-api-authentication-hmac.md)で使用されます。 詳しくは[セクション 14.18](https://tools.ietf.org/html/rfc2616#section-14.18) を参照してください | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Host** | 要求が発行されたテナントを指定します。 このヘッダーは [HMAC 認証](./rest-api-authentication-hmac.md)で使用されます。 詳しくは[セクション 14.23](https://tools.ietf.org/html/rfc2616#section-14.23) を参照してください | `Host: contoso.azconfig.io` |
| **If-Match** | HTTP 要求の条件を設定するために使用されます。 この要求は、対象となるリソースの ETag がこのヘッダーの値と一致する場合にのみ成功します。 '*' 値はすべての ETag と一致します。 詳しくは[セクション 14.24](https://tools.ietf.org/html/rfc2616#section-14.24) を参照してください | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | HTTP 要求の条件を設定するために使用されます。 この要求は、対象となるリソースの ETag がこのヘッダーの値と一致しない場合にのみ成功します。 '*' 値はすべての ETag と一致します。 詳しくは[セクション 14.26](https://tools.ietf.org/html/rfc2616#section-14.26) を参照してください | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sync-Token** | 一連の要求中にリアルタイムの整合性を実現するために使用されます。 | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-ms-client-request-id** | 要求のラウンドトリップを追跡するために使用される、クライアントによって提供される一意の ID。 | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-ms-content-sha256** | HTTP 要求本文の sha256 ダイジェスト。 このヘッダーは [HMAC 認証](./rest-api-authentication-hmac.md)で使用されます。 | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-ms-date** | 日付ヘッダーにアクセスできない場合は、このヘッダーを `Date` ヘッダーの代わりに設定して使用することができます。 このヘッダーは [HMAC 認証](./rest-api-authentication-hmac.md)で使用されます。 | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-ms-return-client-request-id** | `x-ms-client-request-id` ヘッダーと共に使用します。 このヘッダーの値が 'true' の場合、サーバーは `x-ms-client-request-id` 要求ヘッダーの値を返すように指示されます。 | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>レスポンス ヘッダー

このサーバーでは、次の HTTP ヘッダーを応答に含めることができます。

| ヘッダー | 説明 | 例 |
|--|--|--|
| **Content-Type** | HTTP 応答本文内のコンテンツのメディアの種類が格納されています。 詳しくは[セクション 14.17](https://tools.ietf.org/html/rfc2616#section-14.17) を参照してください | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | 指定されたリソースの状態を表す不透明トークン。 条件付きの操作で使用できます。 詳しくは[セクション 14.19](https://tools.ietf.org/html/rfc2616#section-14.19) を参照してください | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Last-Modified** | 要求されたリソースが最後に変更された日時を示します。 [HTTP-Date](https://tools.ietf.org/html/rfc2616#section-3.3.1) としてフォーマットされています。 詳しくは[セクション 14.29](https://tools.ietf.org/html/rfc2616#section-14.29) を参照してください | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **リンク** | 応答に関連するリソースへのリンクを提供します。 このヘッダーは _next_ リンクを使用することでページングに使用されます。 詳しくは [RFC 5988](https://tools.ietf.org/html/rfc5988) を参照してください | `Link: </kv?after={token}>; rel="next"` |
| **Memento-Datetime** | 応答に含まれるコンテンツが前の状態を表すことを示します。 このヘッダーの値は、その状態の日時です。 詳しくは [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) を参照してください | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **retry-after-ms** | 失敗した要求を再試行する前にクライアントが待機する推奨時間 (ミリ秒単位) を提供します。 | `retry-after-ms: 10` |
| **x-ms-request-id** | サービス内で要求を追跡するために使用される、サーバーによって生成される一意の ID。 | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW-Authenticate** | 認証のためにクライアントにチャレンジし、認証の試行が失敗した理由を示すために使用されます。 詳しくは[セクション 14.47](https://tools.ietf.org/html/rfc2616#section-14.47) を参照してください | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
