---
title: Azure アクティビティ ログのイベント スキーマ
description: アクティビティ ログに出力されるデータのイベント スキーマを理解する
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 1/16/2019
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 93e74eb6aefbaeeddf7c4f15d62f4a9ee3d617d4
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622214"
---
# <a name="azure-activity-log-event-schema"></a>Azure アクティビティ ログのイベント スキーマ
**Azure アクティビティ ログ**は、Azure で発生したあらゆるサブスクリプションレベルのイベントの分析に利用できるログです。 この記事では、データのカテゴリごとにイベント スキーマを説明します。 データのスキーマは、ポータル、PowerShell、CLI、または直接 REST API 経由でデータを読み取る場合と、[ログ プロファイルを使用してストレージまたは Event Hubs にデータをストリーミングする場合](./../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile)で異なります。 次の例は、ポータル、PowerShell、CLI、および REST API 経由で利用可能なスキーマを示します。 これらのプロパティの [Azure 診断ログ スキーマ](./diagnostic-logs-schema.md)へのマッピングについては、この記事の最後で紹介します。

## <a name="administrative"></a>管理
このカテゴリには、Resource Manager で実行されるすべての作成、更新、削除、アクション操作のレコードが含まれています。 このカテゴリで表示されるイベントの種類として、"仮想マシンの作成"、"ネットワーク セキュリティ グループの削除" などがあります。ユーザーまたはアプリケーションが Resource Manager を使用して実行するすべてのアクションは、特定のリソースの種類に対する操作としてモデリングされます。 操作の種類が書き込み、削除、またはアクションの場合、その操作の開始のレコードと成功または失敗のレコードは、いずれも管理カテゴリに記録されます。 管理カテゴリには、サブスクリプション内のロールベースのアクセス制御に対する任意の変更も含まれています。

### <a name="sample-event"></a>サンプル イベント
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | 説明 |
| --- | --- |
| authorization |イベントの RBAC プロパティの BLOB。 通常は、"action"、"role"、"scope" の各プロパティが含まれます。 |
| caller |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの電子メール アドレス。 |
| channels |次のいずれかの値です。"Admin"、"Operation" |
| claims |Resource Manager でこの操作を実行するユーザーまたはアプリケーションを認証するために Active Directory によって使用される JWT トークン。 |
| correlationId |通常は文字列形式の GUID。 correlationId を共有するイベントは、同じ uber アクションに属します。 |
| description |イベントを説明する静的テキスト。 |
| eventDataId |イベントの一意の識別子。 |
| eventName | 管理イベントのフレンドリ名。 |
| category | 常に "Administrative" |
| httpRequest |Http 要求を記述する BLOB。 通常、"clientRequestId"、"clientIpAddress"、"method" (HTTP メソッド、 たとえば PUT) が含まれます。 |
| level |イベントのレベル。 次のいずれかの値です。"Critical"、"Error"、"Warning"、および "Informational" |
| resourceGroupName |影響を受けるリソースのリソース グループの名前。 |
| resourceProviderName |影響を受けるリソースのリソース プロバイダーの名前。 |
| resourceType | 管理イベントによって影響を受けたリソースの種類。 |
| resourceId |影響を受けるリソースのリソース ID。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| properties |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |
| status |操作の状態を説明する文字列。 いくつかの一般的な値は次のとおりです: Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus |通常は対応する REST 呼び出しの HTTP 状態コードですが、サブステータスを示す他の文字列 (次のような一般的な値) が含まれる場合もあります。OK (HTTP 状態コード: 200)、作成済み (HTTP 状態コード:201)、受理 (HTTP 状態コード:202)、コンテンツなし (HTTP 状態コード:204)、無効な要求 (HTTP 状態コード:400)、見つかりません (HTTP 状態コード:404)、競合 (HTTP 状態コード:409)、内部サーバー エラー (HTTP 状態コード:500)、Service Unavailable (HTTP 状態コード: 503)、Gateway Timeout (HTTP 状態コード: 504)。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

## <a name="service-health"></a>サービス正常性
このカテゴリには、Azure で発生した任意のサービス正常性インシデントのレコードが含まれます。 このカテゴリで表示されるイベントの種類として、"SQL Azure in East US is experiencing downtime" (米国東部の SQL Azure でダウンタイムが発生しています) などがあります。 サービス正常性イベントには、要対応、支援復旧、インシデント、メンテナンス、情報、またはセキュリティの 6 種類があり、イベントの影響を受けるリソースがサブスクリプション内にある場合にのみ表示されます。

