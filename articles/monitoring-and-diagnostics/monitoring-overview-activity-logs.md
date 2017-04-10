---
title: "Azure アクティビティ ログの概要 | Microsoft Docs"
description: "Azure アクティビティ ログの概要と、Azure アクティビティ ログを使用して Azure サブスクリプション内で発生するイベントを理解する方法について説明します。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 4ec026a5b95170d0eba244123c37cd3c8fab150a
ms.lasthandoff: 03/09/2017


---
# <a name="overview-of-the-azure-activity-log"></a>Azure アクティビティ ログの概要
**Azure アクティビティ ログ** では、サブスクリプションのリソースに対して実行された操作に関する洞察が得られます。 アクティビティ ログではサブスクリプションのコントロール プレーン イベントが報告されるため、以前は "監査ログ" または "操作ログ" と呼ばれていました。 アクティビティ ログを使用すると、サブスクリプションのリソースに対して発生する書き込み操作 (PUT、POST、DELETE) すべてについて、"いつ誰が何を" 行ったのかを確認できます。 さらに、操作の状態など、重要性の大きなプロパティを確認することもできます。 アクティビティ ログには、読み取り (GET) 操作や、クラシック/"RDFE" モデルを使用するリソースに対する操作は含まれません。

![アクティビティ ログとその他の種類のログ ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

図 1: アクティビティ ログとその他の種類のログ

アクティビティ ログは[診断ログ](monitoring-overview-of-diagnostic-logs.md)とは異なります。 アクティビティ ログは、外部から行われるリソースの操作に関するデータを提供します。 診断ログは、リソースによって出力され、そのリソースの操作に関する情報を提供します。

Azure Portal、CLI、PowerShell コマンドレット、Azure Monitor REST API を使用して、アクティビティ ログからイベントを取得できます。


> [!WARNING]
> Azure アクティビティ ログは、主に Azure Resource Manager で発生したアクティビティを記録します。 クラシック/RDFE モデルを使用しているリソースは追跡されません。 一部のクラシック リソース タイプでは、Azure Resource Manager にプロキシ リソース プロバイダー (例: Microsoft.ClassicCompute) が存在します。 これらのプロキシ リソース プロバイダーを使用して Azure Resource Manager 経由でクラシック リソース タイプを操作すると、その操作がアクティビティ ログに表示されます。 クラシック ポータルまたは Azure Resource Manager プロキシの外部でクラシック リソース タイプを操作すると、そのアクションは操作ログにのみ記録されます。 この操作ログには、クラシック ポータルでのみアクセスできます。
>
>

アクティビティ ログの概要については、次のビデオを参照してください。
[!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]

## <a name="what-you-can-do-with-the-activity-log"></a>アクティビティ ログで実行できること
アクティビティ ログでは次のことを実行できます。

![Azure アクティビティ ログ](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* [アクティビティ ログ イベントをトリガーする電子メール アラートまたは Webhook アラートを作成する。](insights-auditlog-to-webhook-email.md)
* サード パーティのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、アクティビティ ログを [**Event Hubs** にストリーミング](monitoring-stream-activity-logs-event-hubs.md)する。
* [**PowerBI コンテンツ パック**](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)を使用して、アクティビティ ログを PowerBI で分析する。
* [アーカイブや手動での検査に使用するためにアクティビティ ログを**ストレージ アカウント**に保存する。](monitoring-archive-activity-log.md) **ログ プロファイル**を使用して、リテンション期間 (日数) を指定できます。
* **Azure ポータル**でアクティビティ ログを照会して表示する。
* PowerShell コマンドレット、CLI、または REST API を使用してアクティビティ ログを照会する。


ログを出力するサブスクリプションとは別のサブスクリプションで、ストレージ アカウントまたはイベント ハブ名前空間を使用できます。 設定を構成するユーザーは、両方のサブスクリプションに対して適切な RBAC アクセスを持っている必要があります。

## <a name="export-the-activity-log-with-log-profiles"></a>ログ プロファイルを使用したアクティビティ ログのエクスポート
**ログ プロファイル** は、アクティビティ ログをエクスポートする方法を制御します。 ログ プロファイルを使用して、以下を構成できます。

* アクティビティ ログの送信先 (ストレージ アカウントまたは Event Hubs)
* 送信するイベント カテゴリ (Write、Delete、Action)。 "*ログ プロファイルでの "カテゴリ" の意味は、アクティビティ ログ イベントでの意味とは異なります。ログ プロファイルでの "カテゴリ" は、操作の種類 (Write、Delete、Action) を指します。アクティビティ ログ イベントでの "category" プロパティは、イベントのソースまたは種類 (Administration、ServiceHealth、Alert など) を指します。*"
* エクスポートするリージョン (場所)
* アクティビティ ログをストレージ アカウントに保持する期間。
    - リテンション期間が&0; 日の場合、ログは永続的に保持されます。 リテンション期間が 0 日の場合、ログは永続的に保持されます。
    - リテンション期間ポリシーが設定されていても、ストレージ アカウントへのログの保存が無効になっている場合 (たとえば、Event Hubs または OMS オプションだけが選択されている場合)、保持ポリシーは無効になります。
    - 保持ポリシーは日単位で適用されるため、その日の終わり (UTC) に、保持ポリシーの期間を超えることになるログは削除されます。 たとえば、保持ポリシーが&1; 日の場合、その日が始まった時点で、一昨日のログは削除されます。

ここに挙げた設定は、ポータルの [アクティビティ ログ] ブレードの [エクスポート] オプションで構成できます。 さらに、[Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell コマンドレット、または CLI を使えば、プログラムを使って構成することもできます。 1 つのサブスクリプションで使用できるログ プロファイルは&1; つだけです。

### <a name="configure-log-profiles-using-the-azure-portal"></a>Azure ポータルを使用したログ プロファイルの構成
Azure ポータルの [エクスポート] オプションを使用して、アクティビティ ログを Event Hubs にストリーミングしたり、ストレージ アカウントに保存したりできます。

1. ポータルの左側のメニューを使用して、 **[アクティビティ ログ]** ブレードに移動します。

    ![ポータルの [アクティビティ ログ] に移動](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. ブレードの上部にある **[エクスポート]** ボタンをクリックします。

    ![ポータルの [エクスポート] ボタン](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. ブレードが表示されたら、以下を選択できます。  

   * イベントをエクスポートするリージョン
   * イベントの保存先となるストレージ アカウント
   * ストレージでイベントを保持する日数。 日数を 0 にした場合には、ログが永久に保持されます。
   * イベントのストリーミング用にイベント ハブを作成する Service Bus 名前空間。

     ![[Export Activity Log (アクティビティ ログのエクスポート)] ブレード](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. **[保存]** をクリックして設定を保存します。 設定はサブスクリプションにすぐに適用されます。

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Azure PowerShell コマンドレットを使用したログ プロファイルの構成
#### <a name="get-existing-log-profile"></a>既存のログ プロファイルの取得
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>ログ プロファイルの追加
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| プロパティ | 必須 | Description |
| --- | --- | --- |
| 名前 |はい |ログ プロファイルの名前。 |
| StorageAccountId |なし |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
| serviceBusRuleId |なし |Event Hubs を作成する Service Bus 名前空間の Service Bus 規則 ID。 これは、`{service bus resource ID}/authorizationrules/{key name}` の形式の文字列です。 |
| 場所 |はい |アクティビティ ログ イベントを収集するリージョンのコンマ区切りリスト。 |
| RetentionInDays |はい |イベントを保持する日数。1 ～2,147,483,647 の範囲。 値が&0; の場合、ログは無期限に (いつまでも) 保存されます。 |
| カテゴリ |なし |収集するイベント カテゴリのコンマ区切りリスト。 指定できる値は、Write、Delete、Action です。 |

#### <a name="remove-a-log-profile"></a>ログ プロファイルの削除
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Azure クロスプラットフォーム CLI を使用したログ プロファイルの構成
#### <a name="get-existing-log-profile"></a>既存のログ プロファイルの取得
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
`name` プロパティには、ログ プロファイルの名前を指定する必要があります。

#### <a name="add-a-log-profile"></a>ログ プロファイルの追加
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| プロパティ | 必須 | Description |
| --- | --- | --- |
| name |はい |ログ プロファイルの名前。 |
| storageId |なし |アクティビティ ログの保存先となるストレージ アカウントのリソース ID。 |
| serviceBusRuleId |なし |Event Hubs を作成する Service Bus 名前空間の Service Bus 規則 ID。 これは、`{service bus resource ID}/authorizationrules/{key name}` の形式の文字列です。 |
| 場所 |はい |アクティビティ ログ イベントを収集するリージョンのコンマ区切りリスト。 |
| RetentionInDays |はい |イベントを保持する日数。1 ～2,147,483,647 の範囲。 値が&0; の場合、ログは無期限に (いつまでも) 保存されます。 |
| categories |なし |収集するイベント カテゴリのコンマ区切りリスト。 指定できる値は、Write、Delete、Action です。 |

#### <a name="remove-a-log-profile"></a>ログ プロファイルの削除
```
azure insights logprofile delete --name my_log_profile
```

## <a name="event-schema"></a>イベント スキーマ
アクティビティ ログ内の各イベントには、以下の例によく似た JSON BLOB があります。

```
{
  "value": [ {
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
    "eventSource": {
      "value": "Microsoft.Resources",
      "localizedValue": "Microsoft Resources"
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
  } ],
"nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```

| 要素名 | Description |
| --- | --- |
| authorization |イベントの RBAC プロパティの BLOB。 通常は、"action"、"role"、"scope" の各プロパティが含まれます。 |
| caller |操作、UPN 要求、または可用性に基づく SPN 要求を実行したユーザーの電子メール アドレス。 |
| channels |値として、"Admin" または "Operation" を指定します。 |
| correlationId |通常は文字列形式の GUID。 correlationId を共有するイベントは、同じ uber アクションに属します。 |
| Description |イベントを説明する静的テキスト。 |
| eventDataId |イベントの一意の識別子。 |
| eventSource |このイベントを生成した Azure サービスまたはインフラストラクチャの名前。 |
| httpRequest |Http 要求を記述する BLOB。 通常、"clientRequestId"、"clientIpAddress"、"method" (HTTP メソッド、 たとえば PUT) が含まれます。 |
| 最小限のレベル |イベントのレベル。 次の値のいずれか: “Critical”、“Error”、“Warning”、“Informational” and “Verbose” |
| resourceGroupName |影響を受けるリソースのリソース グループの名前。 |
| resourceProviderName |影響を受けるリソースのリソース プロバイダーの名前。 |
| resourceUri |影響を受けるリソースのリソース ID。 |
| operationId |単一の操作に対応する複数のイベント間で共有される GUID。 |
| operationName |操作の名前。 |
| プロパティ |イベントの詳細を示す `<Key, Value>` ペアのセット (辞書)。 |
| status |操作の状態を説明する文字列。 一般的な値は、Started、In Progress、Succeeded、Failed、Active、Resolved です。 |
| subStatus |通常は対応する REST 呼び出しの HTTP 状態コードですが、副状態を示す他の文字列が含まれる場合もあります。たとえば、OK (HTTP Status Code: 200)、Created (HTTP Status Code: 201)、Accepted (HTTP Status Code: 202)、No Content (HTTP Status Code: 204)、Bad Request (HTTP Status Code: 400)、Not Found (HTTP Status Code: 404)、Conflict (HTTP Status Code: 409)、Internal Server Error (HTTP Status Code: 500)、Service Unavailable (HTTP Status Code: 503)、Gateway Timeout (HTTP Status Code: 504) などの一般的な値が含まれる場合があります。 |
| eventTimestamp |イベントに対応する要求を処理する Azure サービスによって、イベントが生成されたときのタイムスタンプ。 |
| submissionTimestamp |イベントがクエリで使用できるようになったときのタイムスタンプ。 |
| subscriptionId |Azure サブスクリプション ID。 |
| nextLink |結果セットが複数の応答に分けられているときに、次の結果セットを取得するための継続トークン。 通常、レコードの件数が 200 件を超える場合に必要です。 |

## <a name="next-steps"></a>次のステップ
* [アクティビティ ログ (以前の監査ログ) の詳細を確認する](../azure-resource-manager/resource-group-audit.md)
* [Azure アクティビティ ログを Event Hubs にストリーミングする](monitoring-stream-activity-logs-event-hubs.md)

