---
title: "Azure アクティビティ ログのイベント スキーマ | Microsoft Docs"
description: "アクティビティ ログに出力されるデータのイベント スキーマを理解する"
author: johnkemnetz
manager: robb
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 5c96a1cfa56d1535549cb15d5a7bcf03bd11e723
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---
# <a name="azure-activity-log-event-schema"></a>Azure アクティビティ ログのイベント スキーマ
**Azure アクティビティ ログ**は、Azure で発生したあらゆるサブスクリプションレベルのイベントの分析に利用できるログです。 この記事では、データのカテゴリごとにイベントのスキーマを説明します。

## <a name="administrative"></a>管理
このカテゴリには、Resource Manager で実行されるすべての作成、更新、削除、およびアクション操作のレコードが含まれています。 このカテゴリで表示されるイベントの種類として、"仮想マシンの作成"、"ネットワーク セキュリティ グループの削除" などがあります。ユーザーまたはアプリケーションが Resource Manager を使用して実行するすべてのアクションは、特定のリソースの種類に対する操作としてモデリングされます。 操作の種類が書き込み、削除、またはアクションの場合、その操作の開始のレコードと成功または失敗のレコードは、いずれも管理カテゴリに記録されます。 管理カテゴリには、サブスクリプション内のロールベースのアクセス制御に対する任意の変更も含まれています。

### <a name="sample-event"></a>サンプル イベント
```json
{
  "authorization": {
    "action": "microsoft.support/supporttickets/write",
    "role": "Subscription Admin",
    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841"
  },
  "caller": "admin@contoso.com",
  "channels": "Operation",
  "claims": {
    "aud": "https://management.core.windows.net/",
    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
    "iat": "1421876371",
    "nbf": "1421876371",
    "exp": "1421880271",
    "ver": "1.0",
    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
    "puid": "20030000801A118C",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
    "name": "John Smith",
    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
    "appidacr": "2",
    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
    "http://schemas.microsoft.com/claims/authnclassreference": "1"
  },
  "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "description": "",
  "eventDataId": "44ade6b4-3813-45e6-ae27-7420a95fa2f8",
  "eventName": {
    "value": "EndRequest",
    "localizedValue": "End request"
  },
  "httpRequest": {
    "clientRequestId": "27003b25-91d3-418f-8eb1-29e537dcb249",
    "clientIpAddress": "192.168.35.115",
    "method": "PUT"
  },
  "id": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
  "level": "Informational",
  "resourceGroupName": "MSSupportGroup",
  "resourceProviderName": {
    "value": "microsoft.support",
    "localizedValue": "microsoft.support"
  },
  "resourceUri": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
  "operationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
  "operationName": {
    "value": "microsoft.support/supporttickets/write",
    "localizedValue": "microsoft.support/supporttickets/write"
  },
  "properties": {
    "statusCode": "Created"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": "Created",
    "localizedValue": "Created (HTTP Status Code: 201)"
  },
  "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
  "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
  "subscriptionId": "s1"
}
```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | Description |
| --- | --- |
| authorization |イベントの RBAC プロパティの BLOB。 通常は、"action"、"role"、"scope" の各プロパティが含まれます。 |
| caller |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの電子メール アドレス。 |
| channels |値として、"Admin" または "Operation" を指定します。 |
| claims |リソース マネージャーでこの操作を実行するユーザーまたはアプリケーションを認証するために Active Directory によって使用される JWT トークン。 |
| correlationId |通常は文字列形式の GUID。 correlationId を共有するイベントは、同じ uber アクションに属します。 |
| Description |イベントを説明する静的テキスト。 |
| eventDataId |イベントの一意の識別子。 |
| httpRequest |Http 要求を記述する BLOB。 通常、"clientRequestId"、"clientIpAddress"、"method" (HTTP メソッド、 たとえば PUT) が含まれます。 |
| 最小限のレベル |イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose” |
| resourceGroupName |影響を受けるリソースのリソース グループの名前。 |
| resourceProviderName |影響を受けるリソースのリソース プロバイダーの名前。 |
| resourceId |影響を受けるリソースのリソース ID。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| プロパティ |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |
| status |操作の状態を説明する文字列。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus |通常は対応する REST 呼び出しの HTTP 状態コードですが、副状態を示す他の文字列が含まれる場合もあります。たとえば、OK (HTTP Status Code: 200)、Created (HTTP Status Code: 201)、Accepted (HTTP Status Code: 202)、No Content (HTTP Status Code: 204)、Bad Request (HTTP Status Code: 400)、Not Found (HTTP Status Code: 404)、Conflict (HTTP Status Code: 409)、Internal Server Error (HTTP Status Code: 500)、Service Unavailable (HTTP Status Code: 503)、Gateway Timeout (HTTP Status Code: 504) などの一般的な値が含まれる場合があります。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