### <a name="sample-event"></a>サンプル イベント
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
プロパティの値に関するドキュメントについては、[サービスの正常性通知](./../../azure-monitor/platform/service-notifications.md)に関する記事を参照してください。

## <a name="resource-health"></a>リソース ヘルス
このカテゴリには、Azure リソースで発生したすべてのリソース正常性イベントのレコードが含まれます。 このカテゴリに表示されるイベントの種類として、[Virtual Machine health status changed to unavailable]\(仮想マシンの正常性状態が使用不可に変わりました\) などがあります。 リソース正常性イベントでは、次の 4 つのヘルス状態のいずれかを表すことができます。Available、Unavailable、Degraded、および Unknown。 さらに、リソース正常性イベントは、プラットフォーム開始またはユーザー開始のいずれかのカテゴリーに分けることができます。

### <a name="sample-event"></a>サンプル イベント

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription Id>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | 説明 |
| --- | --- |
| channels | 常に "Admin, Operation" |
| correlationId | 文字列形式の GUID。 |
| description |アラート イベントを説明する静的テキスト。 |
| eventDataId |アラート イベントの一意識別子。 |
| category | 常に "ResourceHealth"。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| level |イベントのレベル。 次のいずれかの値です。"Critical"、"Error"、"Warning"、"Informational"、および "Verbose" |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| resourceGroupName |リソースが含まれているリソース グループの名前。 |
| resourceProviderName |常に "Microsoft.Resourcehealth/healthevent/action"。 |
| resourceType | リソース正常性イベントによって影響を受けたリソースの種類。 |
| resourceId | 影響を受けたリソースのリソース ID の名前。 |
| status |正常性イベントの状態を説明する文字列。 可能な値は次のとおりです。Active、Resolved、InProgress、Updated。 |
| subStatus | アラートの場合、通常は null です。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |
| properties |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。|
| properties.title | リソースの正常性状態を説明するユーザー フレンドリな文字列。 |
| properties.details | イベントの詳細を説明するユーザー フレンドリな文字列。 |
| properties.currentHealthStatus | リソースの現在の正常性状態。 次のいずれかの値です。"Available"、"Unavailable"、"Degraded"、および "Unknown"。 |
| properties.previousHealthStatus | リソースの前回の正常性状態。 次のいずれかの値です。"Available"、"Unavailable"、"Degraded"、および "Unknown"。 |
| properties.type | リソース正常性イベントの種類の説明。 |
| properties.cause | リソース正常性イベントの原因の説明。 "UserInitiated" または "PlatformInitiated" のいずれか。 |


## <a name="alert"></a>アラート:
このカテゴリには、Azure アラートの全アクティビティのレコードが含まれています。 このカテゴリで表示されるイベントの種類として、"CPU % on myVM has been over 80 for the past 5 minutes" (過去 5 分間の myVM の CPU % が 80 を超えました) などがあります。 多様な Azure システムにアラートの概念があります。また、何らかのルールを定義し、条件がそのルールと一致するときに通知を受け取ることができます。 サポートされる Azure のアラートの種類が "アクティブになる" たびに、または通知を生成する条件を満たすたびに、アクティブ化のレコードもこのカテゴリのアクティビティ ログにプッシュされます。

### <a name="sample-event"></a>サンプル イベント

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | 説明 |
| --- | --- |
| caller | 常に Microsoft.Insights/alertRules |
| channels | 常に "Admin, Operation" |
| claims | アラート エンジンの SPN (サービス プリンシパル名) またはリソースの種類の JSON BLOB。 |
| correlationId | 文字列形式の GUID。 |
| description |アラート イベントを説明する静的テキスト。 |
| eventDataId |アラート イベントの一意識別子。 |
| category | 常に "Alert" |
| level |イベントのレベル。 次のいずれかの値です。"Critical"、"Error"、"Warning"、および "Informational" |
| resourceGroupName |メトリック アラートである場合に影響を受けるリソースのリソース グループの名前。 他の種類のアラートの場合は、アラート自体を含むリソース グループの名前です。 |
| resourceProviderName |メトリック アラートである場合に影響を受けるリソースのリソース プロバイダーの名前。 他の種類のアラートの場合は、アラート自体のリソース プロバイダーの名前です。 |
| resourceId | メトリック アラートである場合に影響を受けるリソースのリソース ID の名前。 他の種類のアラートの場合は、アラート リソース自体のリソース ID です。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| properties |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |
| status |操作の状態を説明する文字列。 いくつかの一般的な値は次のとおりです: Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus | アラートの場合、通常は null です。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

