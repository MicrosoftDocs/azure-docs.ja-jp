---
title: "Azure Monitor を使用して API Management を監視する | Microsoft Docs"
description: "Azure Monitor を使用して API Management を監視する方法について説明します。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 717e033aa4bbd4dd8ebcc727c3f551aee81770dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-api-management-with-azure-monitor"></a>Azure Monitor を使用して API Management を監視する
Azure Monitor は、すべての Azure リソースを監視するための単一ソースを提供する Azure サービスです。 Azure Monitor を使用すると、API Management などの Azure リソースのメトリックとログを視覚化、クエリ、ルーティング、アーカイブし、そのメトリックとログに対してアクションを実行できます。 

次のビデオは、Azure Monitor を使用して API Management を監視する方法を示しています。 Azure Monitor の詳細については、「[Azure Monitor の使用]」を参照してください。 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>メトリック
現時点では、API Management は、5 つのメトリックを出力していますが、今後追加される予定です。 これらのメトリックは 5 分間隔で出力され、API の状態と正常性をほぼリアルタイムで把握できます。 メトリックの概要を次に示します。
* ゲートウェイ要求の合計: 期間中の API 要求の数。 
* 成功したゲートウェイ要求: 304、307 および 301 よりも小さな値 (200 など) を含む成功した HTTP 応答コードを受信した API 要求の数。 
* 失敗したゲートウェイ要求: 400 および 500 よりも大きな値を含む失敗した HTTP 応答コードを受信した API 要求の数。
* 未承認ゲートウェイ要求: 401、403、および 429 を含む HTTP 応答コードを受信した API 要求の数。 
* その他のゲートウェイ要求: 上記のカテゴリのいずれにも属していない HTTP 応答コード (たとえば 418) を受信した API 要求の数。

API Management サービスでメトリックにアクセスするか、Azure Monitor ですべての Azure リソースにアクセスできます。 API Management サービスでメトリックを表示するには:
1. Azure Portal を開きます。
2. API Management サービスに移動します。
3. **[メトリック]** をクリックします。

![[メトリック] ブレード][metrics-blade]

メトリックの使用方法の詳細については、「[メトリックの概要]」を参照してください。

## <a name="activity-logs"></a>アクティビティ ログ
アクティビティ ログは、API Management サービスで実行された操作に関する情報を提供します。 以前は "監査ログ" や "操作ログ" と呼ばれていました。 アクティビティ ログを使用すると、API Management サービスで発生した書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。 

> [!NOTE]
> アクティビティ ログには、読み取り (GET) 操作、従来のパブリッシャー ポータルで実行された操作、または元の管理 API の使用に関する情報は含まれません。

アクティビティ ログには API Management サービスでアクセスするか、Azure Monitor ですべての Azure リソースのログにアクセスできます。 API Management サービスでアクティビティ ログを表示するには:
1. Azure Portal を開きます。
2. API Management サービスに移動します。
3. **[アクティビティ ログ]** をクリックします。

![[アクティビティ ログ] ブレード][activity-logs-blade]

メトリックの使用方法の詳細については、「[アクティビティ ログの概要]」を参照してください。

## <a name="alerts"></a>Alerts
メトリックとアクティビティ ログに基づいてアラートを受け取るように設定できます。 Azure Monitor では、アラートがトリガーされたときに次の処理を実行するように構成することができます。

* 電子メール通知を送信する
* Webhook を呼び出す
* Azure Logic App を呼び出す

アラート ルールは、API Management サービスまたは Azure Monitor 内に構成できます。 API Management 内に構成するには: 
1. Azure Portal を開きます。
2. API Management サービスに移動します。
3. **[アラート ルール]** をクリックします。

![[アラート ルール] ブレード][alert-rules-blade]

アラートの使用に関する詳細については、[アラートの概要]に関するページを参照してください。

## <a name="diagnostic-logs"></a>診断ログ
診断ログは、監査とトラブルシューティングを行うために重要な、操作とエラーについての豊富な情報を提供します。 診断ログは、アクティビティ ログとは異なります。 アクティビティ ログは、API リソースで実行された操作に関する情報を提供します。 診断ログでは、リソース自体が実行した操作を調査できます。

現時点では、API Management は、個々の API 要求についての診断ログ (1 時間ごとにバッチ処理) を、次の構造を持つエントリで提供します。

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

診断ログには API Management サービスでアクセスするか、Azure Monitor ですべての Azure リソースのログにアクセスできます。 API Management サービスで診断ログを表示するには:
1. Azure Portal を開きます。
2. API Management サービスに移動します。
3. **[診断ログ]** をクリックします。

![[診断ログ] ブレード][diagnostic-logs-blade]

メトリックの使用方法の詳細については、[診断ログの概要]に関するページを参照してください。

## <a name="next-step"></a>次のステップ

* [Azure Monitor の使用]
* [メトリックの概要]
* [アクティビティ ログの概要]
* [診断ログの概要]
* [アラートの概要]

[Azure Monitor の使用]: ../monitoring-and-diagnostics/monitoring-get-started.md
[メトリックの概要]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[アクティビティ ログの概要]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[診断ログの概要]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[アラートの概要]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png