## <a name="service-health"></a>サービス正常性
このカテゴリには、Azure で発生した任意のサービス正常性インシデントのレコードが含まれます。 このカテゴリで表示されるイベントの種類として、"SQL Azure in East US is experiencing downtime" (米国東部の SQL Azure でダウンタイムが発生しています) などがあります。 サービス正常性イベントには、要対応、支援復旧、インシデント、メンテナンス、情報、またはセキュリティという 6 種類があります。イベントの影響を受けるリソースがサブスクリプションにある場合、1 つのイベントのみが表示されます。

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
  "id": "/subscriptions/mySubscriptionID/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
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
  "resourceId": "/subscriptions/mySubscriptionID",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "mySubscriptionID",
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

### <a name="property-descriptions"></a>プロパティの説明
要素名 | Description
-------- | -----------
channels | 値は "Admin" または "Operation" のいずれか
correlationId | 通常は文字列形式の GUID。 そのイベントは同じ uber アクションに属し、通常は同じ correlationID を共有します。
description | イベントの説明。
eventDataId | イベントの一意の識別子。
eventName | イベントのタイトル。
最小限のレベル | イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose”
resourceProviderName | 影響を受けるリソースのリソース プロバイダーの名前。 不明な場合、これは null になります。
resourceType| 影響を受けるリソースの種類。 不明な場合、これは null になります。
subStatus | サービス正常性イベントの場合、通常は null です。
eventTimestamp | ログ イベントが生成され、アクティビティ ログに送信されたときのタイムスタンプ。
submissionTimestamp |   イベントがアクティビティ ログで使用できるようになったときのタイムスタンプ。
subscriptionId | このイベントが記録された Azure サブスクリプション。
status | 操作の状態を説明する文字列。 一般的な値の例: Active、Resolved。
operationName | 操作の名前。 通常は Microsoft.ServiceHealth/incident/action です。
カテゴリ | "ServiceHealth"
resourceId | 既知の場合、影響を受けるリソースのリソース ID。 それ以外の場合は、サブスクリプション ID が提供されます。
Properties.title | この通信のローカライズされたタイトル。 既定の言語は英語です。
Properties.communication | HTML マークアップによる通信のローカライズされた詳細。 既定は英語です。
Properties.incidentType | 使用可能な値: AssistedRecovery、ActionRequired、Information、Incident、Maintenance、Security
Properties.trackingId | このイベントに関連付けられているインシデントを識別します。 インシデントに関連するイベントを関連付けるために使用します。
Properties.impactedServices | インシデントの影響を受けるサービスやリージョンが記述されるエスケープされた JSON blob。 それぞれ ServiceName が指定された Services の一覧、およびそれぞれ RegionName が指定された ImpactedRegions の一覧です。
Properties.defaultLanguageTitle | 英語で行われる通信
Properties.defaultLanguageContent | HTML マークアップまたはプレーン テキストとして英語で行われる通信
Properties.stage | AssistedRecovery、ActionRequired、Information、Incident、Security で使用可能な値: Active、Resolved。 Maintenance で使用可能な値: Active、Planned、InProgress、Canceled、Rescheduled、Resolved、Complete
Properties.communicationId | このイベントが関連付けられている通信。

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
  "correlationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
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
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
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
  "subscriptionId": "mySubscriptionID"
}
```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | Description |
| --- | --- |
| caller | 常に Microsoft.Insights/alertRules |
| channels | 常に “Admin, Operation” |
| claims | アラート エンジンの SPN (サービス プリンシパル名)、またはリソースの種類の JSON BLOB。 |
| correlationId | 文字列形式の GUID。 |
| description |アラート イベントを説明する静的テキスト。 |
| eventDataId |アラート イベントの一意識別子。 |
| 最小限のレベル |イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose” |
| resourceGroupName |メトリックのアラートである場合に影響を受けるリソースのリソース グループの名前。 その他のアラートの場合、これはアラート自体を含むリソース グループの名前です。 |
| resourceProviderName |メトリックのアラートである場合に影響を受けるリソースのリソース プロバイダーの名前。 その他のアラートの場合、これはアラート自体のリソース プロバイダーの名前です。 |
| resourceId | メトリックのアラートである場合に影響を受けるリソースのリソース ID の名前。 その他のアラートの場合、これはアラート リソース自体のリソース ID です。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| プロパティ |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |
| status |操作の状態を説明する文字列。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus | アラートの場合、通常は null です。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

### <a name="properties-field-per-alert-type"></a>アラートの種類別のプロパティ フィールド
アラート イベントのソースに応じて、プロパティ フィールドには異なる値が格納されます。 アラート イベントの一般的なプロバイダーは、アクティビティ ログ アラートとメトリック アラートの 2 つです。

#### <a name="properties-for-activity-log-alerts"></a>アクティビティ ログ アラートのプロパティ
| 要素名 | Description |
| --- | --- |
| properties.subscriptionId | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントからのサブスクリプション ID。 |
| properties.eventDataId | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントからのイベント データ ID。 |
| properties.resourceGroup | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントからのリソース グループ。 |
| properties.resourceId | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントからのリソース ID。 |
| properties.eventTimestamp | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントのイベント タイムスタンプ。 |
| properties.operationName | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントからの操作名。 |
| properties.status | このアクティビティ ログ アラート ルールがアクティブになった原因であるアクティビティ ログ イベントからの状態。|

#### <a name="properties-for-metric-alerts"></a>メトリック アラートのプロパティ
| 要素名 | Description |
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

## <a name="autoscale"></a>Autoscale
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
  "description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
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
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/mySubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
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
  "subscriptionId": "mySubscriptionID"
}

```