### <a name="properties-field-per-alert-type"></a>アラートの種類別のプロパティ フィールド
アラート イベントのソースに応じて、プロパティ フィールドには異なる値が格納されます。 アラート イベントの一般的なプロバイダーは、アクティビティ ログ アラートとメトリック アラートの 2 つです。

#### <a name="properties-for-activity-log-alerts"></a>アクティビティ ログ アラートのプロパティ
| 要素名 | 説明 |
| --- | --- |
| properties.subscriptionId | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのサブスクリプション ID。 |
| properties.eventDataId | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのイベント データ ID。 |
| properties.resourceGroup | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのリソース グループ。 |
| properties.resourceId | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのリソース ID。 |
| properties.eventTimestamp | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのイベント タイムスタンプ。 |
| properties.operationName | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントの操作名。 |
| properties.status | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントの状態。|

#### <a name="properties-for-metric-alerts"></a>メトリック アラートのプロパティ
| 要素名 | 説明 |
| --- | --- |
| properties.RuleUri | メトリック アラート ルール自体のリソース ID。 |
| properties.RuleName | メトリック アラート ルールの名前。 |
| properties.RuleDescription | (アラート ルールで定義された) メトリック アラート ルールの説明。 |
| properties.Threshold | メトリック アラート ルールの評価で使用されるしきい値。 |
| properties.WindowSizeInMinutes | メトリック アラート ルールの評価で使用されるウィンドウ サイズ。 |
| properties.Aggregation | メトリック アラート ルールで定義されている集計の種類。 |
| properties.Operator | メトリック アラート ルールの評価で使用される条件演算子。 |
| properties.MetricName | メトリック アラート ルールの評価で使用されるメトリックのメトリック名。 |
| properties.MetricUnit | メトリック アラート ルールの評価で使用されるメトリックのメトリック単位。 |

## <a name="autoscale"></a>自動スケール
このカテゴリには、サブスクリプションで定義したすべての自動スケール設定に基づいて、自動スケール エンジンの操作に関連するすべてのイベントのレコードが含まれます。 このカテゴリで表示されるイベントの種類として、"Autoscale scale up action failed" (自動スケールのスケールアップ アクションに失敗しました) などがあります。 自動スケールを使用すると、自動スケール設定で指定した時刻や負荷 (メトリック) データに基づいて、サポートされるリソースの種類のインスタンス数を自動的にスケールアウトまたはスケールインすることができます。 スケールアップまたはスケールダウンの条件を満たした場合、開始イベントと、成功または失敗イベントがこのカテゴリに記録されます。

### <a name="sample-event"></a>サンプル イベント
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | 説明 |
| --- | --- |
| caller | 常に Microsoft.Insights/autoscaleSettings |
| channels | 常に "Admin, Operation" |
| claims | 自動スケール エンジンの SPN (サービス プリンシパル名) またはリソースの種類の JSON BLOB。 |
| correlationId | 文字列形式の GUID。 |
| description |自動スケール イベントを説明する静的テキスト。 |
| eventDataId |自動スケール イベントの一意識別子。 |
| level |イベントのレベル。 次のいずれかの値です。"Critical"、"Error"、"Warning"、および "Informational" |
| resourceGroupName |自動スケール設定のリソース グループの名前。 |
| resourceProviderName |自動スケール設定のリソース プロバイダーの名前。 |
| resourceId |自動スケール設定のリソース ID。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| properties |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |
| properties.Description | 自動スケール エンジンが実行していた処理の詳細な説明。 |
| properties.ResourceName | 影響を受けるリソース (スケール アクションが実行されていたリソース) のリソース ID |
| properties.OldInstancesCount | 自動スケール アクションが有効になる前のインスタンスの数。 |
| properties.NewInstancesCount | 自動スケール アクションが有効になった後のインスタンスの数。 |
| properties.LastScaleActionTime | 自動スケール アクションが発生したときのタイムスタンプ。 |
| status |操作の状態を説明する文字列。 いくつかの一般的な値は次のとおりです: Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus | 自動スケールの場合、通常は null です。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

