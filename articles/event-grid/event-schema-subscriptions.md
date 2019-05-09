---
title: Azure Event Grid のサブスクリプション用のイベント スキーマ
description: Azure Event Grid のサブスクリプション イベントに対して用意されているプロパティについて説明します
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 4994063dfc3bce88489f70969c06bf36b591f907
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536251"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Azure Event Grid のサブスクリプション用のイベント スキーマ

この記事では、Azure サブスクリプション イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

Azure サブスクリプションとリソース グループは、同じ種類のイベントを出力します。 イベントの種類は、リソースの変更またはアクションに関連しています。 主な違いは、リソース グループはリソース グループ内のリソースのイベントを出力し、Azure サブスクリプションはサブスクリプションのリソースのイベントを出力することです。

リソース イベントは、`management.azure.com` に送信される PUT、PATCH、POST、DELETE 操作に対して作成されます。 GET 操作ではイベントは作成されません。 データ プレーンに送信される操作 (`myaccount.blob.core.windows.net` など) は、イベントを作成しません。 アクション イベントでは、リソースのキーの一覧表示のような、操作に対するイベント データが提供されます。

Azure サブスクリプションのイベントをサブスクライブすると、エンドポイントは、そのサブスクリプションのすべてのイベントを受信します。 イベントには、表示するイベント (仮想マシンの更新など) を含めることができますが、重要とは思えないイベント (デプロイ履歴への新しいエントリの書き込みなど) を含めることもできます。 自分のエンドポイントですべてのイベントを受信でき、処理したいイベントを処理するコードを記述できます。 または、イベント サブスクリプションを作成するときにフィルターを設定できます。

プログラムでイベントを処理するには、`operationName` 値を調べることでイベントを並べ替えることができます。 たとえば、イベント エンドポイントで、`Microsoft.Compute/virtualMachines/write` または `Microsoft.Storage/storageAccounts/write` と等しい操作のイベントのみを処理できます。

イベントの対象は、操作の対象となっているリソースのリソース ID です。 リソースのイベントをフィルター処理するには、イベント サブスクリプションを作成するときに、そのリソース ID を指定します。 リソースの種類でフィルター処理するには、次の形式で値を使用します。`/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

サンプル スクリプトとチュートリアルの一覧については、[Azure サブスクリプションのイベント ソース](event-sources.md#azure-subscriptions)に関する記事をご覧ください。

## <a name="available-event-types"></a>使用可能なイベントの種類

Azure サブスクリプションは、VM が作成されたりストレージ アカウントが削除されたりしたときに、Azure Resource Manager から管理イベントを出力します。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | リソースに対するアクションが取り消されたときに発生します。 |
| Microsoft.Resources.ResourceActionFailure | リソースに対するアクションが失敗したときに発生します。 |
| Microsoft.Resources.ResourceActionSuccess | リソースに対するアクションが成功したときに発生します。 |
| Microsoft.Resources.ResourceDeleteCancel | 削除操作が取り消されたときに発生します。 このイベントは、テンプレートのデプロイが取り消された場合に発生します。 |
| Microsoft.Resources.ResourceDeleteFailure | 削除操作が失敗したときに発生します。 |
| Microsoft.Resources.ResourceDeleteSuccess | 削除操作が成功したときに発生します。 |
| Microsoft.Resources.ResourceWriteCancel | 作成または更新操作が取り消されたときに発生します。 |
| Microsoft.Resources.ResourceWriteFailure | 作成または更新操作が失敗したときに発生します。 |
| Microsoft.Resources.ResourceWriteSuccess | 作成または更新操作が成功したときに発生します。 |

## <a name="example-event"></a>イベントの例

次の例は、**ResourceWriteSuccess** イベント用のスキーマを示しています。 **ResourceWriteFailure** イベントと **ResourceWriteCancel** イベントでも、`eventType` の値を変更して、同じスキーマが使用されます。

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

次の例は、**ResourceDeleteSuccess** イベントのスキーマを示しています。 **ResourceDeleteFailure** イベントと **ResourceDeleteCancel** イベントでも、`eventType` の値を変更して、同じスキーマが使用されます。

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

次の例は、**ResourceActionSuccess** イベント用のスキーマを示しています。 **ResourceActionFailure** イベントと **ResourceActionCancel** イベントでも、`eventType` の値を変更して、同じスキーマが使用されます。

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}" 
}]
```

## <a name="event-properties"></a>イベントのプロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| topic | 文字列 | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | 文字列 | 発行元が定義したイベントの対象のパス。 |
| eventType | 文字列 | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | 文字列 | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | 文字列 | イベントの一意識別子。 |
| data | オブジェクト | サブスクリプション イベントのデータ。 |
| dataVersion | 文字列 | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | 文字列 | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| authorization | オブジェクト | 操作の要求された承認。 |
| claims | オブジェクト | 要求のプロパティ。 詳細については、[JWT 認証](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)に関する記事を参照してください。 |
| correlationId | 文字列 | トラブルシューティング用の操作 ID。 |
| httpRequest | オブジェクト | 操作の詳細。 このオブジェクトは、既存のリソースを更新する場合、またはリソースを削除する場合にのみ含まれます。 |
| resourceProvider | 文字列 | 操作に対するリソース プロバイダー。 |
| resourceUri | 文字列 | 操作内のリソースの URI。 |
| operationName | 文字列 | 実行された操作。 |
| status | 文字列 | 操作の状態。 |
| subscriptionId | 文字列 | リソースのサブスクリプション ID。 |
| tenantId | 文字列 | リソースのテナント ID。 |

## <a name="next-steps"></a>次の手順

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