### <a name="property-descriptions"></a>プロパティの説明
| 要素名 | Description |
| --- | --- |
| caller | 常に Microsoft.Insights/autoscaleSettings |
| channels | 常に “Admin, Operation” |
| claims | 自動スケール エンジンの SPN (サービス プリンシパル名)、またはリソースの種類の JSON BLOB。 |
| correlationId | 文字列形式の GUID。 |
| description |自動スケール イベントを説明する静的テキスト。 |
| eventDataId |自動スケール イベントの一意識別子。 |
| 最小限のレベル |イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose” |
| resourceGroupName |自動スケール設定のリソース グループの名前。 |
| resourceProviderName |自動スケール設定のリソース プロバイダーの名前。 |
| resourceId |自動スケール設定のリソース ID。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| プロパティ |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |
| properties.Description | 自動スケール エンジンが実行していた処理の詳細な説明。 |
| properties.ResourceName | 影響を受けるリソース (スケール アクションが実行されていたリソース) のリソース ID |
| properties.OldInstancesCount | 自動スケール アクションが有効になる前のインスタンスの数。 |
| properties.NewInstancesCount | 自動スケール アクションが有効になった後のインスタンスの数。 |
| properties.LastScaleActionTime | 自動スケール アクションが発生したときのタイムスタンプ。 |
| status |操作の状態を説明する文字列。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus | 自動スケールの場合、通常は null です。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |

## <a name="next-steps"></a>次のステップ
* [アクティビティ ログ (以前の監査ログ) の詳細を確認する](monitoring-overview-activity-logs.md)
* [Azure アクティビティ ログを Event Hubs にストリーミングする](monitoring-stream-activity-logs-event-hubs.md)