## <a name="security"></a>セキュリティ
このカテゴリには、Azure Security Center によって生成されたアラートのレコードが含まれます。 このカテゴリで表示されるイベントの種類の例としては、"Suspicious double extension file executed" (拡張子が 2 つある不審なファイルが実行されました) などがあります。

### <a name="sample-event"></a>サンプル イベント
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | 説明 |
| --- | --- |
| channels | 常に "Operation" |
| correlationId | 文字列形式の GUID。 |
| description |セキュリティ イベントを説明する静的テキスト。 |
| eventDataId |セキュリティ イベントの一意識別子。 |
| eventName |セキュリティ イベントのフレンドリ名。 |
| category | 常に "Security" |
| id |セキュリティ イベントの一意リソース識別子。 |
| level |イベントのレベル。 次のいずれかの値です。"Critical"、"Error"、"Warning"、または "Informational" |
| resourceGroupName |リソースのリソース グループの名前。 |
| resourceProviderName |Azure Security Center のリソース プロバイダーの名前。 常に "Microsoft.Security"。 |
| resourceType |セキュリティ イベントを生成したリソースの種類 (例: "Microsoft.Security/locations/alerts") |
| resourceId |セキュリティ アラートのリソース ID。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| properties |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 これらのプロパティは、セキュリティ アラートの種類によって異なります。 Security Center から送られてくるアラートの種類について詳しくは、[こちらのページ](../../security-center/security-center-alerts-type.md)をご覧ください。 |
| properties.Severity |重大度のレベル。 可能性のある値は、"High"、"Medium"、"Low" です。 |
| status |操作の状態を説明する文字列。 いくつかの一般的な値は次のとおりです: Started、In Progress、Succeeded、Failed、Active、Resolved。 |
| subStatus | 通常、セキュリティ イベントの場合は null です。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

## <a name="recommendation"></a>推奨
このカテゴリには、サービスに対して生成されている新しい推奨のすべてのレコードが含まれています。 推奨の例として、「フォールト トレランスの改善のための可用性セットの使用」が挙げられます。 生成される可能性がある推奨事項イベントには、次の 4 種類があります。High Availability、Performance、Security、および Cost Optimization。 

### <a name="sample-event"></a>サンプル イベント
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | 説明 |
| --- | --- |
| channels | 常に "Operation" |
| correlationId | 文字列形式の GUID。 |
| description |推奨イベントを説明する静的テキスト |
| eventDataId | 推奨イベントの一意の識別子。 |
| category | 常に "Recommendation" |
| id |推奨イベントの一意のリソース ID。 |
| level |イベントのレベル。 次のいずれかの値です。"Critical"、"Error"、"Warning"、または "Informational" |
| operationName |操作の名前。  常に "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |リソースのリソース グループの名前。 |
| resourceProviderName |この推奨が適用されるリソースのリソース プロバイダーの名前 ("MICROSOFT.COMPUTE" など) |
| resourceType |この推奨が適用されるリソースのリソース タイプの名前 ("MICROSOFT.COMPUTE/virtualmachines" など) |
| resourceId |推奨が適用されるリソースのリソース ID |
| status | 常に "Active" |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |
| properties |推奨の詳細を示す `<Key, Value>` ペアのセット (辞書)。|
| properties.recommendationSchemaVersion| アクティビティ ログ エントリに公開される推奨プロパティのスキーマ バージョン |
| properties.recommendationCategory | 推奨のカテゴリ。 指定できる値は "High Availability"、"Performance"、"Security"、"Cost" です。 |
| properties.recommendationImpact| 推奨の影響。 指定できる値は "High"、"Medium"、"Low" です。 |
| properties.recommendationRisk| 推奨のリスク。 指定できる値は "Error"、"Warning"、"None" です。 |

## <a name="policy"></a>ポリシー

このカテゴリには、[Azure Policy](../../governance/policy/overview.md) によって実行されるすべての効果アクション操作のレコードが含まれます。 このカテゴリで表示されるイベントの種類の例として、_Audit_ と _Deny_ があります。 Policy によって実行されるすべてのアクションは、リソースに対する操作としてモデル化されます。

### <a name="sample-policy-event"></a>サンプルの Policy イベント

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Policy イベントのプロパティの説明

| 要素名 | 説明 |
| --- | --- |
| authorization | イベントの RBAC プロパティの配列。 新しいリソースの場合、これは、評価をトリガーした要求のアクションとスコープです。 既存のリソースの場合、アクションは "Microsoft.Resources/checkPolicyCompliance/read" です。 |
| caller | 新しいリソースの場合は、デプロイを開始した ID。 既存のリソースの場合は、Microsoft Azure Policy Insights RP の GUID。 |
| channels | Policy イベントは "Operation" チャネルのみを使用します。 |
| claims | Resource Manager でこの操作を実行するユーザーまたはアプリケーションを認証するために Active Directory によって使用される JWT トークン。 |
| correlationId | 通常は文字列形式の GUID。 correlationId を共有するイベントは、同じ uber アクションに属します。 |
| description | このフィールドは、Policy イベントの場合は空白です。 |
| eventDataId | イベントの一意の識別子。 |
| eventName | "BeginRequest" または "EndRequest" のいずれか。 "BeginRequest" は、auditIfNotExists と deployIfNotExists の評価が遅延した場合と、deployIfNotExists 効果がテンプレートのデプロイを開始するときに使用されます。 他のすべての操作は "EndRequest" を返します。 |
| category | アクティビティ ログ イベントを "Policy" に属するものとして宣言します。 |
| eventTimestamp | イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| id | 特定のリソースのイベントの一意識別子。 |
| level | イベントのレベル。 Audit は "Warning" を使用し、Deny は "Error" を使用します。 auditIfNotExists または deployIfNotExists エラーは、重大度に応じて "Warning" または"Error" を生成する可能性があります。 他のすべての Policy イベントは "Informational" を使用します。 |
| operationId | 単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName | 操作の名前。Policy 効果に直接関連します。 |
| resourceGroupName | 評価されるリソースのリソース グループの名前。 |
| resourceProviderName | 評価されるリソースのリソース プロバイダーの名前。 |
| resourceType | 新しいリソースの場合は、評価される種類です。 既存のリソースの場合は、"Microsoft.Resources/checkPolicyCompliance" を返します。 |
| resourceId | 評価されるリソースのリソース ID。 |
| status | Policy の評価結果の状態を説明する文字列。 ほとんどの Policy の評価は "Succeeded" を返しますが、Deny 効果は "Failed" を返します。 auditIfNotExists または deployIfNotExists でのエラーも "Failed" を返します。 |
| subStatus | Policy イベントの場合、フィールドは空白です。 |
| submissionTimestamp | イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId | Azure サブスクリプション ID。 |
| properties.isComplianceCheck | 新しいリソースをデプロイされたり、既存のリソースのリソース マネージャーのプロパティが更新されたりしたときに、"False" を返します。 他のすべての[評価トリガー](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)では "True" になります。 |
| properties.resourceLocation | 評価されているリソースの Azure リージョン。 |
| properties.ancestors | 親管理グループのコンマ区切りの一覧は、直接の親から最も遠い先祖の順に並べ替えられます。 |
| properties.policies | この Policy の評価が結果となるポリシー定義、割り当て、効果、およびパラメーターに関する詳細が含まれます。 |
| relatedEvents | このフィールドは、Policy イベントの場合は空白です。 |

## <a name="mapping-to-diagnostic-logs-schema"></a>診断ログのスキーマへのマッピング

Azure アクティビティ ログをストレージ アカウントまたは Event Hubs 名前空間にストリーミングする場合、データは [Azure 診断ログ スキーマ](./diagnostic-logs-schema.md)に従います。 上記のスキーマから診断ログ スキーマへのプロパティのマッピングを次に示します。

| 診断ログ スキーマ プロパティ | アクティビティ ログ REST API スキーマ プロパティ | メモ |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | subscriptionId、resourceType、resourceGroupName は、すべて resourceId から推測されます。 |
| operationName | operationName.value |  |
| category | 操作の名前の一部 | 操作の種類の詳細内訳 - "Write"/"Delete"/"Action" |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | description |  |
| durationMs | 該当なし | 常時 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | 要求と承認プロパティ |  |
| Level | Level |  |
| location | 該当なし | イベントが処理される場所。 *これは、リソースの場所ではなく、イベントが処理される場所です。このプロパティは、今後の更新で削除されます。* |
| Properties | properties.eventProperties |  |
| properties.eventCategory | category | properties.eventCategory が存在しない場合、カテゴリは "Administrative" |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>次の手順
* [アクティビティ ログ (以前の監査ログ) の詳細を確認する](../../azure-monitor/platform/activity-logs-overview.md)
* [Azure アクティビティ ログを Event Hubs にストリーミングする](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)

